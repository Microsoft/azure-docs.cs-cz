---
title: Propojte doménu s decentralizovaným identifikátorem () (Preview) – Azure Active Directory ověřitelných přihlašovacích údajů.
description: Přečtěte si, jak vytvořit službu DNS BIND?
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 90ea52b0ed5ee2d8e36caab18491eecd6e1295fd
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222811"
---
# <a name="link-your-domain-to-your-decentralized-identifier-did"></a>Propojte doménu s decentralizovaným identifikátorem ().

> [!IMPORTANT]
> Azure Active Directory ověřitelných přihlašovacích údajů je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

V tomto článku:
> [!div class="checklist"]
> * Proč musíme propojit naši službu s naší doménou?
> * Jak propojím DIDs a doménami?
> * Jak proces propojuje doménu funguje?
> * Jak funguje logika ověřování/neověřené domény?

## <a name="prerequisites"></a>Požadavky

Pokud chcete propojit vaši doménu, musíte provést následující:

- Dokončete [Začínáme](get-started-verifiable-credentials.md) a další [sadu kurzů](enable-your-tenant-verifiable-credentials.md).

## <a name="why-do-we-need-to-link-our-did-to-our-domain"></a>Proč musíme propojit naši službu s naší doménou?

A byla zahájena jako identifikátor, který není ukotven k existujícím systémům. To je užitečné, protože uživatel nebo organizace ho můžou vlastnit a řídit. Pokud entita, která interakci s organizací, neví, že patří do, pak to není tak užitečné.

Propojení s doménou, která je v doméně, řeší počáteční problém důvěryhodnosti tím, že umožňuje libovolné entitě kryptograficky ověřit vztah mezi objektem a doménou.

## <a name="how-do-we-link-dids-and-domains"></a>Jak propojím DIDs a doménami?

Vytvoříme propojení mezi doménou a z toho, že implementujeme Open Standard napsaný decentralizovaným rozhraním Identity Foundation označovaným jako [dobře známá konfigurace](https://identity.foundation/.well-known/resources/did-configuration/). Služba ověřitelných přihlašovacích údajů ve službě Azure Active Directory (Azure AD) pomáhá vaší organizaci vytvořit propojení mezi doménou a doménou tím, že zahrnovala informace o doméně, které jste zadali v nějakém z nich, a vygenerovat známý konfigurační soubor:

1. Azure AD používá k zápisu koncového bodu služby v dokumentu, který zadáte během nastavení organizace, informace o doméně. Všem stranám, které komunikují s vaším, se může zobrazit doména, ve které jste provedli vlastní deklarace identity.  

    ```json
        "service": [
          {
            "id": "#linkeddomains",
            "type": "LinkedDomains",
            "serviceEndpoint": {
              "origins": [
                "https://www.contoso.com/"
              ]
            }
          }
    ```

2. Služba ověřitelných přihlašovacích údajů ve službě Azure AD vygeneruje kompatibilní prostředek konfigurace splňující předpisy, který můžete hostovat ve vaší doméně. Konfigurační soubor obsahuje přihlašovací údaje pro credentialType ' DomainLinkageCredential ', které jste podepsali svým držitelem, který má původ vaší domény. Tady je příklad konfiguračního dokumentu, který je uložený v adrese URL kořenové domény.


    ```json
    {
      "@context": "https://identity.foundation/.well-known/contexts/did-configuration-v0.0.jsonld",
      "linked_dids": [
        "jwt..."
      ]
    }
    ```

Po použití známého konfiguračního souboru je potřeba soubor zpřístupnit pomocí názvu domény, který jste zadali při povolování AAD pro ověřitelné přihlašovací údaje.

* Hostujte známý konfigurační soubor v kořenu domény.
* Nepoužívejte přesměrování.
* K distribuci konfiguračního souboru použijte protokol HTTPS.

>[!IMPORTANT]
>Microsoft Authenticator nedodržuje přesměrování, zadaná adresa URL musí být konečnou cílovou adresou URL.

## <a name="user-experience"></a>Uživatelské prostředí 

Když uživatel prochází tok vystavování nebo prezentuje ověřitelné přihlašovací údaje, měl by znát něco o organizaci a jeho činnosti. Pokud se doména s naším ověřitelným přihlašovacím jménem Microsoft Authenticator, ověří relaci s doménou v dokumentu, která obsahuje, a nabídne uživatelům dvě různá prostředí v závislosti na výsledku.

## <a name="verified-domain"></a>Ověřená doména

Předtím, než Microsoft Authenticator zobrazí **ověřenou** ikonu, je nutné, aby bylo splněno několik věcí:

* ODESLALi jsme žádost o podepsání otevřeného ID (SIOP), která je držitelem, musí mít koncový bod služby pro propojenou doménu.
* Kořenová doména nepoužívá přesměrování a používá protokol HTTPS.
* Doména uvedená v dokumentu v dokumentu má znovu přeložit dobře známý prostředek.
* Dobře známé přihlašovací údaje prostředku jsou podepsané stejným nástrojem, který se použil k podepsání SIOP, které Microsoft Authenticator použít k vystartu toku.

Pokud jsou všechny výše uvedené hodnoty pravdivé, Microsoft Authenticator zobrazí ověřenou stránku a zahrne ověřenou doménu.

![Nová žádost o oprávnění](media/how-to-dnsbind/new-permission-request.png) 

## <a name="unverified-domain"></a>Neověřená doména

Pokud některá z výše uvedených hodnot není pravdivá, Microsoft Authenticator zobrazí uživateli upozornění na úplnou stránku, že doména není ověřena, uživatel je uprostřed rizikové transakce a měl by pokračovat s upozorněním. Rozhodli jste se tuto trasu provést z těchto důvodů:

* Tato možnost není ukotvena k doméně.
* Konfigurace nebyla správně nastavena.
* Uživatel, na kterém je interakce, je škodlivý a ve skutečnosti nedokáže prokázat, že vlastní doména (protože ve skutečnosti ne). Vzhledem k tomuto poslednímu bodu je nutné, abyste provedli propojení s doménou, se kterou uživatel zná, aby nedošlo k šíření zprávy upozornění.

![Upozornění na neověřenou doménu na obrazovce Přidat přihlašovací údaje](media/how-to-dnsbind/add-credential-not-verified-authenticated.png)

## <a name="distribute-well-known-config"></a>Distribuovat dobře známou konfiguraci

1. Přejděte na stránku nastavení v ověřitelných přihlašovacích údajích a vyberte možnost **ověřit tuto doménu** .

   ![Ověřit tuto doménu v nastaveních](media/how-to-dnsbind/settings-verify.png) 

2. Stáhněte did-configuration.jsv souboru na obrázku níže.

   ![Stáhnout dobře známou konfiguraci](media/how-to-dnsbind/verify-download.png) 

3. Zkopírujte token JWT, otevřete [JWT.MS](https://www.jwt.ms) a ověřte, jestli je doména správná.

4. Zkopírujte a otevřete panel sítě s [ionty](https://identity.foundation/ion/explorer) , abyste ověřili, že je stejná doména obsažená v dokumentu. 

5. Hostujte dobře známý konfigurační prostředek v zadaném umístění. Příklad: https://www.example.com/.well-known/did-configuration.json

6. Otestujte vystavení nebo prezentace s Microsoft Authenticator k ověření. Ujistěte se, že je zapnuté nastavení v okně upozornění na nezabezpečené aplikace.

>[!NOTE]
>Ve výchozím nastavení je zapnutá možnost upozornit na nezabezpečené aplikace.

Gratulujeme, právě jste si naimplementovali web důvěry s vaší službou.

## <a name="next-steps"></a>Další kroky

Pokud při připojování zadáte nesprávné informace o doméně, kterou se rozhodnete změnit, budete se muset [Odhlásit](how-to-opt-out.md). V tuto chvíli nepodporujeme aktualizaci vašeho dokumentu. Vypnutí a odsouhlasení v nástroji vytvoří zcela novou značku.
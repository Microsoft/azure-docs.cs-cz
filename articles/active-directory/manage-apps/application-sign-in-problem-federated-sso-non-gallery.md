---
title: Problémy s přihlašováním do federované aplikace jednotného přihlašování mimo galerii
description: Doprovodné materiály k konkrétním problémům, se kterými se můžete setkat při přihlašování k aplikaci nakonfigurované pro federované jednotné přihlašování založené na SAML pomocí Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b8aac627936aef2cfa79bbd92d6163fe40b4d32
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274859"
---
# <a name="problems-signing-in-to-a-non-gallery-application-configured-for-federated-single-sign-on"></a>Problémy s přihlašováním k aplikaci mimo galerii nakonfigurovanou pro federované jednotné přihlašování

Pokud chcete řešit potíže s přihlašováním níže, doporučujeme vám postupovat podle těchto návrhů a získat tak lepší diagnostiku a automatizovat postup řešení:

- Pokud chcete zajistit lepší diagnostiku a řešení při použití zkušebního prostředí v Azure Portal, nainstalujte [rozšíření prohlížeče moje aplikace](access-panel-extension-problem-installing.md) na pomoc Azure Active Directory (Azure AD).
- Reprodukuje chybu pomocí prostředí testování na stránce konfigurace aplikace v Azure Portal. Další informace o [ladění aplikací jednotného přihlašování založeného na SAML](../develop/howto-v1-debug-saml-sso-issues.md)

## <a name="application-not-found-in-directory"></a>Aplikace nebyla nalezena v adresáři.

*Chyba AADSTS70001: aplikace s identifikátorem `https://contoso.com` nebyla nalezena v adresáři*.

**Možná příčina**

Atribut vystavitele odeslaný z aplikace do služby Azure AD v požadavku SAML se neshoduje s hodnotou identifikátoru nakonfigurovanou v aplikaci Azure AD.

**Rozhodnutí**

Zajistěte, aby se atribut `Issuer` v žádosti SAML shodoval s hodnotou identifikátoru nakonfigurovanou v Azure AD. Pokud používáte prostředí pro [testování](../develop/howto-v1-debug-saml-sso-issues.md) v Azure Portal s rozšířením zabezpečeného prohlížeče moje aplikace, nemusíte ručně postupovat podle těchto kroků.

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**

6. Vyberte aplikaci, pro kterou chcete nakonfigurovat jednotné přihlašování.

7. Po načtení aplikace klikněte na tlačítko **jednotného přihlašování** z nabídky navigace na levé straně aplikace.

8. Po načtení aplikace otevřete **základní konfiguraci SAML**. Ověřte, zda se hodnota v textovém poli identifikátoru shoduje s hodnotou identifikátoru zobrazeného v chybě.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>Adresa pro odpověď neodpovídá adresám odpovědí nakonfigurovaným pro aplikaci. 

*Chyba AADSTS50011: adresa příjemce `https://contoso.com` neodpovídá adresám odpovědí nakonfigurovaným pro aplikaci.* 

**Možná příčina** 

Hodnota AssertionConsumerServiceURL v požadavku SAML neodpovídá hodnotě adresy URL odpovědi nebo vzoru nakonfigurovanému ve službě Azure AD. Hodnota AssertionConsumerServiceURL v požadavku SAML je adresa URL, která se zobrazí v chybě. 

**Rozhodnutí** 

Zajistěte, aby se atribut `Issuer` v žádosti SAML shodoval s hodnotou identifikátoru nakonfigurovanou v Azure AD. Pokud používáte prostředí pro [testování](../develop/howto-v1-debug-saml-sso-issues.md) v Azure Portal s rozšířením zabezpečeného prohlížeče moje aplikace, nemusíte ručně postupovat podle těchto kroků.
 
1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.** 

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo. 

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** . 

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** . 

5. Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací. 

   * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**
  
6. Vyberte aplikaci, pro kterou chcete nakonfigurovat jednotné přihlašování.

7. Po načtení aplikace klikněte na tlačítko **jednotného přihlašování** z nabídky navigace na levé straně aplikace.

8. Po načtení aplikace otevřete **základní konfiguraci SAML**. Ověřte nebo aktualizujte hodnotu v textovém poli adresy URL odpovědi tak, aby odpovídala hodnotě `AssertionConsumerServiceURL` v požadavku SAML.    
    
Po aktualizaci hodnoty adresy URL odpovědi v Azure AD, která odpovídá hodnotě odeslané aplikací v žádosti SAML, byste měli být schopni se přihlásit k aplikaci.

## <a name="user-not-assigned-a-role"></a>Uživatel nemá přiřazenou roli.

*Chyba AADSTS50105: přihlášený uživatel `brian\@contoso.com` není přiřazen k roli pro aplikaci.*

**Možná příčina**

Uživateli nebyl udělen přístup k aplikaci ve službě Azure AD.

**Rozhodnutí**

Chcete-li přiřadit jednoho nebo více uživatelů k aplikaci přímo, postupujte podle následujících kroků. Pokud používáte prostředí pro [testování](../develop/howto-v1-debug-saml-sso-issues.md) v Azure Portal s rozšířením zabezpečeného prohlížeče moje aplikace, nemusíte ručně postupovat podle těchto kroků.

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**

6. Vyberte aplikaci, kterou chcete přiřadit uživatele ze seznamu.

7. Po načtení aplikace klikněte na **Uživatelé a skupiny** v nabídce navigace na levé straně aplikace.

8. Kliknutím na tlačítko **Přidat** v horní části seznamu **uživatelů a skupin** otevřete podokno **Přidat přiřazení** .

9. v podokně **Přidat přiřazení** klikněte na selektor **Uživatelé a skupiny** .

10. Do pole **Hledat podle jména nebo e-mailové adresy** zadejte jméno a **příjmení** nebo **e-mailovou adresu** uživatele, kterého se zajímáte o přiřazení.

11. Pokud chcete zobrazit **zaškrtávací políčko**, najeďte myší na **uživatele** v seznamu. Kliknutím na zaškrtávací políčko vedle profilové fotografie nebo loga uživatele přidáte uživatele do **vybraného** seznamu.

12. **Volitelné:** Pokud chcete **Přidat více než jednoho uživatele**, zadejte do vyhledávacího pole **jméno nebo e-mailová** adresa další **jméno** nebo **e-mailovou adresu** a kliknutím na zaškrtávací políčko přidejte tohoto uživatele do **vybraného** seznamu.

13. Po dokončení výběru uživatelů klikněte na tlačítko **Vybrat** a přidejte je do seznamu uživatelů a skupin, které chcete přiřadit k aplikaci.

14. **Volitelné:** kliknutím na výběr **role** v podokně **Přidat přiřazení** vyberte roli, kterou chcete přiřadit vybraným uživatelům.

15. Kliknutím na tlačítko **přiřadit** aplikaci přiřadíte vybraným uživatelům.

Po krátké době možné ke spouštění těchto aplikací pomocí metod popsaných v části popis řešení uživatele, které jste vybrali.

## <a name="not-a-valid-saml-request"></a>Nejedná se o platnou žádost SAML.

*Chyba AADSTS75005: požadavek není platná zpráva protokolu typu Saml2.*

**Možná příčina**

Azure AD nepodporuje požadavek SAML, který aplikace odeslala pro jednotné přihlášení. Mezi běžné problémy patří:

-   Chybí povinná pole v požadavku SAML.

-   Metoda kódovaná v požadavku SAML

**Rozhodnutí**

1.  Zachytit požadavek SAML. v tomto kurzu se dozvíte, [jak pomocí SAML ladit jednotné přihlašování k aplikacím ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging) a Naučte se zachytit požadavek SAML.

2.  Kontaktujte dodavatele aplikace a sdílejte tyto položky:

    -   Požadavek SAML

    -   [Požadavky protokolu SAML jednotného přihlašování Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

Dodavatel aplikace by měl ověřit, jestli podporují implementaci Azure AD SAML pro jednotné přihlašování.

## <a name="misconfigured-application"></a>Chybně nakonfigurovaná aplikace

*Chyba AADSTS650056: nesprávně nakonfigurovaná aplikace To může být způsobeno jedním z následujících způsobů: klient v rámci registrace aplikace klienta neuvádí žádná oprávnění pro ' typ grafu AAD ' v požadovaných oprávněních. Nebo správce v tenantovi nesouhlasí. Případně můžete v žádosti zkontrolovat identifikátor aplikace a zajistit, aby odpovídaly nakonfigurovanému identifikátoru klientské aplikace. Obraťte se prosím na správce, aby opravil konfiguraci nebo souhlas jménem tenanta.* ..

**Možná příčina**

Atribut `Issuer` odeslaný z aplikace do služby Azure AD v požadavku SAML se neshoduje s hodnotou identifikátoru nakonfigurovanou pro aplikaci ve službě Azure AD.

**Rozhodnutí**

Zajistěte, aby se atribut `Issuer` v žádosti SAML shodoval s hodnotou identifikátoru nakonfigurovanou v Azure AD. Pokud používáte [prostředí testování](../develop/howto-v1-debug-saml-sso-issues.md) v Azure Portal s rozšířením zabezpečeného prohlížeče moje aplikace, nemusíte ručně postupovat podle těchto kroků:

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce**.

1.  Otevřete **rozšíření Azure Active Directory** tím, že vyberete **všechny služby** v horní části hlavní navigační nabídky vlevo.

1.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Azure Active Directory** .

1.  Vyberte **podnikové aplikace** z navigační nabídky Azure Active Directory vlevo.

1.  Výběrem **všech aplikací** zobrazíte seznam všech aplikací.

    Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace**.

1.  Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

1.  Po načtení aplikace otevřete **základní konfiguraci SAML**. Ověřte, zda se hodnota v textovém poli identifikátoru shoduje s hodnotou identifikátoru zobrazeného v chybě.

## <a name="certificate-or-key-not-configured"></a>Certifikát nebo klíč není nakonfigurovaný.

Chyba AADSTS50003: není nakonfigurovaný žádný podpisový klíč.

**Možná příčina**

Objekt aplikace je poškozený a Azure AD nerozpozná certifikát nakonfigurovaný pro aplikaci.

**Rozhodnutí**

Chcete-li odstranit a vytvořit nový certifikát, postupujte podle následujících kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**

6. Vyberte aplikaci, pro kterou chcete nakonfigurovat jednotné přihlašování.

7. Po načtení aplikace klikněte na tlačítko **jednotného přihlašování** z nabídky navigace na levé straně aplikace.

8. v části **podpisový certifikát SAML** klikněte na **vytvořit nový certifikát** .

9. Vyberte datum vypršení platnosti. Pak klikněte na **Uložit.**

10. Pokud chcete aktivní certifikát přepsat, zkontrolujte, **že je aktivní nový certifikát** . Pak v horní části podokna klikněte na **Uložit** a přijměte aktivaci certifikátu výměny.

11. V části **podpisový certifikát SAML** kliknutím na **Odebrat** odeberte **nepoužitý** certifikát.

## <a name="saml-request-not-present-in-the-request"></a>Požadavek SAML není v žádosti přítomen.

*Chyba AADSTS750054: SAMLRequest nebo SAMLResponse musí být přítomné jako parametry řetězce dotazu v požadavku HTTP pro vazbu přesměrování SAML.*

**Možná příčina**

Služba Azure AD nemohla identifikovat požadavek SAML v rámci parametrů adresy URL v požadavku HTTP. K tomu může dojít, když aplikace při odesílání požadavku SAML do Azure AD nepoužívá vazbu přesměrování HTTP.

**Rozhodnutí**

Aplikace potřebuje odeslat požadavek SAML zakódovaný do hlavičky umístění pomocí vazby přesměrování HTTP. Další informace o způsobu implementace najdete v části věnované vazbě HTTP Redirect v [dokumentu specifikace protokolu SAML](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD posílá token do nesprávného koncového bodu.

**Možná příčina**

Pokud v rámci jednotného přihlašování požadavek na přihlášení neobsahuje adresu URL explicitní odpovědi (adresa URL služby potvrzení), služba Azure AD vybere kteroukoli z konfigurovaných adres URL pro danou aplikaci. I když má aplikace nakonfigurované explicitní adresy URL odpovědi, může být uživatel přesměrován https://127.0.0.1:444. 

Při přidávání aplikace jako aplikace mimo galerii služba Azure Active Directory vytvořila tuto adresu URL pro odpověď jako výchozí hodnotu. Toto chování se změnilo a Azure Active Directory už tuto adresu URL ve výchozím nastavení nepřidává. 

**Rozhodnutí**

Odstraní nepoužívané adresy URL odpovědí nakonfigurované pro aplikaci.

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce**.

2.  Otevřete **rozšíření Azure Active Directory** tím, že vyberete **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Azure Active Directory** .

4.  Vyberte **podnikové aplikace** z navigační nabídky Azure Active Directory vlevo.

5.  Výběrem **všech aplikací** zobrazíte seznam všech aplikací.

    Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace**.

6.  Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

7.  Po načtení aplikace otevřete **základní konfiguraci SAML**. V **adrese URL odpovědi (adresa URL služby vyhodnocení zákazníka)** Odstraňte nepoužívané nebo výchozí adresy URL odpovědí vytvořené systémem. Například, `https://127.0.0.1:444/applications/default.aspx`.



## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problém při přizpůsobení deklarací SAML odeslaných do aplikace

Další informace o tom, jak přizpůsobit deklarace identity atributů SAML odeslané do vaší aplikace, najdete v tématu [mapování deklarací v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) .

## <a name="next-steps"></a>Další kroky
[Požadavky protokolu SAML jednotného přihlašování Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

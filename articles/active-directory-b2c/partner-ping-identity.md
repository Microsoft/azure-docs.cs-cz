---
title: Kurz konfigurace Azure Active Directory B2C s využitím nástroje test identity
titleSuffix: Azure AD B2C
description: Informace o tom, jak integrovat Azure AD B2C ověřování pomocí nástroje test identity
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 430629f94695f0689422434c8d80fe4e1876e5dd
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900178"
---
# <a name="tutorial-configure-ping-identity-with-azure-active-directory-b2c-for-secure-hybrid-access"></a>Kurz: Konfigurace identity pomocí Azure Active Directory B2C pro zabezpečený hybridní přístup

V tomto ukázkovém kurzu se naučíte, jak pomocí  [PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) a [PingFederate](https://www.pingidentity.com/en/software/pingfederate.html) roztáhnout Azure Active Directory (AD) B2C a povolit tak zabezpečený hybridní přístup.

Mnoho existujících webových vlastností, jako jsou weby elektronického obchodování a webové aplikace, které jsou zpřístupněny pro Internet, jsou nasazeny za systémem proxy, někdy se však označují jako systém reverzního proxy serveru. Tyto proxy systémy poskytují různé funkce, včetně předběžného ověřování, vynucování zásad a směrování provozu. Příklady případů použití zahrnují ochranu webové aplikace před příchozím webovým provozem a poskytování jednotné správy relace napříč nasazeními distribuovaných serverů.

Ve většině případů tato konfigurace zahrnuje vrstvu překladu ověřování, která shrnuje ověřování z webové aplikace. Reverzní proxy servery zase poskytují kontext ověřených uživatelů k webovým aplikacím, a to jednodušším způsobem, jako je hodnota záhlaví ve formě Clear nebo Digest. V takové konfiguraci aplikace nepoužívají žádné standardní tokeny, jako je Security Assertion Markup Language (SAML), OAuth nebo Open ID Connect (OIDC), a spíše závisí na proxy serveru k poskytnutí kontextu ověřování a udržování relace s agentem koncového uživatele, jako je například prohlížeč nebo nativní aplikace. Jako služba, která je spuštěná ve formě "man-in-the-middle", můžou mít servery proxy jedinečný ovládací prvek relace. To také znamená, že by proxy služba měla být vysoce efektivní a škálovatelná, nemusí se stát kritickým bodem nebo jediným bodem selhání pro aplikace za službou proxy. Diagram je znázornění typické implementace reverzního proxy serveru a toku komunikace.

![obrázek ukazuje implementaci reverzního proxy serveru](./media/partner-ping/reverse-proxy.png)

Pokud jste v situaci, kdy chcete v takových konfiguracích modernizovat platformu identity, jsou vyvolány následující obavy.

- Jak se může úsilí pro moderní aplikace oddělit od modernizace platformy identity?

- Jak se dá vytvořit prostředí koexistence s moderním a starším ověřováním, které je náročné od poskytovatele moderní identity Service?

  a. Jak řídit konzistenci prostředí koncových uživatelů?

  b. Jak zajistit jednotné přihlašování napříč stávajícími aplikacemi?

Probereme přístup k vyřešení těchto obav integrací Azure AD B2C s technologiemi [PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) a [PingFederate](https://www.pingidentity.com/en/software/pingfederate.html) .

## <a name="coexistence-environment"></a>Prostředí koexistence

Technicky životaschopné jednoduché řešení, které je také cenově výhodné, je nakonfigurovat systém reverzního proxy serveru tak, aby používal moderní systém identit a delegování ověřování.  
Proxy v tomto případě budou podporovat moderní protokoly ověřování a budou používat ověřování založené na přesměrování (pasivní), které odešle uživateli novému zprostředkovateli identity (IdP).

### <a name="azure-ad-b2c-as-an-identity-provider"></a>Azure AD B2C jako zprostředkovatel identity

Azure AD B2C má možnost definovat **zásady** , které řídí různé uživatelské prostředí a chování, které se také nazývají **cesty uživatelů** , jako orchestrace z konce serveru. Každá taková zásada zveřejňuje koncový bod protokolu, který může provést ověřování, jako by šlo o IdP. Na straně aplikace nejsou nutné žádné speciální manipulace pro konkrétní zásady. Aplikace jednoduše vytvoří průmyslový standardní požadavek na ověření na koncový bod ověřování specifický pro protokol, který je vystavený zásadou zájmu.  
Azure AD B2C můžete nakonfigurovat tak, aby sdílely stejného vystavitele napříč několika zásadami nebo jedinečným vystavitelem pro každou zásadu. Každá aplikace může na jednu nebo několik těchto zásad odkazovat tak, že vytvoří nativní požadavek na ověření protokolu a nastaví požadované uživatelské chování, jako je přihlášení, registrace a úpravy profilu. Diagram znázorňuje OIDC a pracovní postupy aplikace SAML.

![obrázek ukazuje implementaci OIDC a SAML.](./media/partner-ping/azure-ad-identity-provider.png)

I když zmíněný scénář dobře funguje pro moderní aplikace, může být obtížné, aby starší aplikace správně přesměrovaly uživatele, protože žádost o přístup k aplikacím nemusí zahrnovat kontext pro činnost koncového uživatele. Ve většině případů proxy vrstva nebo integrovaný Agent na webové aplikaci zachytí žádost o přístup.

### <a name="pingaccess-as-a-reverse-proxy"></a>PingAccess jako reverzní proxy server

Mnoho zákazníků nasadilo PingAccess jako reverzní proxy server, který hraje jednu nebo více rolí, jak je uvedeno výše v tomto článku. PingAccess může zachytit přímý požadavek tak, že se jedná o prostředník nebo jako přesměrování, které pochází z agenta běžícího na serveru webové aplikace.

PingAccess se dá nakonfigurovat pomocí OIDC, OAuth2 nebo SAML, aby se provedlo ověřování vůči zprostředkovateli nadřazeného ověřování. Pro tento účel je možné nakonfigurovat jeden nadřazený IdP na serveru PingAccess. Tuto konfiguraci znázorňuje následující diagram.

![obrázek znázorňuje PingAccess s implementací OIDC.](./media/partner-ping/authorization-flow.png)

V typických nasazeních Azure AD B2C, kde více zásad zveřejňuje více **zprostředkovatelů identity**, se může jednat o výzvu. Vzhledem k tomu, že PingAccess se dá nakonfigurovat jenom s jedním nadřazeným IdP.  

### <a name="pingfederate-as-a-federation-proxy"></a>PingFederate jako federační proxy server

PingFederate je podniková identita, kterou můžete v případě potřeby plně nakonfigurovat jako poskytovatele ověřování nebo proxy server pro jiné nadřazené zprostředkovatelů identity. Tato možnost je znázorněna na následujícím obrázku.

![obrázek ukazuje PingFederate implementaci](./media/partner-ping/pingfederate.png)

Tato funkce se dá použít k kontextovému/dynamickému nebo deklarativnímu přepnutí příchozího požadavku na konkrétní zásadu Azure AD B2C. Následuje popis toku sekvence protokolu pro tuto konfiguraci.

![obrázek znázorňuje pracovní postup PingAccess a PingFederate.](./media/partner-ping/pingaccess-pingfederate-workflow.png)

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, budete potřebovat:

- Předplatné Azure. Pokud ho nemáte, Získejte [bezplatný účet](https://azure.microsoft.com/free/).

- [Tenant Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) , který je propojený s vaším předplatným Azure.

- PingAccess a PingFederate nasazené v kontejnerech Docker nebo přímo na virtuálních počítačích Azure.

## <a name="connectivity"></a>Připojení

Ověřte, že je připojeno následující.

- **PingAccess Server** – umožňuje komunikaci se serverem PingFederate, klientským prohlížečem, OIDC, známým a m zjišťováním OAuth, které publikovala služba Azure AD B2C a server PingFederate.

- **Server PingFederate** – může komunikovat se serverem PingAccess, klientským prohlížečem, OIDC, známým a m zjišťováním OAuth, které publikovala služba Azure AD B2C.

- **Starší verze nebo aplikace Authn založené na hlavičkách** – schopné komunikovat s PingAccess serverem a z něj.

- **Aplikace předávající strany SAML** – může dosáhnout provozu prohlížeče z klienta. Přístup k metadatům federace SAML publikovaným službou Azure AD B2C

- **Moderní aplikace** – může dosáhnout provozu prohlížeče z klienta. Přístup k OIDCu, známým a klíčovým zjišťováním OAuth, který publikovala služba Azure AD B2C

- **REST API** – umožní přístup k provozu z nativního nebo webového klienta. Přístup k OIDCu, známým a klíčovým zjišťováním OAuth, který publikovala služba Azure AD B2C

## <a name="configure-azure-ad-b2c"></a>Konfigurace Azure AD B2C

Pro tento účel můžete použít zásady základních uživatelských toků nebo Advanced identity Enterprise Framework (IEF). PingAccess vygeneruje koncový bod metadat založený na hodnotě **vystavitele** pomocí konvence zjišťování založené na [webfinger](https://tools.ietf.org/html/rfc7033) .
Pokud chcete postupovat podle této konvence, aktualizujte aktualizaci vystavitele Azure AD B2C pomocí vlastností zásad v části toky uživatelů.

![obrázek ukazuje nastavení tokenu](./media/partner-ping/token-setting.png)

V pokročilých zásadách se dá nakonfigurovat pomocí elementu metadata **IssuanceClaimPattern** k **AuthorityWithTfp** hodnoty v [technickém profilu vystavitele tokenu JWT](https://docs.microsoft.com/azure/active-directory-b2c/jwt-issuer-technical-profile).

## <a name="configure-pingaccesspingfederate"></a>Konfigurace PingAccess/PingFederate

V následující části jsou popsány požadované konfigurace.
Diagram znázorňuje Celkový tok uživatele pro integraci.

![obrázek ukazuje integraci PingAccess a PingFederate.](./media/partner-ping/pingaccess.png)

### <a name="configure-pingfederate-as-the-token-provider"></a>Konfigurace PingFederate jako poskytovatele tokenů

Pokud chcete nakonfigurovat PingFederate jako poskytovatele tokenů pro PingAccess, zajistěte, aby připojení od PingFederate do PingAccess bylo navázáno připojením od PingAccess k PingFederate.  
Postup konfigurace najdete v [tomto článku](https://docs.pingidentity.com/bundle/pingaccess-61/page/zgh1581446287067.html) .

### <a name="configure-a-pingaccess-application-for-header-based-authentication"></a>Konfigurace aplikace PingAccess pro ověřování na základě hlaviček

Pro cílovou webovou aplikaci pro ověřování na základě hlaviček musí být vytvořena aplikace PingAccess. Postupujte takto:

#### <a name="step-1--create-a-virtual-host"></a>Krok 1 – Vytvoření virtuálního hostitele

>[!IMPORTANT]
>Pro konfiguraci pro toto řešení je potřeba vytvořit virtuálního hostitele pro každou aplikaci. Další informace o požadavcích na konfiguraci a jejich dopadech najdete v tématu [klíčové důležité](https://docs.pingidentity.com/bundle/pingaccess-43/page/reference/pa_c_KeyConsiderations.html)informace.

Pomocí těchto kroků vytvořte virtuálního hostitele:

1. Přejít na **Nastavení**  >  **přístup k**  >  **virtuálním hostitelům**

2. Vyberte **Přidat virtuálního hostitele** .

3. Do pole Hostitel zadejte část adresy URL aplikace, která je plně kvalifikovaného názvu domény.

4. Do pole Port zadejte **443**

5. Vyberte **Uložit**.

#### <a name="step-2--create-a-web-session"></a>Krok 2 – Vytvoření webové relace

Pomocí těchto kroků vytvořte relaci webu:

1. Přejít k **Nastavení**  >  **přístup k**  >  **webovým relacím**

2. Vyberte **Přidat webovou relaci** .

3. Zadejte **název** webové relace.

4. Vyberte **typ souboru cookie**, **podepsaný JWT** nebo **zašifrovaný token JWT** .

5. Zadejte pro **cílovou skupinu** jedinečnou hodnotu.

6. Do pole **ID klienta** zadejte **ID aplikace Azure AD** .

7. Do pole **tajný kód klienta** zadejte **klíč** , který jste vygenerovali pro aplikaci ve službě Azure AD.

8. Volitelné – můžete vytvářet a používat vlastní deklarace identity pomocí rozhraní Microsoft Graph API. Pokud se rozhodnete tak učinit, vyberte možnost **Upřesnit** a zrušte výběr možnosti **Profil požadavku** a **Aktualizace atributů uživatele** . Další informace o používání vlastních deklarací identity najdete v tématu [použití vlastní deklarace identity](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#optional---use-a-custom-claim).

9. Vyberte **Uložit**.

#### <a name="step-3--create-identity-mapping"></a>Krok 3 – vytvoření mapování identit

>[!NOTE]
>Mapování identit lze použít s více než jednou aplikací, pokud více než jedna aplikace očekává stejná data v hlavičce.

Pomocí těchto kroků vytvořte mapování identit:

1. Přejít na **Nastavení**  >  **přístup k**  >  **mapování identit**

2. Vyberte **Přidat mapování identit** .

3. Zadat **název**

4. Vyberte typ mapování identit **mapování identit hlaviček** .

5. V tabulce **mapování atributů** zadejte požadovaná mapování. Třeba

   Název atributu | Název hlavičky |
   |-------|--------|
   |názvu | x – userprinciplename |
   |e-mail   |    x – e-mail  |
   |identifikátor   | x-OID  |
   |SCP   |     x – rozsah |
   |AMR    |    x – AMR    |

6. Vyberte **Uložit**.

#### <a name="step-4--create-a-site"></a>Krok 4 – Vytvoření webu

>[!NOTE]
>V některých konfiguracích je možné, že lokalita může obsahovat více než jednu aplikaci. Lokalitu lze použít s více než jednou aplikací, kde je to vhodné.

Pomocí těchto kroků vytvořte lokalitu:

1. Přejít na **Hlavní**  >  **lokality**

2. Vybrat **Přidat web**

3. Zadejte **název** webu.

4. Zadejte **cíl** webu. Cíl je název hostitele: dvojice portů serveru, který je hostitelem aplikace. Nezadávejte cestu k aplikaci v tomto poli. Například aplikace na https://mysite:9999/AppName bude mít cílovou hodnotu osobního webu: 9999

5. Označuje, zda cíl očekává zabezpečená připojení.

6. Pokud cíl očekává zabezpečená připojení, **nastavte důvěryhodnou skupinu důvěryhodných certifikátů.**

7. Vyberte **Uložit**.

#### <a name="step-5--create-an-application"></a>Krok 5 – Vytvoření aplikace

Pomocí těchto kroků vytvořte v PingAccess aplikaci pro každou aplikaci v Azure, kterou chcete chránit.

1. Přejít k **hlavním**  >  **aplikacím**

2. Vyberte **Přidat aplikaci** .

3. Zadejte **název** aplikace.

4. Volitelně zadejte **Popis** aplikace.

5. Zadejte **kořenový kontext** pro aplikaci. Například aplikace na https://mysite:9999/AppName bude mít kontext kořene/APPNAME. Kořen kontextu musí začínat lomítkem (/), nesmí končit lomítkem (/) a může být více než jedna vrstva hluboká, například/Apps/MyApp..

6. Vyberte **virtuálního hostitele** , kterého jste vytvořili.

   >[!NOTE]
   >Kombinace virtuálního hostitele a kořene kontextu musí být v PingAccess jedinečné.

7. Vyberte **webovou relaci** , kterou jste vytvořili.

8. Vyberte **lokalitu** , kterou jste vytvořili, která obsahuje aplikaci.

9. Vyberte **mapování identity** , které jste vytvořili.

10. Pokud chcete povolit web při uložení, vyberte **povoleno** .

11. Vyberte **Uložit**.

### <a name="configure-the-pingfederate-authentication-policy"></a>Konfigurace zásad ověřování PingFederate

Nakonfigurujte zásady ověřování PingFederate tak, aby federovat na víc zprostředkovatelů identity poskytovaných klienty Azure AD B2C.

1. Vytvořte kontrakt pro přemostění atributů mezi zprostředkovatelů identity a SP. Další informace najdete v tématu [kontrakty pro federační centrum a zásady ověřování](https://docs.pingidentity.com/bundle/pingfederate-101/page/ope1564002971971.html). Pokud SP nevyžaduje jinou sadu atributů od každého IdP, budete pravděpodobně potřebovat jenom jednu kontrakt.

2. Pro každou IdP vytvořte IdP spojení mezi IdP a PingFederate, a to jako s aktualizací SP.

3. V okně **mapování cílové relace** přidejte do připojení IDP příslušné kontrakty zásad ověřování.

4. V okně **Selektory** nakonfigurujte selektor ověřování. Například viz instance **prvního adaptéru identifikátoru** k namapování jednotlivých IDP na odpovídající připojení IDP v zásadách ověřování.

5. Vytvořte připojení SP mezi PingFederate, federační centrum jako IdP a SP.

6. Do připojení SP v okně **Mapování zdrojů ověřování** přidejte odpovídající kontrakt zásad ověřování.

7. Pracujte s každým IdP a připojte se k PingFederate, federačnímu centru jako SP.

8. Pracujte s aktualizací SP a připojte se k PingFederate, federační centrum jako IdP.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích.

- [Vlastní zásady v Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Začínáme s vlastními zásadami v Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)

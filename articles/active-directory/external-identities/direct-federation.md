---
title: Přímá federace se zprostředkovatelem identity pro B2B – Azure AD
description: Přímo se federovat se zprostředkovatelem identity SAML nebo WS-Fed, aby se hosté mohli přihlásit k aplikacím Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/24/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a23056445331c82aded878a77d1e0e730e4e2ceb
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96339468"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Přímá federace pomocí AD FS a poskytovatelů třetích stran pro uživatele typu Host (Preview)

> [!NOTE]
>  Přímá federace je funkce veřejné verze Preview Azure Active Directory. Další informace o verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

Tento článek popisuje, jak nastavit přímou federaci s jinou organizací pro spolupráci B2B. Můžete nastavit přímou federaci s jakoukoli organizací, jejíž poskytovatel identity (IdP) podporuje protokol SAML 2,0 nebo WS-Fed.
Když nastavíte přímou federaci s IdPem partnera, můžou noví uživatelé typu host z této domény používat vlastní účet organizace spravovaný IdP pro přihlášení k vašemu tenantovi Azure AD a začít spolupracovat s vámi. Není potřeba, aby uživatel typu Host vytvořil samostatný účet služby Azure AD.
> [!NOTE]
> Uživatelé typu host s přímým přístupem se musí přihlásit pomocí odkazu, který obsahuje kontext tenanta (například `https://myapps.microsoft.com/?tenantid=<tenant id>` nebo nebo `https://portal.azure.com/<tenant id>` v případě ověřené domény `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com` ). Přímé odkazy na aplikace a prostředky fungují i tak dlouho, dokud budou zahrnovat kontext tenanta. Uživatelé s přímými federace se momentálně nemůžou přihlásit pomocí běžných koncových bodů, které nemají kontext tenanta. Například použití `https://myapps.microsoft.com` , `https://portal.azure.com` , nebo `https://teams.microsoft.com` způsobí chybu.
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>Kdy je uživatel typu Host ověřený pomocí přímé federace?
Po nastavení přímé federace s organizací budou všichni noví uživatelé typu Host, kteří pozvánku nastavili, ověřeni pomocí přímé federace. Je důležité si uvědomit, že nastavení přímé federace nemění metodu ověřování pro uživatele typu Host, kteří už od vás požádali o pozvání. Tady je několik příkladů:
 - Pokud uživatelé typu host už z vaší organizace překládali pozvánky a následně jste nastavili přímo federaci s jejich organizací, budou tito uživatelé typu Host dál používat stejnou metodu ověřování, kterou používali před nastavením přímé federace.
 - Pokud nastavíte přímou federaci s partnerskými organizacemi a budete pozvat uživatele typu Host a partnerská organizace se později přesune do služby Azure AD, budou se uživatelé typu Host, kteří už provedli uplatnění pozvánky, dál používat přímé federace, pokud existují přímé federační zásady ve vašem tenantovi.
 - Pokud odstraníte přímou federaci s partnerskými organizacemi, všichni uživatelé typu Host, kteří aktuálně používají přímou federaci, se nebudou moci přihlásit.

V některém z těchto scénářů můžete aktualizovat metodu ověřování uživatele typu Host tím, že z adresáře odstraníte uživatelský účet hosta a znovu je zvete.

Přímá federace je vázaná na obory názvů domény, jako jsou contoso.com a fabrikam.com. Při vytváření přímé konfigurace federace s AD FS nebo IdP třetí strany organizace přiřadí k těmto zprostředkovatelů identity jeden nebo víc oborů názvů domény. 

## <a name="end-user-experience"></a>Prostředí koncového uživatele 
Pomocí přímé federace se uživatelé typu Host přihlásí k vašemu tenantovi Azure AD pomocí vlastního účtu organizace. Když přistupují ke sdíleným prostředkům a zobrazí se výzva k přihlášení, uživatelé s přímými federace se přesměrují na své IdP. Po úspěšném přihlášení se vrátí službě Azure AD, která bude mít přístup k prostředkům. Obnovovací tokeny pro uživatele s přímým přístupem jsou platné po dobu 12 hodin, což je [výchozí délka tokenu předávací aktualizace](../develop/active-directory-configurable-token-lifetimes.md#exceptions) ve službě Azure AD. Pokud má federované IdP jednotné přihlašování (SSO), uživatel bude mít přístup k JEDNOTNÉmu přihlašování a po počátečním ověření se nezobrazí žádné výzvy k přihlášení.

## <a name="limitations"></a>Omezení

### <a name="dns-verified-domains-in-azure-ad"></a>Domény ověřené DNS v Azure AD
Doména, kterou chcete federovat s **, nesmí být** ve službě Azure AD ověřená DNS. Máte možnost nastavit přímou federaci s nespravovanými (e-mailem nebo "virovými") klienty Azure AD, protože nejsou ověřené DNS.

### <a name="authentication-url"></a>Adresa URL pro ověření
Přímá federace je povolená jenom pro zásady, ve kterých se doména URL ověřování shoduje s cílovou doménou, nebo kde adresa URL ověřování je jedním z těchto povolených zprostředkovatelů identity (Tento seznam se může změnit):

-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   oktapreview.com
-   okta-emea.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

Například při nastavování přímé federace pro _ * Fabrikam. com * * se ověřování předá ověřovací adresa URL `https://fabrikam.com/adfs` . Například hostitel ve stejné doméně bude také předávat `https://sts.fabrikam.com/adfs` . Adresa URL `https://fabrikamconglomerate.com/adfs` pro ověření nebo `https://fabrikam.com.uk/adfs` stejná doména ale nebude úspěšné.

### <a name="signing-certificate-renewal"></a>Podpisové obnovení certifikátu
Pokud v nastavení zprostředkovatele identity zadáte adresu URL metadat, služba Azure AD automaticky obnoví podpisový certifikát, jakmile vyprší jeho platnost. Pokud se ale certifikát z jakéhokoli důvodu před časem vypršení platnosti neposkytne, nebo pokud nezadáte adresu URL metadat, Azure AD ho nebude moct obnovit. V takovém případě budete muset podpisový certifikát aktualizovat ručně.

### <a name="limit-on-federation-relationships"></a>Omezení federačních vztahů
V současné době je podporováno maximálně 1 000 federačních vztahů. Toto omezení zahrnuje i [interní federace](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) i přímé federace.

### <a name="limit-on-multiple-domains"></a>Omezení ve více doménách
V současné době nepodporujeme přímou federaci s více doménami ze stejného tenanta.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>Můžu nastavit přímou federaci s doménou, pro kterou existuje nespravovaný tenant (e-mail ověřený)? 
Ano. Pokud se doména neověřila a tenant neprošel [převzetím správce](../enterprise-users/domains-admin-takeover.md), můžete nastavit přímou federaci s touto doménou. Nespravované nebo ověřené e-mailem jsou klienti vytvořeni v případě, že uživatel uplatní pozvánku B2B nebo provede samoobslužnou registraci pro službu Azure AD pomocí domény, která aktuálně neexistuje. S těmito doménami můžete nastavit přímou federaci. Pokud se pokusíte nastavit přímou federaci s doménou ověřenou DNS, ať už v Azure Portal nebo prostřednictvím PowerShellu, zobrazí se chyba.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Pokud je povolená přímá federace a e-mailová ověřování jednorázového hesla, která metoda má přednost?
Pokud je v partnerské organizaci vytvořená přímá federace, má přednost před ověřováním jednorázovým heslem e-mailu pro nové uživatele typu host z této organizace. Pokud uživatel typu Host znovu nastavil pozvánku pomocí jednorázového ověřování hesla před nastavením přímé federace, bude používat jednorázové ověřování pomocí hesla. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>Jsou problémy s přihlašováním přímo v rámci federačních adres způsobeny částečně synchronizovanými tenantů?
Ne, v tomto scénáři by měla být použita funkce pro [jednorázové heslo e-mailu](one-time-passcode.md) . "Částečně synchronizovaná tenant" odkazuje na partnera Azure AD, u kterého nejsou místní identity uživatelů plně synchronizovány do cloudu. Host, jehož identita ještě v cloudu neexistuje, ale pokud se pokusí uplatnit uplatnění pozvánky B2B, nebude se moct přihlásit. Funkce jednorázového hesla umožní tomuto hostovi přihlašovat se. Funkce přímé federace adresuje scénáře, kde má host svůj vlastní účet organizace spravovaný IdP, ale organizace nemá žádnou přítomnost Azure AD vůbec.

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>Krok 1: Konfigurace zprostředkovatele identity partnerské organizace
Nejdřív vaše partnerská organizace potřebuje nakonfigurovat poskytovatele identity o požadované deklarace identity a vztahy důvěryhodnosti předávající strany. 

> [!NOTE]
> K ilustraci, jak nakonfigurovat poskytovatele identity pro přímou federaci, použijeme jako příklad Active Directory Federation Services (AD FS) (AD FS). Přečtěte si článek [Konfigurace přímé federace s AD FS](direct-federation-adfs.md), který obsahuje příklady konfigurace AD FS jako zprostředkovatele identity SAML 2,0 nebo WS-Fed poskytovatele identity v přípravě na přímou federaci.

### <a name="saml-20-configuration"></a>Konfigurace SAML 2,0

Azure AD B2B se dá nakonfigurovat tak, aby federovat s poskytovateli identity, kteří používají protokol SAML s konkrétními požadavky uvedenými níže. Další informace o nastavení vztahu důvěryhodnosti mezi poskytovatelem identity SAML a Azure AD najdete v tématu  [použití zprostředkovatele identity saml 2,0 (IDP) pro jednotné přihlašování](../hybrid/how-to-connect-fed-saml-idp.md).  

> [!NOTE]
> Cílová doména pro přímou federaci nesmí být ověřená DNS v Azure AD. Doména URL ověřování musí odpovídat cílové doméně, nebo musí být doménou povoleného zprostředkovatele identity. Podrobnosti najdete v části [omezení](#limitations) . 

#### <a name="required-saml-20-attributes-and-claims"></a>Povinné atributy a deklarace SAML 2,0
V následujících tabulkách jsou uvedeny požadavky na konkrétní atributy a deklarace identity, které je třeba nakonfigurovat u poskytovatele identity třetí strany. Pokud chcete nastavit přímou federaci, musí se v odpovědi SAML 2,0 od poskytovatele identity přijmout následující atributy. Tyto atributy je možné nakonfigurovat tak, že propojíte se souborem XML služby token zabezpečení online nebo je zadáte ručně.

Požadované atributy pro odpověď SAML 2,0 z IdP:

|Atribut  |Hodnota  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Cílová skupina     |`urn:federation:MicrosoftOnline`         |
|Vystavitel     |Identifikátor URI vystavitele partnerského IdPu, například `http://www.example.com/exk10l6w90DHM0yi...`         |


Požadované deklarace pro token SAML 2,0 vydaný IdPem:

|Atribut  |Hodnota  |
|---------|---------|
|Formát NameID     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|EmailAddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>Konfigurace WS-Fed 
Azure AD B2B je možné nakonfigurovat tak, aby federovat s poskytovateli identity, kteří používají protokol WS-Fed s některými konkrétními požadavky, jak je uvedeno níže. V současné době byly u těchto dvou zprostředkovatelů WS-Fed testovány kompatibility s Azure AD zahrnutí AD FS a Shibboleth. Další informace o tom, jak vytvořit vztah důvěryhodnosti předávající strany mezi poskytovatelem WS-Fed kompatibilním s Azure AD, najdete v dokumentu věnovaném integraci služby STS pomocí protokolů WS, který je k dispozici v dokumentech pro [kompatibilitu zprostředkovatele identity Azure AD](https://www.microsoft.com/download/details.aspx?id=56843).

> [!NOTE]
> Cílová doména pro přímou federaci nesmí být ověřená DNS v Azure AD. Doména URL ověřování musí odpovídat cílové doméně nebo doméně povoleného poskytovatele identity. Podrobnosti najdete v části [omezení](#limitations) . 

#### <a name="required-ws-fed-attributes-and-claims"></a>Povinné atributy WS-Fed a deklarace identity

V následujících tabulkách jsou uvedeny požadavky na konkrétní atributy a deklarace identity, které je třeba nakonfigurovat u poskytovatele identity WS-Fed třetí strany. Chcete-li nastavit přímou federaci, musí být v WS-Fed zprávě od poskytovatele identity přijaty následující atributy. Tyto atributy je možné nakonfigurovat tak, že propojíte se souborem XML služby token zabezpečení online nebo je zadáte ručně.

Požadované atributy v WS-Fed zprávě z IdP:
 
|Atribut  |Hodnota  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Cílová skupina     |`urn:federation:MicrosoftOnline`         |
|Vystavitel     |Identifikátor URI vystavitele partnerského IdPu, například `http://www.example.com/exk10l6w90DHM0yi...`         |

Požadované deklarace identity pro token WS-Fed vydané IdPem:

|Atribut  |Hodnota  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|EmailAddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>Krok 2: Konfigurace přímé federace ve službě Azure AD 
Dále nakonfigurujete federaci s poskytovatelem identity nakonfigurovaným v kroku 1 v Azure AD. Můžete použít buď portál Azure AD, nebo PowerShell. Může trvat 5-10 minut, než se uplatní zásady přímých federačních zásad. Během této doby se nepokusit uplatnit pozvánku na přímou federační doménu. Jsou vyžadovány následující atributy:
- Identifikátor URI vystavitele partnerského IdPu
- Koncový bod pasivního ověřování partnera IdP (podporuje se jenom https)
- Certifikát

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Konfigurace přímé federace na portálu Azure AD

1. Přejděte na web [Azure Portal](https://portal.azure.com/). V levém podokně vyberte **Azure Active Directory**. 
2. Vyberte **externí identity**  >  **všichni zprostředkovatelé identity**.
3. Vyberte a pak vyberte **Nový SAML/WS-IDP**.

    ![Snímek obrazovky zobrazující tlačítko pro přidání nového SAML nebo WS-Fed IdP](media/direct-federation/new-saml-wsfed-idp.png)

4. Na **nové stránce IDP s podporou SAML/WS** vyberte v části **protokol poskytovatele identity** možnost **SAML** nebo **WS**.

    ![Snímek obrazovky zobrazující tlačítko pro analýzu na stránce SAML nebo WS-Fed IdP](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Zadejte název domény partnerské organizace, který bude cílovým názvem domény pro přímou federaci.
6. Můžete nahrát soubor metadat, který vyplní podrobnosti metadat. Pokud se rozhodnete zadat metadata ručně, zadejte následující informace:
   - Název domény partnerského IdPu
   - ID entity IdP partnera
   - Pasivní koncový bod žadatele IdP partnera
   - Certifikát
   > [!NOTE]
   > Adresa URL metadat je volitelná, ale důrazně ji doporučujeme. Pokud zadáte adresu URL metadat, může Azure AD automaticky obnovit podpisový certifikát, jakmile vyprší jeho platnost. Pokud se certifikát z jakéhokoli důvodu před časem vypršení platnosti nebo pokud neposkytnete adresa URL metadat, Azure AD ho nebude moct obnovit. V takovém případě budete muset podpisový certifikát aktualizovat ručně.

7. Vyberte **Uložit**. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>Konfigurace přímé federace ve službě Azure AD pomocí prostředí PowerShell

1. Nainstalujte nejnovější verzi Azure AD PowerShellu pro modul Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)). (Pokud potřebujete podrobné kroky, rychlý Start pro přidání uživatele typu Host zahrnuje část [Instalace nejnovějšího modulu AzureADPreview](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module).) 
2. Spusťte následující příkaz: 
   ```powershell
   Connect-AzureAD
   ```
1. V příkazovém řádku pro přihlášení se přihlaste pomocí účtu spravovaného globálního správce. 
2. Spusťte následující příkazy a nahraďte hodnoty ze souboru federačních metadat. Pro AD FS Server a okta je soubor federace federationmetadata.xml, například: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml` . 

   ```powershell
   $federationSettings = New-Object Microsoft.Open.AzureAD.Model.DomainFederationSettings
   $federationSettings.PassiveLogOnUri ="https://sts.totheclouddemo.com/adfs/ls/"
   $federationSettings.LogOffUri = $federationSettings.PassiveLogOnUri
   $federationSettings.IssuerUri = "http://sts.totheclouddemo.com/adfs/services/trust"
   $federationSettings.MetadataExchangeUri="https://sts.totheclouddemo.com/adfs/services/trust/mex"
   $federationSettings.SigningCertificate= <Replace with X509 signing cert’s public key>
   $federationSettings.PreferredAuthenticationProtocol="WsFed" OR "Samlp"
   $domainName = <Replace with domain name>
   New-AzureADExternalDomainFederation -ExternalDomainName $domainName  -FederationSettings $federationSettings
   ```

## <a name="step-3-test-direct-federation-in-azure-ad"></a>Krok 3: testování přímé federace ve službě Azure AD
Nyní otestujte nastavení přímé federace pozváním nového uživatele typu Host B2B. Podrobnosti najdete v tématu [Přidání uživatelů spolupráce Azure AD B2B v Azure Portal](add-users-administrator.md).
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Návody upravit přímý vztah federace?

1. Přejděte na web [Azure Portal](https://portal.azure.com/). V levém podokně vyberte **Azure Active Directory**. 
2. Vyberte **externí identity**.
3. Vybrat **všechny zprostředkovatele identity**
4. V části **Zprostředkovatelé identit SAML/WS** vyberte poskytovatele.
5. V podokně podrobností zprostředkovatele identity aktualizujte hodnoty.
6. Vyberte **Uložit**.


## <a name="how-do-i-remove-direct-federation"></a>Návody odebrat přímo federaci?
Můžete odebrat nastavení přímé federace. Pokud tak učiníte, přesměrujete uživatele typu Host, kteří už provedli své pozvánky, se nebudou moct přihlásit. Můžete jim ale udělit přístup k prostředkům tím, že je odstraníte z adresáře a znovu je dodáte. Postup odebrání přímé federace se zprostředkovatelem identity na portálu Azure AD:

1. Přejděte na web [Azure Portal](https://portal.azure.com/). V levém podokně vyberte **Azure Active Directory**. 
2. Vyberte **externí identity**.
3. Vyberte **všechny zprostředkovatele identity**.
4. Vyberte poskytovatele identity a pak vyberte **Odstranit**. 
5. Kliknutím na **Ano** potvrďte odstranění. 

Odebrání přímé federace se zprostředkovatelem identity pomocí prostředí PowerShell:
1. Nainstalujte nejnovější verzi Azure AD PowerShellu pro modul Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Spusťte následující příkaz: 
   ```powershell
   Connect-AzureAD
   ```
3. V příkazovém řádku pro přihlášení se přihlaste pomocí účtu spravovaného globálního správce. 
4. Zadejte následující příkaz:
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [prostředí pro uplatnění pozvánky](redemption-experience.md) , když se externí uživatelé přihlásí pomocí různých zprostředkovatelů identity.
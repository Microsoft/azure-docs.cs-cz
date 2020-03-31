---
title: Přímá federace s poskytovatelem identity pro B2B – Azure AD
description: Přímo federate s POSKYTOVATELEM identity SAML nebo WS-Fed, takže se hosté můžou přihlásit k vašim aplikacím Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b99a80a90df8fcfc5efe6dfa0c2cd7e8e5e04e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050884"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Přímá federace se službou AD FS a poskytovateli třetích stran pro uživatele typu Host (preview)
|     |
| --- |
| Přímá federace je funkce veřejného náhledu služby Azure Active Directory. Další informace o náhledech najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Tento článek popisuje, jak nastavit přímou federaci s jinou organizací pro spolupráci B2B. Můžete nastavit přímou federaci s libovolnou organizací, jejíž zprostředkovatel identity (IdP) podporuje protokol SAML 2.0 nebo WS-Fed.
Když nastavíte přímou federaci s idp partnera, noví uživatelé typu Host z této domény můžou použít svůj vlastní organizační účet spravovaný idp, aby se přihlásili k tenantovi Azure AD a začali s vámi spolupracovat. Není nutné, aby uživatel typu Host vytvořil samostatný účet Azure AD.
> [!NOTE]
> Uživatelé typu Host s přímou federací se musí `https://myapps.microsoft.com/?tenantid=<tenant id>` `https://portal.azure.com/<tenant id>`přihlásit pomocí odkazu, který `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com`obsahuje kontext klienta (například nebo , nebo v případě ověřené domény ). Přímé odkazy na aplikace a prostředky také fungovat tak dlouho, dokud zahrnují kontext klienta. Uživatelé přímé federace se aktuálně nemohou přihlásit pomocí běžných koncových bodů, které nemají žádný kontext klienta. Například pomocí `https://myapps.microsoft.com` `https://portal.azure.com`, `https://teams.microsoft.com` , nebo bude mít za následek chybu.
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>Kdy je uživatel typu Host ověřen s přímou federací?
Po nastavení přímé federace s organizací budou všichni noví pozvaní uživatelé typu Host ověřeni pomocí přímé federace. Je důležité si uvědomit, že nastavení přímé federace nezmění metodu ověřování pro uživatele typu Host, kteří již uplatnili pozvánku od vás. Zde je několik příkladů:
 - Pokud uživatelé typu Host již od vás uplatnili pozvánky a následně jste nastavili přímou federaci s jejich organizací, budou tito uživatelé typu Host nadále používat stejnou metodu ověřování, kterou použili před nastavením přímé federace.
 - Pokud nastavíte přímou federaci s partnerskou organizací a pozvete uživatele typu Host a partnerská organizace se později přesune do služby Azure AD, uživatelé typu Host, kteří již uplatnili pozvánky, budou nadále používat přímé federace, pokud je to přímé federace zásad y ve vašem tenantovi existuje.
 - Pokud odstraníte přímou federaci s partnerskou organizací, nebudou se moci přihlásit žádní uživatelé typu Host, kteří aktuálně používají přímou federaci.

V každém z těchto scénářů můžete aktualizovat metodu ověřování uživatele typu Host odstraněním uživatelského účtu hosta z adresáře a jejich opětovným pozváním.

Přímá federace je vázána na obory názvů domény, jako jsou contoso.com a fabrikam.com. Při vytváření přímé konfigurace federace se službou AD FS nebo s idp třetí strany organizace přidruží k těmto idp. 

## <a name="end-user-experience"></a>Prostředí koncového uživatele 
S přímou federací se uživatelé typu Host přihlašují k tenantovi Azure AD pomocí vlastního účtu organizace. Když přistupují ke sdíleným prostředkům a jsou vyzváni k přihlášení, jsou uživatelé přímé federace přesměrováni na zprostředkovatele zprostředkovatele. Po úspěšném přihlášení se vrátí do služby Azure AD pro přístup k prostředkům. Tokeny aktualizace uživatelů přímé federace jsou platné po dobu 12 hodin, [výchozí délka pro obnovovací token průchodu](../develop/active-directory-configurable-token-lifetimes.md#exceptions) ve službě Azure AD. Pokud federovaný IdP má povolení s tezaury, uživatel bude mít zacházet s uživatelským přihlašování a nezobrazí žádné přihlašovací výzvu po počátečním ověřování.

## <a name="limitations"></a>Omezení

### <a name="dns-verified-domains-in-azure-ad"></a>Domény ověřené DNS ve službě Azure AD
Doména, kterou chcete federate s ***nesmí*** být dns ověřena ve službě Azure AD. Můžete nastavit přímou federaci s nespravovanými (ověřenými e-mailem nebo "virálními") klienty Azure AD, protože nejsou ověřeni DNS.

### <a name="authentication-url"></a>Adresa URL ověřování
Přímá federace je povolena pouze pro zásady, kde doména adresy URL ověřování odpovídá cílové doméně nebo kde je adresa URL ověřování jedním z těchto povolených poskytovatelů identity (tento seznam se může změnit):
-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

Například při nastavování **fabrikam.com**přímé federace pro `https://fabrikam.com/adfs` fabrikam.com , adresa URL ověřování projde ověřením. Hostitel ve stejné doméně také projde, například `https://sts.fabrikam.com/adfs`. Adresa URL `https://fabrikamconglomerate.com/adfs` ověřování `https://fabrikam.com.uk/adfs` nebo pro stejnou doménu však neprojde.

### <a name="signing-certificate-renewal"></a>Obnovení podpisu certifikátu
Pokud zadáte adresu URL metadat v nastavení zprostředkovatele identity, Azure AD automaticky obnoví podpisový certifikát, když vyprší jeho platnost. Pokud je však certifikát z jakéhokoli důvodu před vypršením platnosti otočen nebo pokud nezadáte adresu URL metadat, služba Azure AD jej nebude moci obnovit. V takovém případě budete muset podpisový certifikát aktualizovat ručně.

### <a name="limit-on-federation-relationships"></a>Omezit vztahy federace
V současné době je podporováno maximálně 1 000 federačních vztahů. Tento limit zahrnuje [jak interní federace,](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) tak přímé federace.

### <a name="limit-on-multiple-domains"></a>Omezit více domén
V současné době nepodporujeme přímou federaci s více doménami od stejného klienta.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>Můžu nastavit přímou federaci s doménou, pro kterou existuje nespravovaný klient (ověřený e-mailem)? 
Ano. Pokud doména nebyla ověřena a tenant neprošel [převzetím správce](../users-groups-roles/domains-admin-takeover.md), můžete s ní nastavit přímou federaci. Nespravované, nebo e-mailem ověřené, tenanty jsou vytvořeny, když uživatel uplatní pozvání B2B nebo provede samoobslužné registrace pro Azure AD pomocí domény, která v současné době neexistuje. S těmito doménami můžete nastavit přímou federaci. Pokud se pokusíte nastavit přímou federaci s doménou ověřenou DNS, ať už na webu Azure Portal nebo přes PowerShell, zobrazí se chyba.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Pokud jsou povoleny přímé federace a e-mailem jednorázové ověřování přístupového kódu, která metoda má přednost?
Pokud je vytvořena přímá federace s partnerskou organizací, má přednost před ověřením jednorázového hesla e-mailem pro nové uživatele typu Host z této organizace. Pokud uživatel typu Host uplatnil pozvánku pomocí jednorázového ověřování pomocí hesla před nastavením přímé federace, bude nadále používat jednorázové ověřování pomocí hesla. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>Řeší přímé federační problémy s přihlášením z důvodu částečně synchronizovaného nájmu?
Ne, v tomto scénáři by měla být použita funkce [jednorázového hesla e-mailu.](one-time-passcode.md) "Částečně synchronizované nájmy" odkazuje na partnera klienta Azure AD, kde místní identity uživatelů nejsou plně synchronizovány do cloudu. Host, jehož identita ještě v cloudu neexistuje, ale který se pokusí uplatnit vaši pozvánku b2B, se nebude moct přihlásit. Funkce jednorázového přístupového kódu by tomuto hostu umožnila přihlásit se. Funkce přímé federace řeší scénáře, kde host má svůj vlastní účet organizace spravované idp, ale organizace nemá přítomnost Azure AD vůbec.

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>Krok 1: Konfigurace zprostředkovatele identity partnerské organizace
Nejprve musí partnerská organizace nakonfigurovat svého poskytovatele identity s požadovanými deklaracemi identit a důvěryhodnými certifikáty předávající strany. 

> [!NOTE]
> Pro ilustraci konfigurace zprostředkovatele identity pro přímou federaci použijeme jako příklad službu AD FS (Active Directory Federation Services). Viz článek [Konfigurace přímé federace se službou AD FS](direct-federation-adfs.md), který uvádí příklady konfigurace služby AD FS jako zprostředkovatele identity SAML 2.0 nebo WS-Fed v rámci přípravy na přímou federaci.

### <a name="saml-20-configuration"></a>Konfigurace SAML 2.0

Azure AD B2B lze nakonfigurovat tak, aby federate s poskytovateli identity, které používají protokol SAML se specifickými požadavky uvedenými níže. Další informace o nastavení vztahu důvěryhodnosti mezi poskytovatelem identity SAML a službou Azure AD najdete v [tématu Použití zprostředkovatele identity SAML 2.0 (IdP) pro jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-saml-idp).  

> [!NOTE]
> Cílová doména pro přímé federace nesmí být dns ověřena ve službě Azure AD. Doména url ověřování musí odpovídat cílové doméně nebo musí být doménou povoleného zprostředkovatele identity. Podrobnosti najdete v části [Omezení.](#limitations) 

#### <a name="required-saml-20-attributes-and-claims"></a>Požadované atributy a deklarace identity SAML 2.0
V následujících tabulkách jsou uvedeny požadavky na konkrétní atributy a deklarace identity, které musí být nakonfigurovány u jiného poskytovatele identity. Chcete-li nastavit přímé federace, musí být přijata následující atributy v odpovědi SAML 2.0 od poskytovatele identity. Tyto atributy lze nakonfigurovat propojením se souborem XML služby tokenů zabezpečení online nebo ručním zadáním.

Povinné atributy pro odpověď SAML 2.0 z IdP:

|Atribut  |Hodnota  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Cílová skupina     |`urn:federation:MicrosoftOnline`         |
|Vystavitel     |Identifikátor URI vystaviteli partnerského idp, například`http://www.example.com/exk10l6w90DHM0yi...`         |


Požadované deklarace identity tokenu SAML 2.0 vydaného idp:

|Atribut  |Hodnota  |
|---------|---------|
|NameID formát     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|Emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>Konfigurace WS-Fed 
Azure AD B2B lze nakonfigurovat tak, aby federate s poskytovateli identity, které používají protokol WS Fed s některé konkrétní požadavky, jak je uvedeno níže. V současné době dva poskytovatelé WS-Fed byly testovány na kompatibilitu s Azure AD patří AD FS a Shibboleth. Další informace o vytvoření vztahu důvěryhodnosti předávající strany mezi poskytovatelem kompatibilním s WS-Fed se službou Azure AD najdete v tématu "StS Integration Paper using WS Protocols", který je k dispozici v [dokumentech kompatibility zprostředkovatele identity Azure AD](https://www.microsoft.com/download/details.aspx?id=56843).

> [!NOTE]
> Cílová doména pro přímé federace nesmí být dns ověřena ve službě Azure AD. Doména url ověřování musí odpovídat cílové doméně nebo doméně povoleného zprostředkovatele identity. Podrobnosti najdete v části [Omezení.](#limitations) 

#### <a name="required-ws-fed-attributes-and-claims"></a>Požadované atributy a nároky WS-Fed

V následujících tabulkách jsou uvedeny požadavky na konkrétní atributy a deklarace identity, které musí být nakonfigurovány u poskytovatele identity WS-Fed třetí strany. Chcete-li nastavit přímou federaci, musí být ve zprávě WS-Fed od poskytovatele identity přijaty následující atributy. Tyto atributy lze nakonfigurovat propojením se souborem XML služby tokenů zabezpečení online nebo ručním zadáním.

Povinné atributy ve zprávě WS-Fed z IdP:
 
|Atribut  |Hodnota  |
|---------|---------|
|PasivníPožadavek     |`https://login.microsoftonline.com/login.srf`         |
|Cílová skupina     |`urn:federation:MicrosoftOnline`         |
|Vystavitel     |Identifikátor URI vystaviteli partnerského idp, například`http://www.example.com/exk10l6w90DHM0yi...`         |

Požadované nároky pro token WS-Fed vydaný idp:

|Atribut  |Hodnota  |
|---------|---------|
|ID neměnné     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|Emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>Krok 2: Konfigurace přímé federace ve službě Azure AD 
Dále nakonfigurujete federaci s poskytovatelem identity nakonfigurovaným v kroku 1 ve službě Azure AD. Můžete použít portál Azure AD nebo PowerShell. Může trvat 5-10 minut, než se projeví zásady přímé federace. Během této doby se nepokoušejte uplatnit pozvánku pro přímou federační doménu. Jsou vyžadovány následující atributy:
- Identifikátor URI vystavitela partnera IdP
- Koncový bod pasivního ověřování ipp partnera (podporuje se pouze https)
- Certifikát

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Konfigurace přímé federace na portálu Azure AD

1. Přejděte na [portál Azure](https://portal.azure.com/). V levém podokně vyberte **Azure Active Directory**. 
2. Vyberte **organizační vztahy**.
3. Vyberte **zprostředkovatele identity**a pak vyberte **nový zprostředkovatel služby IDP služby SAML/WS-Fed**.

    ![Snímek obrazovky s tlačítkem pro přidání nového idp SAML nebo WS-Fed](media/direct-federation/new-saml-wsfed-idp.png)

4. Na stránce **New SAML/WS-Fed IdP** v yberte v části **Protokol zprostředkovatele identity**možnost **SAML** nebo **WS-FED**.

    ![Snímek obrazovky s tlačítkem analýzy na stránce idp SAML nebo WS-Fed](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Zadejte název domény partnerské organizace, která bude cílovým názvem domény pro přímou federaci.
6. Můžete nahrát soubor metadat k vyplnění podrobností metadat. Pokud se rozhodnete zadat metadata ručně, zadejte následující informace:
   - Název domény partnera IdP
   - ID entity partnerského IdP
   - Pasivní koncový bod osorpartnera IdP
   - Certifikát
   > [!NOTE]
   > Adresa URL metadat je volitelná, ale důrazně ji doporučujeme. Pokud zadáte adresu URL metadat, Azure AD můžete automaticky obnovit podpisový certifikát, když vyprší jeho platnost. Pokud je certifikát z jakéhokoli důvodu otočen před vypršením platnosti nebo pokud nezadáte adresu URL metadat, azure ad nebude možné jej obnovit. V takovém případě budete muset podpisový certifikát aktualizovat ručně.

7. Vyberte **Uložit**. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>Konfigurace přímé federace ve službě Azure AD pomocí PowerShellu

1. Nainstalujte nejnovější verzi modulu Azure AD PowerShell pro graf[(AzureADPreview).](https://www.powershellgallery.com/packages/AzureADPreview) (Pokud potřebujete podrobné kroky, úvodní k dispozici pro přidání uživatele typu Host obsahuje část [Instalace nejnovějšího modulu AzureADPreview](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module).) 
2. Spusťte následující příkaz: 
   ```powershell
   Connect-AzureAD
   ```
1. Na výzvu k přihlášení se přihlaste pomocí spravovaného účtu globálního správce. 
2. Spusťte následující příkazy a nahrazte hodnoty ze souboru metadat federace. Pro server služby AD FS a okta je federační `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`soubor federationmetadata.xml, například: . 

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

## <a name="step-3-test-direct-federation-in-azure-ad"></a>Krok 3: Testování přímé federace ve službě Azure AD
Nyní otestujte nastavení přímé federace pozváním nového uživatele typu Host B2B. Podrobnosti najdete v tématu [Přidání uživatelů spolupráce Azure AD B2B na webu Azure Portal](add-users-administrator.md).
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Jak lze upravit přímý federační vztah?

1. Přejděte na [portál Azure](https://portal.azure.com/). V levém podokně vyberte **Azure Active Directory**. 
2. Vyberte **organizační vztahy**.
3. Vybrat **zprostředkovatele identity**
4. V části **SAML/WS-Fed zprostředkovatele identity**vyberte poskytovatele.
5. V podokně podrobností zprostředkovatele identity aktualizujte hodnoty.
6. Vyberte **Uložit**.


## <a name="how-do-i-remove-direct-federation"></a>Jak mohu odstranit přímou federaci?
Můžete odebrat nastavení přímé federace. Pokud tak učiníte, uživatelé typu host federace, kteří už pozvánky uplatnili, se nebudou moci přihlásit. Můžete jim však znovu udělit přístup k prostředkům jejich odstraněním z adresáře a jejich opětovným pozváním. Odebrání přímé federace s poskytovatelem identity na portálu Azure AD:

1. Přejděte na [portál Azure](https://portal.azure.com/). V levém podokně vyberte **Azure Active Directory**. 
2. Vyberte **organizační vztahy**.
3. Vyberte **zprostředkovatele identity**.
4. Vyberte zprostředkovatele identity a pak vyberte **Odstranit**. 
5. Chcete-li odstranění potvrdit, vyberte **možnost Ano.** 

Odebrání přímé federace s poskytovatelem identity pomocí PowerShellu:
1. Nainstalujte nejnovější verzi modulu Azure AD PowerShell pro graf[(AzureADPreview).](https://www.powershellgallery.com/packages/AzureADPreview)
2. Spusťte následující příkaz: 
   ```powershell
   Connect-AzureAD
   ```
3. Na výzvu k přihlášení se přihlaste pomocí spravovaného účtu globálního správce. 
4. Zadejte následující příkaz:
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```

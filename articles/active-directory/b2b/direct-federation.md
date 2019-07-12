---
title: Nastavení federace s přímým přístupem pomocí zprostředkovatele identity pro B2B – Azure Active Directory | Dokumentace Microsoftu
description: Přímo vytvořit federaci s zprostředkovatele identity SAML nebo WS-Fed, abyste mohli hosté přihlásit do aplikace Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bc3c1325e8379082134e2cbec1586f7d338ee61
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797938"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Přímé federace se službou AD FS a poskytovatelů třetích stran pro uživatele typu Host (preview)
|     |
| --- |
| Federace s přímým přístupem je funkce ve verzi public preview služby Azure Active Directory. Další informace o verzích Preview najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Tento článek popisuje, jak nastavit přímé federace s jinou organizací spolupráci B2B. Můžete nastavit přímé federace se službou každá organizace má zprostředkovatele identity (IdP) podporuje protokol SAML 2.0 nebo WS-Fed.
Při nastavování přímé federace se službou zprostředkovatele identity partnerem nové uživatele typu Host z této domény můžete použít vlastní účet organizace spravovat zprostředkovatele identity pro přihlášení k tenantovi Azure AD a začněte spolupracovat s vámi. Není nutné pro uživatele typu Host, chcete-li vytvořit samostatný účet Azure AD.
> [!NOTE]
> Přímé federace uživatele typu Host musíte se přihlásit pomocí odkazu, který zahrnuje kontextu tenanta (například `https://myapps.microsoft.com/?tenantid=<tenant id>` nebo `https://portal.azure.com/<tenant id>`, nebo v případě ověřenou doménu, `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com`). Přímé odkazy k aplikacím a prostředkům také fungovat tak dlouho, dokud zahrnují kontextu tenanta. Uživatelé federace s přímým přístupem jsou aktuálně nejde se přihlásit pomocí běžných koncových bodů, které nemají žádný kontext klienta. Například použití `https://myapps.microsoft.com`, `https://portal.azure.com`, nebo `https://teams.microsoft.com` dojde chybě.
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>Když je ověření uživatele typu Host s federací s přímým přístupem?
Po nastavení federace s přímým přístupem s organizací se žádné nové uživatele typu Host, pozvaní ověřovat využívající federaci s přímým přístupem. Je důležité si uvědomit, že nastavení federace s přímým přístupem nedojde ke změně metody ověření pro uživatele typu Host, kteří už využili pozvánku od vás. Následuje několik příkladů:
 - Pokud uživatelé typu Host už využili pozvánky a následně nastavit federace s přímým přístupem ve své organizaci, tyto uživatele typu Host bude nadále používat stejnou metodu ověřování, který se použije dřív, než se nastavení federace s přímým přístupem.
 - Pokud nastavení s přímým přístupem federace se službou partnerské organizace a pozvat uživatele typu Host, a později partnerské organizace je následně přesunuto do služby Azure AD, uživatele typu Host, kteří už využili pozvánky bude nadále používat federace s přímým přístupem, co nejdelší přímou zásada federace ve vašem tenantovi existuje.
 - Při odstranění přímé federace se službou partnerské organizace, budou všechny uživatele typu Host využívající federaci s přímým přístupem se aktuálně nejde se přihlásit.

V některém z těchto scénářů můžete aktualizovat metodu ověřování uživatele typu Host z adresáře odstranit uživatelský účet guest a reinviting je.

Federace s přímým přístupem je vázán na obory názvů domény, například contoso.com a fabrikam.com. Při vytváření konfigurace s přímým přístupem federační službou AD FS nebo zprostředkovatele identity třetí strany, organizace přidružit jeden nebo více oborů názvů domény do těchto zprostředkovatelů identity. 

## <a name="end-user-experience"></a>Činnost koncového uživatele 
S přímým přístupem federací přihlášení uživatele typu Host do tenanta Azure AD pomocí vlastní účet organizace. Když se přístup ke sdíleným prostředkům a výzva pro přihlášení, federace s přímým přístupem uživatelé přesměrovaní na jejich zprostředkovatele identity. Po úspěšném přihlášení se vrátí do služby Azure AD pro přístup k prostředkům. Přímé federace uživatelé obnovovací tokeny jsou platné po dobu 12 hodin [výchozí délka pro průchozí obnovovací token](../develop/active-directory-configurable-token-lifetimes.md#exceptions) ve službě Azure AD. Pokud federované IdP má povoleno jednotné přihlašování, uživateli budou moct používat jednotné přihlašování a nezobrazí libovolného řádku přihlašování po počáteční ověřování.

## <a name="limitations"></a>Omezení

### <a name="dns-verified-domains-in-azure-ad"></a>Ověření DNS domény v Azure AD
Přímé federace je povoleno pouze pro domény, které jsou ***není*** DNS ověřit ve službě Azure AD. Federace s přímým přístupem je povolen pro nespravovaný (ověřený e-mailem nebo "virální") Azure AD klienty, protože nejsou ověřeny DNS.
### <a name="authentication-url"></a>Adresa URL pro ověřování
Federace s přímým přístupem je povoleno pouze pro zásady, kde doména adresy URL ověřování odpovídá cílové doméně, nebo kde ověřovací adresa URL je jedním z těchto zprostředkovatelů identity povolené (Tento seznam se může změnit):
-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

Například při nastavování přímé federaci pro **fabrikam.com**, adresy URL ověřování `https://fabrikam.com/adfs` budou úspěšné ověření. Hostitel ve stejné doméně také předá, například `https://sts.fabrikam.com/adfs`. Ale ověřovací adresa URL `https://fabrikamconglomerate.com/adfs` nebo `https://fabrikam.com.uk/adfs` pro stejné domény nebudou předávat.

### <a name="signing-certificate-renewal"></a>Prodloužit platnost podpisového certifikátu
Pokud zadáte adresu URL metadat v nastavení zprostředkovatele identity, Azure AD po jeho vypršení automaticky obnovit podpisový certifikát. Ale pokud tento certifikát je otočený z jakéhokoli důvodu před časem vypršení platnosti nebo pokud nezadáte adresa URL metadat, Azure AD nebude možné ho obnovíte. V takovém případě budete muset ručně aktualizovat podpisový certifikát.
## <a name="frequently-asked-questions"></a>Nejčastější dotazy
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>Můžete nastavit přímé federace s doménou, pro kterou existuje nespravovaného tenanta (ověřený e-mailem)? 
Ano. Pokud doména není ověřená a neprošlo tenanta [převzetí správce](../users-groups-roles/domains-admin-takeover.md), můžete nastavit federace s přímým přístupem. Nespravované nebo ověřit e-mailové klienty vytvářejí, když uživatel uplatňuje B2B pozvání nebo provede samoobslužnou registraci pro službu Azure AD používáte doménu, která aktuálně neexistuje. Můžete nastavit přímé federace se službou těchto domén. Pokud se pokusíte nastavit přímé federace se službou ověření DNS domény, na webu Azure Portal nebo pomocí Powershellu, zobrazí se vám chyba.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Pokud federace s přímým přístupem a ověřování jednorázovým heslem e-mailu jsou povoleny, která metoda má přednost?
Po vytvoření federace s přímým přístupem se partnerské organizace, má přednost před ověřování jednorázovým heslem e-mailu pro nové uživatele typu Host z této organizace. Pokud uživatel typu Host uplatnit pozvání před nastavením federace s přímým přístupem pomocí ověřování jednorázovým heslem, budete dál používat ověřování jednorázovým heslem. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>Přímé federace adresu problémy s přihlašováním kvůli částečně synchronizovaná tenantů?
Ne, [e-mailu jednorázové heslo](one-time-passcode.md) funkce by měla sloužit v tomto scénáři. "Částečně synchronizovaná tenantů" odkazuje na tenanta Azure AD partnera, kde nejsou místních identit uživatelů synchronizované plně do cloudu. Hosta, jehož identita ještě neexistuje v cloudu, ale kteří se pokusí uplatnění pozvání B2B nebudou moct přihlásit. Funkce jednorázové heslo by mohl tento hosta k přihlášení. Funkci federace s přímým přístupem řeší scénáře, kdy hosta má své vlastní účet organizace spravovat zprostředkovatele identity, ale organizace nemá žádné přítomnost služby Azure AD.

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>Krok 1: Nakonfigurovat partnerské organizaci zprostředkovatele identity
Nejprve musí vaše organizace partnera svého zprostředkovatele identity nakonfigurovat požadované deklarace identit a vztahy důvěryhodnosti předávající strany. 

> [!NOTE]
> Pro ilustraci, jak nakonfigurovat zprostředkovatele identity pro federaci s přímým přístupem, použijeme jako příklad služby Active Directory Federation Services (AD FS). Přečtěte si článek [konfigurace s přímým přístupem federace se službou AD FS](direct-federation-adfs.md), které jsou uvedené příklady, jak nakonfigurovat službu AD FS jako SAML 2.0 nebo WS-Fed zprostředkovatele identity v rámci přípravy pro federaci s přímým přístupem.

### <a name="saml-20-configuration"></a>Konfigurace SAML 2.0

Azure AD B2B je možné nakonfigurovat pro vytvoření federace pomocí zprostředkovatelů identity, které používají protokol SAML s konkrétní požadavky uvedené níže. Další informace o nastavení vztahu důvěryhodnosti mezi SAML zprostředkovatele identity a Azure AD najdete v tématu [pomocí zprostředkovatele Identity (IdP) 2.0 SAML pro jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-saml-idp).  

> [!NOTE]
> Poznámka: cílové domény pro federování s přímým přístupem nesmí být ověření DNS na Azure AD. Doména adresy URL ověřování musí odpovídat cílové domény nebo ho musí představovat doménu samotného poskytovatele povolené. Zobrazit [omezení](#limitations) podrobné informace. 

#### <a name="required-saml-20-attributes-and-claims"></a>Povinné atributy SAML 2.0 a deklarace identity
Následující tabulky popisují požadavky na konkrétní atributy a deklarace identity, které musí být nakonfigurované u zprostředkovatele identity třetí strany. Nastavení federace s přímým přístupem, musí být následující atributy dostali odpověď SAML 2.0, od poskytovatele identity. Tyto atributy lze nastavit odkazování na soubor XML služby tokenů zabezpečení online nebo ručním zadáním.

Vyžadované atributy pro odpověď SAML 2.0 ze zprostředkovatele identity:

|Atribut  |Value  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Cílová skupina     |`urn:federation:MicrosoftOnline`         |
|Vystavitel     |Identifikátor URI partnera zprostředkovatele identity, například vystavitele `http://www.example.com/exk10l6w90DHM0yi...`         |


Požadované deklarace identit pro token SAML 2.0, Vystavitel zprostředkovatele identity:

|Atribut  |Value  |
|---------|---------|
|Formát ID názvu položky     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>Konfigurace WS-Fed 
Azure AD B2B je možné nakonfigurovat pro vytvoření federace pomocí zprostředkovatelů identity, které používají protokol WS-Fed některé konkrétní požadavky, jak je uvedeno níže. V současné době dva poskytovatelé WS-Fed jsme otestovali kompatibilitu s Azure AD zahrnují služby AD FS a Shibboleth. Další informace o vytvoření vztahu důvěryhodnosti předávající strany mezi zprostředkovateli WS-Fed kompatibilní s Azure AD, najdete v části "Integrace služby tokenů zabezpečení papíru pomocí protokolů WS" k dispozici v [dokumentů Azure AD Identity poskytovatele kompatibility](https://www.microsoft.com/download/details.aspx?id=56843).

> [!NOTE]
> Cílové domény pro federování s přímým přístupem nesmí být ověření DNS na Azure AD. Doména adresy URL ověřování musí odpovídat cílové domény nebo domény poskytovatele povolené. Zobrazit [omezení](#limitations) podrobné informace. 

#### <a name="required-ws-fed-attributes-and-claims"></a>Požadované WS-Fed atributech a deklaracích identit

Následující tabulky popisují požadavky na konkrétní atributy a deklarace identity, které musí být nakonfigurované u zprostředkovatele identity WS-Fed třetích stran. Nastavení federace s přímým přístupem, musí být přijata následující atributy ve zprávě WS-Fed od poskytovatele identity. Tyto atributy lze nastavit odkazování na soubor XML služby tokenů zabezpečení online nebo ručním zadáním.

Vyžadované atributy ve zprávě WS-Fed ze zprostředkovatele identity:
 
|Atribut  |Value  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Cílová skupina     |`urn:federation:MicrosoftOnline`         |
|Vystavitel     |Identifikátor URI partnera zprostředkovatele identity, například vystavitele `http://www.example.com/exk10l6w90DHM0yi...`         |

Požadované deklarace identit pro token WS-Fed Vystavitel zprostředkovatele identity:

|Atribut  |Value  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>Krok 2: Konfigurace federace s přímým přístupem ve službě Azure AD 
V dalším kroku budete konfigurace federace s poskytovatelem identity ve službě Azure AD nakonfigurovali v kroku 1. Můžete použít buď na portálu Azure AD nebo prostředí PowerShell. Může trvat 5 až 10 minut, než zásady federace s přímým přístupem se projeví. Během této doby Nepokoušejte se uplatnit pozvánku s přímým přístupem federace domény. Vyžadují se následující atributy:
- Vystavitel URI partnera zprostředkovatele identity
- Koncový bod pasivního ověřování partnera zprostředkovatele identity (podporuje se pouze https)
- Certifikát

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Ke konfiguraci federace s přímým přístupem na portálu Azure AD

1. Přejděte na [Azure Portal](https://portal.azure.com/). V levém podokně vyberte **Azure Active Directory**. 
2. Vyberte **organizační vztahy**.
3. Vyberte **zprostředkovatelé Identity**a pak vyberte **nové SAML/WS-Fed IdP**.

    ![Snímek obrazovky zobrazující tlačítko pro přidání nové SAML nebo WS-Fed IdP](media/direct-federation/new-saml-wsfed-idp.png)

4. Na **nové SAML/WS-Fed IdP** stránce v části **protokol zprostředkovatele Identity**vyberte **SAML** nebo **WS-FED**.

    ![Snímek obrazovky zobrazující tlačítko analýzy na stránce SAML nebo WS-Fed IdP](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Zadejte název domény vaší organizace partnera, který může mít název cílové domény pro federování s přímým přístupem
6. Můžete nahrát soubor metadat k naplnění podrobnosti metadat. Pokud budete chtít ručně vstupní metadata, zadejte následující informace:
   - Název domény partnerského serveru zprostředkovatele identity
   - ID entity partnera zprostředkovatele identity
   - Koncový bod pasivního žadatele partnera zprostředkovatele identity
   - Certifikát
   > [!NOTE]
   > Adresa URL metadat je volitelný, ale důrazně doporučujeme ji. Pokud zadáte adresu URL metadat, Azure AD můžou automaticky obnovit podpisový certifikát po jeho vypršení. Pokud tento certifikát je otočený z jakéhokoli důvodu před časem vypršení platnosti nebo pokud nezadáte adresa URL metadat, Azure AD nebude možné ho obnovit. V takovém případě budete muset ručně aktualizovat podpisový certifikát.

7. Vyberte **Uložit**. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>Ke konfiguraci federace s přímým přístupem ve službě Azure AD pomocí Powershellu

1. Nainstalujte nejnovější verzi Azure AD PowerShell pro modul grafu ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)). (Pokud potřebujete podrobné kroky, rychlý start pro přidání uživatele typu Host zahrnuje v části [nainstalovat nejnovější modulu AzureADPreview](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module).) 
2. Spusťte následující příkaz: 
   ```powershell
   Connect-AzureAD
   ```
1. Na řádku přihlášení Přihlaste se pomocí spravovaný účet globálního správce. 
2. Spusťte následující příkazy, nahraďte hodnoty ze souboru metadat federace. Pro Server služby AD FS a Okta federace je soubor federationmetadata.xml, například: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`. 

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

## <a name="step-3-test-direct-federation-in-azure-ad"></a>Krok 3: Testování s přímým přístupem federace ve službě Azure AD
Teď otestujte nastavení federace s přímým přístupem pozváním nový uživatel typu Host B2B. Podrobnosti najdete v tématu [uživatele spolupráce B2B Azure AD přidat na portálu Azure portal](add-users-administrator.md).
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Jak mohu upravit vztah s přímým přístupem federace?

1. Přejděte na [Azure Portal](https://portal.azure.com/). V levém podokně vyberte **Azure Active Directory**. 
2. Vyberte **organizační vztahy**.
3. Vyberte **zprostředkovatelů Identity**
4. V části **poskytovatele identity SAML/WS-Fed**, vyberte zprostředkovatele.
5. V podokně podrobností zprostředkovatele identity aktualizujte hodnoty.
6. Vyberte **Uložit**.


## <a name="how-do-i-remove-direct-federation"></a>Jak odeberu federace s přímým přístupem?
Můžete odebrat nastavení federace s přímým přístupem. Pokud tak učiníte, nebudou moct přihlásit s přímým přístupem federace uživatele typu Host, kteří už mají uplatnit své pozvánky. Ale můžete poskytnout jim přístup k vašim prostředkům znovu tak, že jejich odstranění z adresáře a jejich reinviting. Odebrat na portálu Azure AD s přímým přístupem federace pomocí zprostředkovatele identity:

1. Přejděte na [Azure Portal](https://portal.azure.com/). V levém podokně vyberte **Azure Active Directory**. 
2. Vyberte **organizační vztahy**.
3. Vyberte **zprostředkovatelé Identity**.
4. Vyberte zprostředkovatele identity a pak vyberte **odstranit**. 
5. Vyberte **Ano** potvrďte odstranění. 

Na odebrání přímé federace se službou zprostředkovatelů identity pomocí prostředí PowerShell:
1. Nainstalujte nejnovější verzi Azure AD PowerShell pro modul grafu ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Spusťte následující příkaz: 
   ```powershell
   Connect-AzureAD
   ```
3. Na řádku přihlášení Přihlaste se pomocí spravovaný účet globálního správce. 
4. Zadejte následující příkaz:
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```

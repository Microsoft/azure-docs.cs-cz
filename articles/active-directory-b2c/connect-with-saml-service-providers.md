---
title: Konfigurace Azure AD B2C jako SAML IdP pro vaše aplikace
title-suffix: Azure AD B2C
description: Jak nakonfigurovat Azure AD B2C poskytovat kontrolní výrazy protokolu SAML pro vaše aplikace (poskytovatele služeb). Azure AD B2C bude fungovat jako jediný zprostředkovatel identity (IdP) pro vaši aplikaci SAML.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: a72b5b50daaae33336de9caab5202c2bf42f5c15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051618"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Registrace aplikace SAML v Azure AD B2C

V tomto článku se dozvíte, jak nakonfigurovat Azure Active Directory B2C (Azure AD B2C) tak, aby fungovat jako zprostředkovatel evičník kontrolního výrazu zabezpečení (SAML) zprostředkovatelidentity (IdP) do vašich aplikací.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="scenario-overview"></a>Přehled scénáře

Organizace, které používají Azure AD B2C jako své řešení pro správu identity zákazníka a přístupu, mohou vyžadovat interakci s poskytovateli identit nebo aplikacemi, které jsou nakonfigurované k ověřování pomocí protokolu SAML.

Azure AD B2C dosahuje spolupráce SAML jedním ze dvou způsobů:

* Tím, že jedná jako *poskytovatel identity* (IdP) a dosažení jednotného přihlašování (SSO) s poskytovateli služeb založených na SAML (vaše aplikace)
* Tím, že jednájako *poskytovatel služeb* (SP) a spolupracuje s poskytovateli identit založených na SAML, jako jsou Salesforce a ADFS

![Diagram s B2C jako poskytovatelem identity vlevo a B2C jako poskytovateleslužeb vpravo](media/saml-identity-provider/saml-idp-diagram-01.jpg)

Shrnutí dvou nevýhradních základních scénářů pomocí saml:

| Scénář | Role Azure AD B2C | Postupy |
| -------- | ----------------- | ------- |
| Moje aplikace očekává, že kontrolní výraz SAML dokončí ověřování. | **Azure AD B2C funguje jako zprostředkovatel identity (IdP)**<br />Azure AD B2C funguje jako SAML IdP pro aplikace. | Tento článek. |
| Moji uživatelé potřebují jednotné přihlašování s poskytovatelem identity kompatibilním se standardem SAML, jako jsou ADFS, Salesforce nebo Shibboleth.  | **Azure AD B2C funguje jako poskytovatel služeb (SP)**<br />Azure AD B2C funguje jako poskytovatel služeb při připojování k poskytovateli identity SAML. Je to proxy federace mezi vaší aplikací a poskytovatelem identity SAML.  | <ul><li>[Nastavení přihlášení pomocí služby ADFS jako idp saml pomocí vlastních zásad](identity-provider-adfs2016-custom.md)</li><li>[Nastavení přihlášení u poskytovatele služby Salesforce SAML pomocí vlastních zásad](identity-provider-salesforce-custom.md)</li></ul> |

## <a name="prerequisites"></a>Požadavky

* Dokončete kroky v [začínáme s vlastní mi se zásadami v Azure AD B2C](custom-policy-get-started.md). Potřebujete vlastní zásady *SocialAndLocalAccounts* z vlastního balíčku zásad pro začátečníky popsané v článku.
* Základní znalost protokolu jazyka ZNAČEK zabezpečení (SAML).
* Webová aplikace nakonfigurovaná jako poskytovatel služeb SAML (SP). Pro účely tohoto kurzu můžete použít [testovací aplikaci SAML,][samltest] kterou poskytujeme.

## <a name="components-of-the-solution"></a>Součásti roztoku

Pro tento scénář jsou vyžadovány tři hlavní součásti:

* **Saml poskytovatel služeb** s možností odesílat požadavky SAML a přijímat, dekódovat a reagovat na kontrolní výrazy SAML z Azure AD B2C. To to je také známé jako předávající strana.
* Veřejně dostupný **koncový bod metadat** SAML pro vašeho poskytovatele služeb.
* [Klient Azure AD B2C](tutorial-create-tenant.md)

Pokud ještě nemáte poskytovatele služeb SAML a přidružený koncový bod metadat, můžete použít tuto ukázkovou aplikaci SAML, kterou jsme zpřístupnili k testování:

[Testovací aplikace SAML][samltest]

## <a name="1-set-up-certificates"></a>1. Nastavení certifikátů

Chcete-li vytvořit vztah důvěryhodnosti mezi poskytovatelem služeb a Azure AD B2C, musíte poskytnout certifikáty webové aplikace X509.

* **Certifikáty poskytovatele služeb**
  * Certifikát s privátním klíčem uloženým ve webové aplikaci. Tento certifikát používá váš poskytovatel služeb k podepsání požadavku SAML odeslaného do Azure AD B2C. Azure AD B2C přečte veřejný klíč z metadat poskytovatele služeb k ověření podpisu.
  * (Nepovinné) Certifikát s privátním klíčem uloženým ve webové aplikaci. Azure AD B2C čte veřejný klíč z metadat poskytovatele služeb k šifrování kontrolního výrazu SAML. Poskytovatel služeb pak použije soukromý klíč k dešifrování kontrolního výrazu.
* **Certifikáty Azure AD B2C**
  * Certifikát s privátním klíčem ve službě Azure AD B2C. Tento certifikát používá Azure AD B2C k podepsání odpovědi SAML odeslané vašemu poskytovateli služeb. Váš poskytovatel služeb přečte veřejný klíč metadat Azure AD B2C k ověření podpisu odpovědi SAML.

Můžete použít certifikát vydaný autoritou veřejného certifikátu nebo v tomto kurzu certifikát podepsaný svým držitelem.

### <a name="11-prepare-a-self-signed-certificate"></a>1.1 Příprava certifikátu podepsaného svým držitelem

Pokud ještě nemáte certifikát, můžete použít certifikát podepsaný svým držitelem pro tento kurz. V systému Windows můžete ke generování certifikátu použít rutinu [New-SelfSignedCertificate aplikace](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) PowerShell.

1. Spusťte tento příkaz prostředí PowerShell a vygenerujte certifikát podepsaný svým držitelem. Upravte `-Subject` argument podle potřeby pro vaši aplikaci a název klienta Azure AD B2C. Můžete také upravit `-NotAfter` datum a určit pro certifikát jiné vypršení platnosti.

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. Otevřít **Spravovat uživatelské certifikáty** > aktuální**osobní** > **certifikáty** > **uživatelů** > *yourappname.yourtenant.onmicrosoft.com*
1. Výběr certifikátu > **akce** > **Export všech úkolů** > **Export**
1. Vyberte **Ano** > **Další** > **Ano, exportovat soukromý klíč** > **Další**
1. Přijmout výchozí hodnoty pro **export ovat formát souboru**
1. Zadání hesla pro certifikát

### <a name="12-upload-the-certificate"></a>1.2 Nahrání certifikátu

Dále nahrajte kontrolní výraz SAML a podpisový certifikát odpovědi do Azure AD B2C.

1. Přihlaste se k [portálu Azure a](https://portal.azure.com) přejděte do svého tenanta Azure AD B2C.
1. V části **Zásady**vyberte **rozhraní Identity Experience Framework** a potom **klávesy policy**.
1. Vyberte **Přidat**a pak vyberte **Možnosti** > **nahrání**.
1. Zadejte **název**, například *SamlIdpCert*. Předpona *B2C_1A_* se automaticky přidá k názvu klíče.
1. Nahrajte certifikát pomocí ovládacího prvku pro nahrávání souborů.
1. Zadejte heslo certifikátu.
1. Vyberte **Vytvořit**.
1. Ověřte, zda se klíč zobrazí očekávaným způsobem. Například *B2C_1A_SamlIdpCert*.

## <a name="2-prepare-your-policy"></a>2. Připravte si svou politiku

### <a name="21-create-the-saml-token-issuer"></a>2.1 Vytvoření vydavatele tokenu SAML

Nyní přidejte možnost pro vašeho tenanta k vydávání tokenů SAML pomocí [saml tokeny vystavitele](saml-issuer-technical-profile.md) a [SAML poskytovatel relace](custom-policy-reference-sso.md#samlssosessionprovider) technické profily.

Otevřete `SocialAndLocalAccounts\` **`TrustFrameworkExtensions.xml`** ve vlastním balíčku zásad pro začátečníky.

Vyhledejte `<ClaimsProviders>` oddíl a přidejte následující úryvek XML.

Můžete změnit hodnotu `IssuerUri` metadat. Toto je identifikátor URI vystavittele, který je vrácen v odpovědi SAML z Azure AD B2C. Aplikace předávající strany by měla být nakonfigurována tak, aby při ověřování kontrolních výrazů SAML přijímala identifikátor URI vystavitho certifikátu.

```XML
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="None"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <!-- The issuer contains the policy name; it should be the same name as configured in the relying party application. B2C_1A_signup_signin_SAML is used below. -->
        <!--<Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>-->
      </Metadata>
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-sp"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-sp">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="3-add-the-saml-relying-party-policy"></a>3. Přidejte zásady předávající strany SAML

Teď, když váš tenant může vydávat kontrolní výrazy SAML, musíte vytvořit zásady předávající strany SAML a upravit cestu uživatele tak, aby namísto JWT vydal kontrolní výraz SAML.

### <a name="31-create-sign-up-or-sign-in-policy"></a>3.1 Vytvoření zásad registrace nebo přihlášení

1. Vytvořte kopii souboru *SignUpOrSignin.xml* v pracovním adresáři počátečního balíčku a uložte jej s novým názvem. Například *SignUpOrSigninSAML.xml*. Toto je soubor zásad předávající strany.

1. Otevřete soubor *SignUpOrSigninSAML.xml* v upřednostňovaném editoru.

1. Změňte `PolicyId` `PublicPolicyUri` zásady a zásady na _B2C_1A_signup_signin_saml_ a `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml` jak je vidět níže.

    ```XML
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. Těsně před `<RelyingParty>` element přidejte následující fragment XML. Tento kód XML přepíše krok orchestrace číslo 7 cesty uživatele _SignUpOrSignIn._ Pokud jste začali z jiné složky ve startovacím balíčku nebo jste svou cestu uživatele přizpůsobili `order` přidáním nebo odebráním kroků orchestrace, ujistěte se, že číslo (v prvku) je zarovnáno `LocalAccounts`s `SocialAccounts` číslem `SocialAndLocalAccountsWithMfa`zadaným v cestě uživatele pro krok vystavittele tokenu (například v ostatních složkách počátečního balíčku je to krok číslo 4 pro , 6 pro a 9 pro).

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. Nahraďte `<TechnicalProfile>` celý `<RelyingParty>` prvek v elementu následujícím technickým profilem XML.

    ```XML
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
    ```

1. Aktualizujte `tenant-name` s názvem vašeho klienta Azure AD B2C.

Konečný soubor zásad předávající strany by měl vypadat takto:

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="contoso.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin_saml"
  PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_saml">

  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <UserJourneys>
    <UserJourney Id="SignUpOrSignIn">
      <OrchestrationSteps>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
      </OrchestrationSteps>
    </UserJourney>
  </UserJourneys>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

### <a name="32-upload-and-test-your-policy-metadata"></a>3.2 Nahrání a testování metadat zásad

Uložte změny a nahrajte nový soubor zásad. Po nahrání obou zásad (rozšíření a souborů předávající strany) otevřete webový prohlížeč a přejděte k metadatům zásad.

Metadata IDP zásad Azure AD B2C jsou informace používané v protokolu SAML k vystavení konfigurace zprostředkovatele identity SAML. Metadata definují umístění služeb, jako je například přihlášení a odhlášení, certifikáty, způsob přihlášení a další. Metadata zásad Azure AD B2C jsou k dispozici na následující adrese URL. Nahraďte `tenant-name` název vašeho klienta Azure AD `policy-name` B2C a názvem (ID) zásady:

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

Vaše vlastní zásady a tenant AD B2C Azure jsou teď připraveni. Dále vytvořte registraci aplikace v Azure AD B2C.

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. Instalační aplikace v adresáři Azure AD B2C

### <a name="41-register-your-application-in-azure-active-directory"></a>4.1 Registrace aplikace ve službě Azure Active Directory

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V horní nabídce vyberte filtr **Directory + subscription** a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. V levé nabídce vyberte **Azure AD B2C**. Nebo vyberte **Všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací (preview)** a pak vyberte **Nová registrace**.
1. Zadejte **název** aplikace. Například *SAMLApp1*.
1. V části **Podporované typy účtů**vyberte možnost Účty pouze v tomto **organizačním adresáři.**
1. V části **Přesměrovat identifikátor URI** `https://localhost`vyberte možnost **Web**a zadejte . Tuto hodnotu později upravíte v manifestu registrace aplikace.
1. Vyberte **Zaregistrovat**.

### <a name="42-update-the-app-manifest"></a>4.2 Aktualizace manifestu aplikace

Pro aplikace SAML existuje několik vlastností, které je třeba nakonfigurovat v manifestu registrace aplikace.

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na registraci aplikace, kterou jste vytvořili v předchozí části.
1. V části **Manage**vyberte **Manifest,** chcete-li otevřít editor manifestu. V následujících částech můžete upravit několik vlastností.

#### <a name="identifieruris"></a>identifikátorUris

Jedná `identifierUris` se o kolekci řetězců obsahující uživatelem definované identifikátory URI, které jednoznačně identifikují webovou aplikaci v rámci svého klienta Azure AD B2C. Váš poskytovatel služeb musí tuto `Issuer` hodnotu nastavit v prvku požadavku SAML.

#### <a name="samlmetadataurl"></a>samlMetadataUrl

Tato vlastnost představuje adresu URL veřejně dostupných metadat poskytovatele služeb. Adresa URL metadat může překážet na soubor metadat nahraný do libovolného anonymně přístupného koncového bodu, například úložiště objektů blob.

Metadata jsou informace používané v protokolu SAML k vystavení konfigurace strany SAML, například poskytovatele služeb. Metadata definují umístění služeb, jako je přihlášení a odhlášení, certifikáty, způsob přihlášení a další. Azure AD B2C přečte metadata poskytovatele služeb a podle toho funguje. Metadata nejsou vyžadována. Můžete také zadat některé atributy, jako je identifikátor URI odpovědi a identifikátor URI pro odhlášení přímo v manifestu aplikace.

Pokud jsou vlastnosti zadané *v* adrese URL metadat SAML a v manifestu registrace aplikace, jsou **sloučeny**. Vlastnosti zadané v adrese URL metadat jsou zpracovány jako první a mají přednost.

Pro účely tohoto kurzu, který používá testovací aplikaci `samlMetadataUrl`SAML, použijte následující hodnotu pro :

```JSON
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>replyUrlsWithType (volitelné)

Pokud nezadáte identifikátor URI metadat, můžete explicitně zadat adresu URL odpovědi. Tato volitelná `AssertionConsumerServiceUrl` vlastnost`SingleSignOnService` představuje ( URL v metadatech poskytovatele služeb) a `BindingType` předpokládá se, že je `HTTP POST`.

Pokud se rozhodnete nakonfigurovat adresu URL odpovědi a adresu URL odhlášení v manifestu aplikace bez použití metadat poskytovatele služeb, Azure AD B2C nebude ověřovat podpis požadavku SAML ani šifrovat odpověď SAML.

Pro účely tohoto kurzu, ve kterém používáte `url` testovací `replyUrlsWithType` aplikaci SAML, nastavte vlastnost na hodnotu uvedenou v následujícím fragmentu JSON.

```JSON
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>logoutUrl (volitelné)

Tato volitelná `Logout` vlastnost`SingleLogoutService` představuje adresu URL ( adresu URL `BindingType` v metadatech `Http-Redirect`předávající strany) a předpokládá se, že je pro .

Pro účely tohoto kurzu, který používá `logoutUrl` testovací `https://samltestapp2.azurewebsites.net/logout`aplikaci SAML, ponechte nastavenou na :

```JSON
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. Aktualizujte kód aplikace

Posledním krokem je povolit Azure AD B2C jako SAML IdP ve vaší aplikaci předávající strany SAML. Každá aplikace je jiná a postup se liší. Podrobnosti nahlédněte do dokumentace k aplikaci.

Obvykle jsou vyžadovány některé nebo všechny následující:

* **Metadata**:`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **Vystavitel**: Použití entityID v souboru metadat
* **Přihlašovací url/SAML koncový bod/ADRESA URL SAML**: Kontrola hodnoty v souboru metadat
* **Certifikát**: Jedná *se o B2C_1A_SamlIdpCert*, ale bez soukromého klíče. Jak získat veřejný klíč certifikátu:

    1. Přejděte na adresu URL metadat zadanou výše.
    1. Zkopírujte hodnotu `<X509Certificate>` v prvku.
    1. Vložte jej do textového souboru.
    1. Uložte textový soubor jako soubor *CER.*

### <a name="51-test-with-the-saml-test-app-optional"></a>5.1 Test s testovací aplikací SAML (volitelně)

Chcete-li dokončit tento kurz pomocí naší [testovací aplikace SAML][samltest]:

* Aktualizace názvu klienta
* Aktualizovat název zásady, například *B2C_1A_signup_signin_saml*
* Zadejte tento identifikátor URI vystavithona:`https://contoso.onmicrosoft.com/app-name`

Vyberte **Přihlásit** a měli byste se naskytnout s přihlašovací obrazovkou uživatele. Po přihlášení je kontrolní výraz SAML vydán zpět do ukázkové aplikace.

## <a name="sample-policy"></a>Ukázková zásada

Poskytujeme kompletní ukázkové zásady, které můžete použít pro testování pomocí testovací aplikace SAML.

1. Stáhněte si [zásady ukázkové ukázky přihlášení iniciované pomocí aplikace SAML-SP](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)
1. Aktualizace `TenantId` tak, aby odpovídala názvu klienta, například *contoso.b2clogin.com*
1. Zachovat název zásady *B2C_1A_SAML2_signup_signin*

## <a name="supported-and-unsupported-saml-modalities"></a>Podporované a nepodporované způsoby SAML

Následující scénáře předávající strany SAML (RP) jsou podporovány prostřednictvím vlastního koncového bodu metadat:

* Více adres URL odhlášení nebo vazby POST pro adresu URL odhlášení v objektu instančního objektu aplikace nebo služby.
* Zadejte podpisový klíč pro ověření požadavků RP v objektu instančního objektu aplikace nebo služby.
* Zadejte šifrovací klíč tokenu v objektu instančního objektu aplikace nebo služby.
* Přihlášení iniciovaná poskytovatelem identity nejsou aktuálně podporována ve verzi preview.

## <a name="next-steps"></a>Další kroky

- Více informací o protokolu SAML naleznete [na webových stránkách OASIS](https://www.oasis-open.org/).
- Získejte testovací webovou aplikaci SAML z [úložiště komunity Azure AD B2C GitHub](https://github.com/azure-ad-b2c/saml-sp-tester).

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

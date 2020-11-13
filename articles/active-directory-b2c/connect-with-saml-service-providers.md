---
title: Konfigurace Azure AD B2C IdP jako SAML pro vaše aplikace
title-suffix: Azure AD B2C
description: Jak nakonfigurovat Azure AD B2C pro poskytování kontrolních výrazů protokolu SAML vašim aplikacím (poskytovatelům služeb). Azure AD B2C bude fungovat jako IdP (Single identity Provider) do vaší aplikace SAML.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/26/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 6f7888e978fd4eb19232c156ce65b6e4967d9c5a
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2020
ms.locfileid: "94575964"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Registrace aplikace SAML v Azure AD B2C

V tomto článku se dozvíte, jak nakonfigurovat Azure Active Directory B2C (Azure AD B2C), aby fungoval Security Assertion Markup Language jako zprostředkovatel identity SAML (IdP) pro vaše aplikace.

## <a name="scenario-overview"></a>Přehled scénáře

Organizace, které používají Azure AD B2C jako řešení pro správu identit a přístupu zákazníků, můžou vyžadovat interakci s poskytovateli identity nebo aplikacemi, které jsou nakonfigurované k ověřování pomocí protokolu SAML.

Azure AD B2C dosahuje interoperability SAML jedním ze dvou způsobů:

* Funguje jako *zprostředkovatel identity* (IDP) a vydává se jednotné přihlašování (SSO) s poskytovateli služeb založenými na SAML (vaše aplikace)
* Funguje jako *poskytovatel služeb* (SP) a spolupracuje s poskytovateli identity založenými na SAML, jako je SALESFORCE a ADFS

![Diagram s B2C jako poskytovatel identity vlevo a B2C jako poskytovatel služeb hned](media/saml-identity-provider/saml-idp-diagram-01.jpg)

Shrnutí dvou scénářů, které nejsou exkluzivní, pomocí SAML:

| Scénář | Role Azure AD B2C | Postupy |
| -------- | ----------------- | ------- |
| Moje aplikace očekává pro dokončení ověřování kontrolní výraz SAML. | **Azure AD B2C funguje jako zprostředkovatel identity (IdP).**<br />Azure AD B2C funguje jako IdP SAML pro aplikace. | Tento článek. |
| Moji uživatelé potřebují jednotné přihlašování pomocí zprostředkovatele identity kompatibilního s SAML, jako je ADFS, Salesforce nebo Shibboleth.  | **Azure AD B2C funguje jako poskytovatel služeb (SP).**<br />Azure AD B2C slouží jako poskytovatel služeb při připojování k poskytovateli identity SAML. Jedná se o federační proxy mezi vaší aplikací a poskytovatelem identity SAML.  | <ul><li>[Nastavení přihlášení pomocí služby ADFS jako IdP SAML pomocí vlastních zásad](identity-provider-adfs2016-custom.md)</li><li>[Nastavení přihlašování pomocí poskytovatele služby Salesforce SAML pomocí vlastních zásad](identity-provider-salesforce-custom.md)</li></ul> |

## <a name="prerequisites"></a>Požadavky

* Proveďte kroky v části Začínáme [s vlastními zásadami v Azure AD B2C](custom-policy-get-started.md). Vlastní zásady *SocialAndLocalAccounts* potřebujete od úvodní sady Custom Policy Pack popsané v článku.
* Základní porozumění protokolu Security Assertion Markup Language (SAML).
* Webová aplikace konfigurovaná jako poskytovatel služeb SAML (SP). Pro tento kurz můžete použít [testovací aplikaci SAML][samltest] , kterou poskytujeme.

## <a name="components-of-the-solution"></a>Součásti řešení

V tomto scénáři jsou vyžadovány tři hlavní komponenty:

* **Poskytovatel služeb** SAML s možností ODESÍLAT požadavky SAML a přijímat, dekódovat a reagovat na kontrolní výrazy saml z Azure AD B2C. Poskytovatel služeb se označuje také jako aplikace předávající strany.
* Veřejně dostupný **koncový bod METADAT** SAML pro vašeho poskytovatele služeb.
* [Tenant Azure AD B2C](tutorial-create-tenant.md)

Pokud ještě nemáte poskytovatele služby SAML a přidruženého koncového bodu metadat, můžete použít tuto ukázkovou aplikaci SAML, kterou jsme zpřístupnili pro testování:

[Testovací aplikace SAML][samltest]

## <a name="1-set-up-certificates"></a>1. nastavení certifikátů

Chcete-li vytvořit vztah důvěryhodnosti mezi vaším poskytovatelem služeb a Azure AD B2C, je nutné zadat certifikáty webové aplikace x509.

* **Certifikáty poskytovatele služeb**
  * Certifikát s privátním klíčem uloženým ve vaší webové aplikaci. Tento certifikát používá poskytovatel služeb k podepsání žádosti SAML odeslané na Azure AD B2C. Azure AD B2C přečte veřejný klíč z metadat poskytovatele služby a ověří podpis.
  * Volitelné Certifikát s privátním klíčem uloženým ve vaší webové aplikaci. Azure AD B2C přečte veřejný klíč z metadat poskytovatele služby k zašifrování kontrolního výrazu SAML. Poskytovatel služeb potom pomocí privátního klíče dešifruje kontrolní výraz.
* **Azure AD B2C certifikátů**
  * Certifikát s privátním klíčem v Azure AD B2C. Tento certifikát používá Azure AD B2C k podepsání odpovědi SAML odeslané poskytovateli služeb. Poskytovatel služeb přečte veřejný klíč Azure AD B2C metadata a ověří podpis odpovědi SAML.

Můžete použít certifikát vydaný veřejnou certifikační autoritou nebo pro tento kurz certifikát podepsaný svým držitelem.

### <a name="11-prepare-a-self-signed-certificate"></a>1,1 Příprava certifikátu podepsaného svým držitelem

Pokud ještě certifikát nemáte, můžete pro tento kurz použít certifikát podepsaný svým držitelem. Ve Windows můžete k vygenerování certifikátu použít rutinu [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) prostředí PowerShell.

1. Spuštěním tohoto příkazu PowerShellu Vygenerujte certifikát podepsaný svým držitelem. Upravte `-Subject` argument podle potřeby pro vaši aplikaci a Azure AD B2C název tenanta. Můžete také upravit `-NotAfter` Datum a zadat jiné vypršení platnosti certifikátu.

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

1. Otevřete **Spravovat uživatelské certifikáty**  >  **aktuální uživatel**  >  **osobní**  >  **certifikáty**  >  *YourAppName.yourtenant.onmicrosoft.com* .
1. Vyberte certifikát > **akci**  >  **všechny úkoly**  >  **exportovat** .
1. Vyberte **Ano**  >  **Next**  >  **, další Ano, exportovat privátní klíč**  >  **Další** .
1. Přijměte výchozí hodnoty pro **Formát souboru pro export** .
1. Zadejte heslo pro certifikát.

### <a name="12-upload-the-certificate"></a>1,2 Nahrajte certifikát.

Potom do Azure AD B2C nahrajte kontrolní výraz SAML a podpisový certifikát odpovědi.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a vyhledejte Azure AD B2C tenanta.
1. V části **zásady** vyberte možnost **Architektura prostředí identity** a pak **klíče zásad**.
1. Vyberte **Přidat** a pak vybrat **Možnosti**  >  **nahrát**.
1. Zadejte **název** , například *SamlIdpCert*. *B2C_1A_* předpony se automaticky přidají do názvu vašeho klíče.
1. Nahrajte certifikát pomocí ovládacího prvku nahrát soubor.
1. Zadejte heslo certifikátu.
1. Vyberte **Vytvořit**.
1. Ověřte, že se klíč zobrazuje podle očekávání. Například *B2C_1A_SamlIdpCert*.

## <a name="2-prepare-your-policy"></a>2. Příprava zásad

### <a name="21-create-the-saml-token-issuer"></a>2,1 Vytvoření vystavitele tokenu SAML

Teď přidejte schopnost vašeho tenanta, aby vydával tokeny SAML, pomocí [vystavitele tokenu SAML](saml-issuer-technical-profile.md) a technických profilů [zprostředkovatele relace SAML](custom-policy-reference-sso.md#samlssosessionprovider) .

Otevřete `SocialAndLocalAccounts\` **`TrustFrameworkExtensions.xml`** v úvodní sadě vlastních zásad.

Vyhledejte `<ClaimsProviders>` část a přidejte následující fragment kódu XML.

Můžete změnit hodnotu `IssuerUri` metadat. Toto je identifikátor URI vystavitele vrácený v odpovědi SAML z Azure AD B2C. Aplikace předávající strany by měla být nakonfigurovaná tak, aby přijímala identifikátor URI vystavitele během ověřování kontrolního výrazu SAML.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
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
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="3-add-the-saml-relying-party-policy"></a>3. přidejte zásadu předávající strany SAML.

Teď, když váš tenant může vystavovat kontrolní výrazy SAML, je nutné vytvořit zásadu předávající strany SAML a upravit cestu uživatele tak, aby vydávala kontrolní výraz SAML namísto tokenu JWT.

### <a name="31-create-sign-up-or-sign-in-policy"></a>3,1 Vytvoření zásady registrace nebo přihlašování

1. Vytvořte kopii souboru *SignUpOrSignin.xml* v pracovním adresáři úvodní Pack a uložte ho s novým názvem. Například *SignUpOrSigninSAML.xml*. Toto je soubor zásad předávající strany.

1. Otevřete soubor *SignUpOrSigninSAML.xml* v upřednostňovaném editoru.

1. Změňte `PolicyId` zásadu a na `PublicPolicyUri` _B2C_1A_signup_signin_saml_ , `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml` Jak vidíte níže.

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. Přidejte následující fragment kódu XML těsně před `<RelyingParty>` element. Tento kód XML přepíše krok orchestrace číslo 7 cesty uživatele _SignUpOrSignIn_ . Pokud jste spustili z jiné složky v počátečním balíčku nebo jste přizpůsobili cestu uživatele přidáním nebo odebráním kroků orchestrace, ujistěte se, že je číslo (v `order` elementu) zarovnáno s parametrem zadaným v cestě uživatele pro krok vystavitele tokenu (například ve složkách dalších počátečních sad je číslo kroku 4 pro `LocalAccounts` , 6 pro `SocialAccounts` a 9 pro `SocialAndLocalAccountsWithMfa` ).

    ```xml
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. Nahraďte celý `<TechnicalProfile>` element v `<RelyingParty>` elementu následujícím technickým profilem XML.

    ```xml
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

1. Aktualizujte `tenant-name` název vašeho tenanta Azure AD B2C.

Konečný soubor zásad předávající strany by měl vypadat jako následující kód XML:

```xml
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

> [!NOTE]
> Při implementaci jiných typů uživatelských toků (například přihlášení, resetování hesla nebo úpravách profilu) je proces v podstatě stejný, jak je popsáno v této části. V kroku 4 výše změníte poslední krok cesty uživatele z `JWTIssuer` na `Saml2AssertionIssuer` . A v kroku 6 výše v části předávající strana změníte **protokol** z `OpenIdConnect` na `SAML2` .

### <a name="32-upload-and-test-your-policy-metadata"></a>3,2 nahrávání a testování metadat zásad

Uložte změny a nahrajte nový soubor zásad. Po nahrání obou zásad (rozšíření a souborů předávající strany) otevřete webový prohlížeč a přejděte na metadata zásad.

Metadata IDP zásad Azure AD B2C jsou informace, které se používají v protokolu SAML k vystavení konfigurace zprostředkovatele identity SAML. Metadata definují umístění služeb, jako je například přihlášení a odhlášení, certifikáty, metoda přihlašování a další. Metadata zásad Azure AD B2C jsou k dispozici na následující adrese URL. Nahraďte `tenant-name` názvem vašeho tenanta Azure AD B2C a `policy-name` názvem (ID) zásady, například.../B2C_1A_SAML2_signup_signin/SAMLP/metadata:

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

Vaše vlastní zásady a Azure AD B2C tenant jsou teď připravené. V dalším kroku vytvořte registraci aplikace v Azure AD B2C.

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. instalace aplikace v adresáři Azure AD B2C

### <a name="41-register-your-application-in-azure-ad-b2c"></a>4,1 zaregistrovat aplikaci v Azure AD B2C

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací** a pak vyberte **Nová registrace**.
1. Zadejte **název** aplikace. Například *SAMLApp1*.
1. V části **podporované typy účtů** vyberte **účty jenom v tomto organizačním adresáři** .
1. V části **identifikátor URI pro přesměrování** vyberte **Web** a potom zadejte `https://localhost` . Tuto hodnotu upravíte později v manifestu registrace aplikace.
1. Vyberte **Zaregistrovat**.

### <a name="42-update-the-app-manifest"></a>4,2 aktualizace manifestu aplikace

Pro aplikace SAML existuje několik vlastností, které je třeba nakonfigurovat v manifestu registrace aplikace.

1. V [Azure Portal](https://portal.azure.com)přejděte k registraci aplikace, kterou jste vytvořili v předchozí části.
1. V části **Spravovat** vyberte **manifest** a otevřete tak editor manifestu. V následujících oddílech upravíte několik vlastností.

#### <a name="identifieruris"></a>identifierUris

`identifierUris`Je kolekce řetězců obsahující uživatelsky definované identifikátory URI, které jedinečně identifikují webovou aplikaci v rámci jejího Azure AD B2C tenanta. Identifikátor URI se musí shodovat s názvem požadavku SAML `Issuer` . Uživatelem definovaná hodnota identifikátoru URI je obvykle stejná jako metadata poskytovatele služby `entityID` .

#### <a name="samlmetadataurl"></a>samlMetadataUrl

Tato vlastnost představuje adresu URL veřejně dostupných metadat poskytovatele služeb. Adresa URL metadat může ukazovat na soubor metadat odeslaný do libovolného anonymního dostupného koncového bodu, například jako úložiště objektů BLOB.

Metadata jsou informace, které se používají v protokolu SAML k vystavení konfigurace večírku SAML, jako je například poskytovatel služeb. Metadata definují umístění služeb, jako je přihlášení a odhlášení, certifikáty, metoda přihlašování a další. Azure AD B2C čte metadata poskytovatele služby a funguje odpovídajícím způsobem. Metadata se nevyžadují. Můžete také zadat některé atributy, jako je identifikátor URI odpovědi, a identifikátor URI odhlášení přímo v manifestu aplikace.

Pokud jsou v adrese URL metadat *SAML i v* manifestu registrace aplikace zadány vlastnosti, budou **sloučeny**. Vlastnosti zadané v adrese URL metadat jsou zpracovávány jako první a mají přednost.

Pro účely tohoto kurzu, který používá testovací aplikaci SAML, použijte následující hodnotu pro `samlMetadataUrl` :

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>replyUrlsWithType (volitelné)

Pokud neposkytnete identifikátor URI metadat, můžete explicitně zadat adresu URL odpovědi. Tato volitelná vlastnost představuje `AssertionConsumerServiceUrl` ( `SingleSignOnService` Adresa URL v metadatech poskytovatele služeb) a předpokládá se, že `BindingType` je `HTTP POST` .

Pokud se rozhodnete nakonfigurovat adresu URL odpovědi a adresu URL pro odhlášení v manifestu aplikace bez použití metadat poskytovatele služby, Azure AD B2C neověří podpis žádosti SAML ani nezašifruje odpověď SAML.

Pro účely tohoto kurzu, ve kterém používáte aplikaci testu SAML, nastavte `url` vlastnost `replyUrlsWithType` na hodnotu zobrazenou v následujícím fragmentu kódu JSON.

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>logoutUrl (volitelné)

Tato volitelná vlastnost představuje `Logout` adresu URL ( `SingleLogoutService` Adresa URL v metadatech předávající strany) a předpokládá se, že je `BindingType` `Http-Redirect` .

Pro účely tohoto kurzu, který používá testovací aplikaci SAML, nechejte `logoutUrl` nastaveno na `https://samltestapp2.azurewebsites.net/logout` :

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. aktualizujte kód aplikace

Posledním krokem je povolení Azure AD B2C IdP jako SAML v aplikaci předávající strany SAML. Každá aplikace je odlišná a postup se liší. Podrobnosti najdete v dokumentaci k vaší aplikaci.

Metadata můžete nakonfigurovat ve vašem poskytovateli služeb jako "statická metadata" nebo "dynamická metadata". Ve statickém režimu zkopírujte všechna nebo část metadat z metadat zásad Azure AD B2C. V dynamickém režimu můžete nastavit adresu URL na metadata a nechat si aplikaci dynamicky číst metadata.

Obvykle jsou potřeba některé z těchto možností:

* **Metadata** : `https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **Vystavitel** : hodnota požadavku SAML se `issuer` musí shodovat s jedním z identifikátorů URI nakonfigurovaných v `identifierUris` elementu manifestu registrace aplikace. Pokud název požadavku SAML `issuer` v `identifierUris` elementu neexistuje, [přidejte ho do manifestu registrace aplikace](#identifieruris). Například, `https://contoso.onmicrosoft.com/app-name`. 
* **Přihlašovací adresa URL/koncový bod SAML/adresa URL SAML** : ověřte hodnotu v souboru metadat zásad Azure AD B2C SAML pro `<SingleSignOnService>` element XML.
* **Certifikát** : toto je *B2C_1A_SamlIdpCert* , ale bez privátního klíče. Získání veřejného klíče certifikátu:

    1. Přejít na adresu URL metadat uvedenou výše.
    1. Zkopírujte hodnotu v `<X509Certificate>` elementu.
    1. Vložte ho do textového souboru.
    1. Uložte textový soubor jako soubor *. cer* .

### <a name="51-test-with-the-saml-test-app-optional"></a>5,1 test pomocí testovací aplikace SAML (volitelné)

K dokončení tohoto kurzu použijte naši [aplikaci testu SAML][samltest]:

* Aktualizovat název tenanta
* Název zásad aktualizace, například *B2C_1A_signup_signin_saml*
* Zadejte tento identifikátor URI vystavitele. Použijte jeden z identifikátorů URI nalezených v `identifierUris` elementu v manifestu registrace aplikace, například `https://contoso.onmicrosoft.com/app-name` .

Vyberte **přihlašovací údaje** , které byste měli mít k dispozici na přihlašovací obrazovce uživatele. Po přihlášení je kontrolní výraz SAML vydán zpět do ukázkové aplikace.

## <a name="enable-encrypted-assertions-optional"></a>Povolit šifrované kontrolní výrazy (volitelné)

Chcete-li zašifrovat kontrolní výrazy SAML odeslané zpět poskytovateli služeb, Azure AD B2C použijí certifikát veřejného klíče poskytovatele služby. Veřejný klíč musí existovat v metadatech SAML popsaných výše v [části "samlMetadataUrl"](#samlmetadataurl) jako popisovač klíče s použitím šifrování.

Následující kód XML je příkladem popisovače šifr metadat SAML s použitím nastaveného na šifrování:

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

Pokud chcete povolit Azure AD B2C odesílat šifrované kontrolní výrazy, nastavte položku metadat **WantsEncryptedAssertion** na `true` [technický profil předávající strany](relyingparty.md#technicalprofile). Můžete také nakonfigurovat algoritmus použitý k zašifrování kontrolního výrazu SAML. Další informace najdete v tématu [metadata technického profilu předávající strany](relyingparty.md#metadata). 

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="enable-identity-provider-initiated-flow-optional"></a>Povolit tok iniciované zprostředkovatelem identity (volitelné)

V toku iniciované zprostředkovatelem identity se proces přihlášení iniciuje zprostředkovatelem identity (Azure AD B2C), který pošle nevyžádaný odpověď SAML poskytovateli služby (vaší aplikace předávající strany). V současné době nepodporujeme scénáře, ve kterých je poskytovatel iniciující identity externím poskytovatelem identity, například [AD-FS](identity-provider-adfs2016-custom.md)nebo [Salesforce](identity-provider-salesforce-custom.md).

Pokud chcete povolit tok spuštěného zprostředkovatele identity (Azure AD B2C), **IdpInitiatedProfileEnabled** nastavte `true` v [technickém profilu předávající strany](relyingparty.md#technicalprofile)položku metadat IdpInitiatedProfileEnabled.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="IdpInitiatedProfileEnabled">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

Pokud se chcete přihlásit nebo zaregistrovat uživatele prostřednictvím toku iniciované zprostředkovatelem identity, použijte tuto adresu URL:

```
https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/generic/login?EntityId=app-identifier-uri 
```

Nahraďte následující hodnoty:

* **název tenanta** s názvem tenanta
* **název zásady** s názvem zásady předávající strany SAML
* identifikátor **App-Identifier-URI** s `identifierUris` v souboru metadat, jako je například`https://contoso.onmicrosoft.com/app-name`
## <a name="sample-policy"></a>Ukázková zásada

Poskytujeme kompletní ukázkovou zásadu, kterou můžete použít pro testování pomocí testovací aplikace SAML.

1. Stáhnout [ukázkovou zásadu přihlášení iniciované SAML-SP](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated)
1. Aktualizujte `TenantId` tak, aby odpovídaly názvu tenanta, například *contoso.b2clogin.com*
1. Ponechte název zásady *B2C_1A_SAML2_signup_signin*

## <a name="supported-and-unsupported-saml-modalities"></a>Podporované a nepodporované postupy SAML

Následující scénáře předávající strany SAML (RP) jsou podporovány prostřednictvím vlastního koncového bodu metadat:

* Několik adres URL pro odhlášení nebo příspěvek pro odhlašovací adresu URL v objektu Application nebo instančního objektu.
* Zadejte podpisový klíč pro ověření požadavků RP v objektu Application nebo instančního objektu.
* Zadejte šifrovací klíč tokenu v objektu aplikace nebo instančního objektu.
* Zprostředkovatel identity inicioval přihlášení, kde je poskytovatel identity Azure AD B2C.

## <a name="saml-token"></a>Token SAML

Token SAML je token zabezpečení, který je vydaný Azure AD B2C po úspěšném přihlášení. Obsahuje informace o uživateli, poskytovateli služeb, pro který je token určen, signatura a doba platnosti. Následující tabulka obsahuje seznam deklarací a vlastností, které můžete očekávat v tokenu SAML vydaném Azure AD B2C.

|Prvek  |Vlastnost  |Poznámky  |
|---------|---------|---------|
|`<Response>`| `ID` | Automaticky generovaný jedinečný identifikátor odpovědi. | 
|`<Response>`| `InResponseTo` | ID požadavku SAML, na který je tato zpráva odezva. | 
|`<Response>` | `IssueInstant` | Doba, po kterou se má odpověď vydávat. Hodnota Time je kódována v UTC.  Pokud chcete změnit nastavení pro životnost tokenu, nastavte `TokenNotBeforeSkewInSeconds` [metadata](saml-issuer-technical-profile.md#metadata) technického profilu vystavitele tokenu SAML. | 
|`<Response>` | `Destination`| Odkaz na identifikátor URI označující adresu, na kterou byla tato odpověď odeslána. Hodnota je shodná s požadavkem SAML `AssertionConsumerServiceURL` . | 
|`<Response>` `<Issuer>` | |Identifikuje vystavitele tokenu. Toto je libovolný identifikátor URI definovaný `IssuerUri` [metadaty](saml-issuer-technical-profile.md#metadata) problému tokenu SAML.     |
|`<Response>` `<Assertion>` `<Subject>` `<NameID>`     |         |Objekt zabezpečení, o kterém token vyhodnotí informace, například ID objektu uživatele. Tato hodnota je neměnná a nelze ji znovu přiřadit ani použít znovu. Dá se použít k bezpečnému provádění kontrol autorizace, například když se token používá pro přístup k prostředku. Ve výchozím nastavení se deklarace identity subjektu naplní s ID objektu uživatele v adresáři.|
|`<Response>` `<Assertion>` `<Subject>` `<NameID>`     | `Format` | Odkaz na identifikátor URI představující klasifikaci informací o identifikátoru založeném na řetězci. Ve výchozím nastavení je tato vlastnost vynechána. Nastavením [SubjectNamingInfo](relyingparty.md#subjectnaminginfo) předávající strany můžete určit `NameID` formát, například `urn:oasis:names:tc:SAML:2.0:nameid-format:transient` . |
|`<Response>` `<Assertion>` `<Subject>` `<Conditions>` |`NotBefore` |Čas, kdy bude token platný. Hodnota Time je kódována v UTC. Vaše aplikace by měla tuto deklaraci identity použít k ověření platnosti životnosti tokenu. Pokud chcete změnit nastavení pro životnosti tokenů, nastavte `TokenNotBeforeSkewInSeconds` [metadata](saml-issuer-technical-profile.md#metadata) technického profilu problému s tokenem SAML. |
|`<Response>` `<Assertion>` `<Subject>` `<Conditions>` | `NotOnOrAfter` | Čas, kdy se token stal neplatným Vaše aplikace by měla tuto deklaraci identity použít k ověření platnosti životnosti tokenu. Hodnota je 15 minut po `NotBefore` a nelze ji změnit.|
|`<Response>` `<Assertion>` `<Conditions>` `<AudienceRestriction>` `<Audience>` | |Odkaz na identifikátor URI, který identifikuje zamýšlenou cílovou skupinu. Identifikuje zamýšleného příjemce tokenu. Hodnota je shodná s požadavkem SAML `AssertionConsumerServiceURL` .|
|`<Response>``<Assertion>` `<AttributeStatement>` kolekce`<Attribute>` | | Kolekce kontrolních výrazů (deklarace identity), jak je nakonfigurováno ve výstupních deklaracích [technického profilu předávající strany](relyingparty.md#technicalprofile) . Můžete nakonfigurovat název kontrolního výrazu nastavením `PartnerClaimType` výstupní deklarace identity. |

## <a name="next-steps"></a>Další kroky

- Další informace o [protokolu SAML najdete na webu Oasis](https://www.oasis-open.org/).
- Získejte testovací webovou aplikaci SAML z [Azure AD B2C úložiště GitHub Community](https://github.com/azure-ad-b2c/saml-sp-tester).

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

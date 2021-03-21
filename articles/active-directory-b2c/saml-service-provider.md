---
title: Konfigurace Azure Active Directory B2C IdP jako SAML pro vaše aplikace
title-suffix: Azure Active Directory B2C
description: Jak nakonfigurovat Azure Active Directory B2C pro poskytování kontrolních výrazů protokolu SAML vašim aplikacím (poskytovatelům služeb). Azure AD B2C bude fungovat jako IdP (Single identity Provider) do vaší aplikace SAML.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 1035f43642f3884e7cc0f6ab47e9c9afd1f29170
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102107385"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Registrace aplikace SAML v Azure AD B2C

V tomto článku se dozvíte, jak propojit aplikace Security Assertion Markup Language (poskytovatelé služeb) a Azure Active Directory B2C (Azure AD B2C) pro ověřování.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="overview"></a>Přehled

Organizace, které používají Azure AD B2C jako řešení pro správu identit a přístupu zákazníků, můžou vyžadovat integraci s aplikacemi, které se ověřují pomocí protokolu SAML. Následující diagram ukazuje, jak Azure AD B2C slouží jako *zprostředkovatel identity* (IDP) k zajištění jednotného přihlašování (SSO) s aplikacemi založenými na SAML.

![Diagram s B2C jako poskytovatel identity vlevo a B2C jako poskytovatel služeb napravo.](media/saml-service-provider/saml-service-provider-integration.png)

1. Aplikace vytvoří požadavek SAML AuthN, který se odešle do koncového bodu přihlášení služby Azure AD B2C's SAML.
2. Uživatel může k ověřování použít místní účet Azure AD B2C nebo jiného zprostředkovatele federovaného identity (Pokud je nakonfigurované).
3. Pokud se uživatel přihlásí pomocí federovaného poskytovatele identity, pošle se Azure AD B2C odpověď tokenu.
4. Azure AD B2C generuje kontrolní výraz SAML a odešle ho do aplikace.

## <a name="prerequisites"></a>Předpoklady

* Proveďte kroky v části Začínáme [s vlastními zásadami v Azure AD B2C](custom-policy-get-started.md). Vlastní zásady *SocialAndLocalAccounts* potřebujete od úvodní sady Custom Policy Pack popsané v článku.
* Základní porozumění protokolu SAML a znalost implementace SAML aplikace
* Webová aplikace konfigurovaná jako aplikace SAML. Pro tento kurz můžete použít [testovací aplikaci SAML][samltest] , kterou poskytujeme.

## <a name="components"></a>Komponenty

V tomto scénáři jsou vyžadovány tři hlavní komponenty:

* **Aplikace** SAML s možností ODESÍLAT požadavky SAML Authn a přijímat, dekódovat a ověřovat odpovědi saml z Azure AD B2C. Aplikace SAML je také známá jako aplikace nebo poskytovatel služeb předávající strany.
* Veřejně dostupný **koncový bod METADAT** SAML nebo dokument XML aplikace SAML.
* [Tenant Azure AD B2C](tutorial-create-tenant.md)

Pokud ještě nemáte aplikaci SAML a přidružený koncový bod metadat, můžete použít tuto ukázkovou aplikaci SAML, kterou jsme zpřístupnili pro testování:

[Testovací aplikace SAML][samltest]

## <a name="set-up-certificates"></a>Nastavení certifikátů

Aby bylo možné vytvořit vztah důvěryhodnosti mezi vaší aplikací a Azure AD B2C, musí být obě služby schopné vytvořit a ověřit podpisy druhé strany. Nakonfigurujte certifikáty x509 v Azure AD B2C a ve vaší aplikaci.

**Certifikáty aplikací**

| Využití | Povinné | Popis |
| --------- | -------- | ----------- |
| Podepisování požadavku SAML  | No | Certifikát s privátním klíčem uloženým ve vaší webové aplikaci, který aplikace používá k podepisování požadavků SAML odeslaných do Azure AD B2C. Webová aplikace musí zveřejnit veřejný klíč prostřednictvím koncového bodu metadat SAML. Azure AD B2C ověří podpis požadavku SAML pomocí veřejného klíče z metadat aplikace.|
| Šifrování kontrolního výrazu SAML  | No | Certifikát s privátním klíčem uloženým ve vaší webové aplikaci. Webová aplikace musí zveřejnit veřejný klíč prostřednictvím koncového bodu metadat SAML. Azure AD B2C může šifrovat kontrolní výrazy ve vaší aplikaci pomocí veřejného klíče. Aplikace používá privátní klíč k dešifrování kontrolního výrazu.|

**Azure AD B2C certifikátů**

| Využití | Povinné | Popis |
| --------- | -------- | ----------- |
| Podepisování odpovědí SAML | Yes | Certifikát s privátním klíčem uloženým v Azure AD B2C. Tento certifikát používá Azure AD B2C k podepsání odpovědi SAML odeslané do vaší aplikace. Vaše aplikace přečte veřejný klíč Azure AD B2C metadata a ověří podpis odpovědi SAML. |

V produkčním prostředí doporučujeme používat certifikáty vydané veřejnou certifikační autoritou. Tento postup však můžete provést i u certifikátů podepsaných svým držitelem.

### <a name="prepare-a-self-signed-certificate-for-saml-response-signing"></a>Příprava certifikátu podepsaného svým držitelem pro podepisování odpovědí SAML

Musíte vytvořit podpisový certifikát odpovědi SAML, aby vaše aplikace mohla důvěřovat kontrolnímu výrazu z Azure AD B2C.

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="enable-your-policy-to-connect-with-a-saml-application"></a>Povolení připojení k zásadě pomocí aplikace SAML

Aby bylo možné se připojit k aplikaci SAML, Azure AD B2C musí být schopni vytvořit odpovědi SAML.

Otevřete `SocialAndLocalAccounts\` **`TrustFrameworkExtensions.xml`** v úvodní sadě vlastních zásad.

Vyhledejte `<ClaimsProviders>` část a přidejte následující fragment kódu XML pro implementaci generátoru odpovědí SAML.

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
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
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

#### <a name="configure-the-issueruri-of-the-saml-response"></a>Konfigurace IssuerUri odpovědi SAML

`IssuerUri`V technickém profilu vystavitele tokenu SAML můžete změnit hodnotu položky metadat. Tato změna se projeví v `issuerUri` atributu vráceném v odpovědi SAML z Azure AD B2C. Vaše aplikace by měla být nakonfigurována tak, aby přijímala totéž `issuerUri` během ověřování odezvy SAML.

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
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

#### <a name="sign-the-azure-ad-b2c-idp-saml-metadata-optional"></a>Podepsat Azure AD B2C IdP metadata SAML (volitelné)

Můžete dát Azure AD B2C k podepsání jeho dokumentu metadat SAML IdP, pokud to vyžaduje aplikace. Provedete to tak, že vygenerujete a nahrajete klíč zásad podepisování metadat SAML IdP, jak je znázorněno v [přípravě certifikátu podepsaného svým držitelem pro podepisování odpovědí SAML](#prepare-a-self-signed-certificate-for-saml-response-signing). Pak nakonfigurujte `MetadataSigning` položku metadat v technickém profilu vystavitele tokenu SAML. `StorageReferenceId`Musí odkazovat na název klíče zásad.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlMetadataCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

#### <a name="sign-the-azure-ad-b2c-idp-saml-response-element-optional"></a>Podepsat element odpovědi SAML Azure AD B2C IdP (volitelné)

Můžete zadat certifikát, který se má použít k podepsání zpráv SAML. Zpráva je `<samlp:Response>` element v odpovědi SAML odeslané do aplikace.

Pokud chcete zadat certifikát, vygenerujte a nahrajte klíč zásad, jak je znázorněno v [přípravě certifikátu podepsaného svým držitelem pro podepisování odpovědí SAML](#prepare-a-self-signed-certificate-for-saml-response-signing). Pak nakonfigurujte `SamlMessageSigning` položku metadat v technickém profilu vystavitele tokenu SAML. `StorageReferenceId`Musí odkazovat na název klíče zásad.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlMessageCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```
## <a name="configure-your-policy-to-issue-a-saml-response"></a>Konfigurace zásady, aby vydávala odpověď SAML

Teď, když vaše zásada může vytvářet odpovědi SAML, je nutné nakonfigurovat zásadu, aby vydávala odpověď SAML namísto výchozí odezvy JWT pro vaši aplikaci.

### <a name="create-a-sign-up-or-sign-in-policy-configured-for-saml"></a>Vytvoření zásady registrace nebo přihlašování nakonfigurované pro SAML

1. Vytvořte kopii souboru *SignUpOrSignin.xml* v pracovním adresáři úvodní Pack a uložte ho s novým názvem. Například *SignUpOrSigninSAML.xml*. Tento soubor je vaším souborem zásad předávající strany a je nakonfigurován tak, aby vydával odpověď JWT ve výchozím nastavení.

1. Otevřete soubor *SignUpOrSigninSAML.xml* v upřednostňovaném editoru.

1. Změňte `PolicyId` zásadu a na `PublicPolicyUri` _B2C_1A_signup_signin_saml_ , `http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml` Jak vidíte níže.

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. Na konci cesty uživatele Azure AD B2C obsahuje `SendClaims` krok. Tento krok odkazuje na technický profil vystavitele tokenu. Pokud chcete vydat odpověď SAML místo výchozí odpovědi JWT, upravte `SendClaims` krok tak, aby odkazoval na nový technický profil vystavitele tokenu SAML `Saml2AssertionIssuer` .

Přidejte následující fragment kódu XML těsně před `<RelyingParty>` element. Tento kód XML přepíše krok Orchestration číslo 7 v cestě uživatele _SignUpOrSignIn_ . Pokud jste spustili z jiné složky v počátečním balíčku nebo jste přizpůsobili cestu uživatele přidáním nebo odebráním kroků orchestrace, ujistěte se, že číslo v `order` prvku odpovídá číslu zadanému v cestě uživatele pro krok vystavitele tokenu. Například ve složkách dalších startovních sad je odpovídající číslo kroku 4 pro `LocalAccounts` , 6 pro `SocialAccounts` a 9 pro `SocialAndLocalAccountsWithMfa` ).

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>
      <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys>
```

Element předávající strany určuje, který protokol vaše aplikace používá. Výchozí formát je `OpenId`. `Protocol`Element musí být změněn na `SAML` . Výstupní deklarace identity vytvoří mapování deklarací identity na kontrolní výraz SAML.

Nahraďte celý `<TechnicalProfile>` element v `<RelyingParty>` elementu následujícím technickým profilem XML. Aktualizujte `tenant-name` název vašeho tenanta Azure AD B2C.

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
> Stejný postup můžete použít k implementaci dalších typů uživatelských toků (například přihlašování, resetování hesla nebo toků úprav profilu).

### <a name="upload-your-policy"></a>Nahrajte zásady

Uložte změny a nahrajte do Azure Portal nové soubory zásad **TrustFrameworkExtensions.xml** a **SignUpOrSigninSAML.xml** .

### <a name="test-the-azure-ad-b2c-idp-saml-metadata"></a>Testování Azure AD B2C IdP metadat SAML

Po nahrání souborů zásad Azure AD B2C pomocí informací o konfiguraci vygeneruje dokument metadat SAML zprostředkovatele identity, který bude používat aplikace. Dokument metadat SAML obsahuje umístění služeb, například metody přihlašování a odhlášení, certifikáty atd.

Metadata zásad Azure AD B2C jsou k dispozici na následující adrese URL:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/samlp/metadata`

Nahraďte `<tenant-name>` názvem vašeho tenanta Azure AD B2C a `<policy-name>` názvem (ID) zásady, například:

`https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_signup_signin_saml/samlp/metadata`

## <a name="register-your-saml-application-in-azure-ad-b2c"></a>Registrace aplikace SAML v Azure AD B2C

Pro Azure AD B2C pro důvěřování vaší aplikaci vytvoříte registraci Azure AD B2C aplikace, která obsahuje informace o konfiguraci, jako je například koncový bod metadat aplikace.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací** a pak vyberte **Nová registrace**.
1. Zadejte **název** aplikace. Například *SAMLApp1*.
1. V části **podporované typy účtů** vyberte **účty jenom v tomto organizačním adresáři**.
1. V části **identifikátor URI pro přesměrování** vyberte **Web** a potom zadejte `https://localhost` . Tuto hodnotu upravíte později v manifestu registrace aplikace.
1. Vyberte **Zaregistrovat**.

### <a name="configure-your-application-in-azure-ad-b2c"></a>Konfigurace aplikace v Azure AD B2C

V případě aplikací SAML budete muset v manifestu registrace aplikace nakonfigurovat několik vlastností.

1. V [Azure Portal](https://portal.azure.com)přejděte k registraci aplikace, kterou jste vytvořili v předchozí části.
1. V části **Spravovat** vyberte **manifest** , aby se otevřel editor manifestu, a pak změňte vlastnosti popsané v následujících částech.

#### <a name="add-the-identifier"></a>Přidat identifikátor

Když aplikace SAML odešle požadavek na Azure AD B2C, požadavek SAML AuthN obsahuje `Issuer` atribut, což je obvykle stejná hodnota jako metadata aplikace `entityID` . Azure AD B2C používá tuto hodnotu k vyhledání registrace aplikace v adresáři a načtení konfigurace. Aby bylo toto vyhledávání úspěšné, `identifierUri` musí být v registraci aplikace naplněna hodnota, která odpovídá `Issuer` atributu.

V registračním manifestu vyhledejte `identifierURIs` parametr a přidejte odpovídající hodnotu. Tato hodnota bude mít stejnou hodnotu, která je nakonfigurovaná v požadavcích SAML AuthN pro EntityId v aplikaci, a na `entityID` hodnotě v metadatech aplikace.

Následující příklad ukazuje `entityID` v metadatech SAML:

```xml
<EntityDescriptor ID="id123456789" entityID="https://samltestapp2.azurewebsites.net" validUntil="2099-12-31T23:59:59Z" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
```

`identifierUris`Vlastnost bude přijímat jenom adresy URL v doméně `tenant-name.onmicrosoft.com` .

```json
"identifierUris":"https://samltestapp2.azurewebsites.net",
```

#### <a name="share-the-applications-metadata-with-azure-ad-b2c"></a>Sdílejte metadata aplikace pomocí Azure AD B2C

Po načtení registrace aplikace pomocí `identifierUri` Azure AD B2C pomocí metadat aplikace ověřte požadavek SAML Authn a určete, jak reagovat.

Doporučuje se, aby vaše aplikace vystavuje veřejně přístupný koncový bod metadat.

Pokud jsou vlastnosti zadané v adrese URL metadat SAML *i v manifestu* registrace aplikace, *sloučí* se. Vlastnosti zadané v adrese URL metadat jsou zpracovávány jako první a mají přednost.

Pomocí testovací aplikace SAML jako příklad použijte `samlMetadataUrl` v manifestu aplikace následující hodnotu:

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="override-or-set-the-assertion-consumer-url-optional"></a>Přepsat nebo nastavit adresu URL spotřebitele kontrolního výrazu (volitelné)

Můžete nakonfigurovat adresu URL odpovědi, na kterou Azure AD B2C odesílá odpovědi SAML. Adresy URL odpovědí lze konfigurovat v manifestu aplikace. Tato konfigurace je užitečná, když vaše aplikace nezveřejňuje veřejně přístupný koncový bod metadat.

Adresa URL odpovědi pro aplikaci SAML je koncový bod, na kterém aplikace očekává příjem odpovědí SAML. Aplikace obvykle poskytuje tuto adresu URL v dokumentu metadat v `AssertionConsumerServiceUrl` atributu, jak je znázorněno níže:

```xml
<SPSSODescriptor AuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    ...
    <AssertionConsumerService index="0" isDefault="true" Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://samltestapp2.azurewebsites.net/SP/AssertionConsumer" />        
</SPSSODescriptor>
```

Pokud chcete přepsat metadata poskytnutá v `AssertionConsumerServiceUrl` atributu nebo adresa URL není k dispozici v dokumentu metadat, můžete nakonfigurovat adresu URL v manifestu pod `replyUrlsWithType` vlastností. `BindingType`Nastaví se na `HTTP POST` .

Pomocí testovací aplikace SAML jako příklad můžete nastavit `url` vlastnost `replyUrlsWithType` na hodnotu zobrazenou v následujícím fragmentu kódu JSON.

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="override-or-set-the-logout-url-optional"></a>Přepsat nebo nastavit adresu URL pro odhlášení (volitelné)

Můžete nakonfigurovat adresu URL pro odhlášení, na kterou Azure AD B2C odešle uživatele po žádosti o odhlášení. Adresy URL odpovědí lze konfigurovat v manifestu aplikace.

Pokud chcete přepsat metadata poskytnutá v `SingleLogoutService` atributu nebo adresa URL není k dispozici v dokumentu metadat, můžete ji nakonfigurovat v manifestu pod `Logout` vlastností. `BindingType`Nastaví se na `Http-Redirect` .

Aplikace obvykle poskytuje tuto adresu URL v dokumentu metadat v `AssertionConsumerServiceUrl` atributu, jak je znázorněno níže:

```xml
<IDPSSODescriptor WantAuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://samltestapp2.azurewebsites.net/logout" ResponseLocation="https://samltestapp2.azurewebsites.net/logout" />

</IDPSSODescriptor>
```

Pomocí testovací aplikace SAML jako příklad byste měli opustit `logoutUrl` nastavení `https://samltestapp2.azurewebsites.net/logout` :

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

> [!NOTE]
> Pokud se rozhodnete nakonfigurovat adresu URL odpovědi a adresu URL pro odhlášení v manifestu aplikace, aniž byste museli zaplnit koncový bod metadat aplikace prostřednictvím `samlMetadataUrl` vlastnosti, Azure AD B2C neověří podpis požadavku SAML, ani nebude zašifrovat odpověď SAML.

## <a name="configure-azure-ad-b2c-as-a-saml-idp-in-your-saml-application"></a>Konfigurace Azure AD B2C v aplikaci SAML jako IdP SAML

Posledním krokem je povolení Azure AD B2C IdP jako SAML ve vaší aplikaci SAML. Každá aplikace je odlišná a postup se liší. Podrobnosti najdete v dokumentaci k vaší aplikaci.

Metadata lze v aplikaci konfigurovat jako *statická metadata* nebo *Dynamická metadata*. Ve statickém režimu zkopírujte všechna metadata nebo jejich část z metadat zásad Azure AD B2C. V dynamickém režimu zadejte adresu URL pro metadata a umožněte, aby vaše aplikace dynamicky četla metadata.

Obvykle jsou potřeba některé z těchto možností:

* **Metadata**: použijte formát `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata` .
* **Vystavitel**: hodnota požadavku SAML se `issuer` musí shodovat s jedním z identifikátorů URI nakonfigurovaných v `identifierUris` elementu manifestu registrace aplikace. Pokud název požadavku SAML `issuer` v `identifierUris` elementu neexistuje, [přidejte ho do manifestu registrace aplikace](#add-the-identifier). Například, `https://contoso.onmicrosoft.com/app-name`. 
* **Přihlašovací adresa URL/koncový bod SAML/adresa URL SAML**: ověřte hodnotu v souboru metadat zásad Azure AD B2C SAML pro `<SingleSignOnService>` element XML.
* **Certifikát**: Tento certifikát je *B2C_1A_SamlIdpCert*, ale bez privátního klíče. Získání veřejného klíče certifikátu:

    1. Přejít na adresu URL metadat uvedenou výše.
    1. Zkopírujte hodnotu v `<X509Certificate>` elementu.
    1. Vložte ho do textového souboru.
    1. Uložte textový soubor jako soubor *. cer* .

### <a name="test-with-the-saml-test-app"></a>Testování pomocí testovací aplikace SAML

K otestování konfigurace můžete použít naši [aplikaci testu SAML][samltest] :

* Aktualizujte název tenanta.
* Aktualizujte název zásady, například *B2C_1A_signup_signin_saml*.
* Zadejte tento identifikátor URI vystavitele. Použijte jeden z identifikátorů URI nalezených v `identifierUris` elementu v manifestu registrace aplikace, například `https://contoso.onmicrosoft.com/app-name` .

Vyberte **přihlašovací údaje** , které byste měli mít k dispozici na přihlašovací obrazovce uživatele. Po přihlášení je odpověď SAML vrácena zpět do ukázkové aplikace.

## <a name="supported-and-unsupported-saml-modalities"></a>Podporované a nepodporované postupy SAML

Následující scénáře aplikace SAML jsou podporovány prostřednictvím vlastního koncového bodu metadat:

* Několik adres URL pro odhlášení nebo příspěvek pro odhlašovací adresu URL v objektu aplikace nebo instančního objektu.
* Zadejte podpisový klíč pro ověření požadavků předávající strany (RP) v objektu aplikace nebo instančního objektu služby.
* Zadejte šifrovací klíč tokenu do objektu aplikace nebo instančního objektu.
* Přihlášení iniciované zprostředkovatelem identity, kde je poskytovatel identity Azure AD B2C.

## <a name="next-steps"></a>Další kroky

- Získejte testovací webovou aplikaci SAML z [Azure AD B2C úložiště GitHub Community](https://github.com/azure-ad-b2c/saml-sp-tester).
- Podívejte se na [Možnosti registrace aplikace SAML v Azure AD B2C](saml-service-provider-options.md)

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end

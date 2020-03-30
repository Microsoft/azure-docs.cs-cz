---
title: Nastavení přihlášení u poskytovatele Služby SALESForce SAML pomocí vlastních zásad
titleSuffix: Azure AD B2C
description: Nastavte přihlášení u poskytovatele Salesforce SAML pomocí vlastních zásad ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67fe9ef4ad2b025d11f88976973658c9cd8ae693
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187946"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-custom-policies-in-azure-active-directory-b2c"></a>Nastavení přihlášení u poskytovatele Salesforce SAML pomocí vlastních zásad ve službě Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek ukazuje, jak povolit přihlášení pro uživatele z organizace Salesforce pomocí [vlastních zásad](custom-policy-overview.md) v Azure Active Directory B2C (Azure AD B2C). Přihlášení povolíte přidáním [technického profilu SAML](saml-technical-profile.md) do vlastní chodnícího pravidla.

## <a name="prerequisites"></a>Požadavky

- Dokončete kroky v [části Začínáme s vlastními zásadami ve službě Azure Active Directory B2C](custom-policy-get-started.md).
- Pokud jste tak ještě neučinili, zaregistrujte si [bezplatný účet Developer Edition](https://developer.salesforce.com/signup). Tento článek používá [salesforce lightning experience](https://developer.salesforce.com/page/Lightning_Experience_FAQ).
- [Nastavte moji doménu](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) pro organizaci Salesforce.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Nastavení služby Salesforce jako poskytovatele identity

1. [Přihlaste se ke sanu Salesforce](https://login.salesforce.com/).
2. V levé nabídce v části **Nastavení**rozbalte **položku Identita**a vyberte **položku Identity Provider**.
3. Vyberte **Povolit zprostředkovatele identity**.
4. V **části Vyberte certifikát**vyberte certifikát, který má Salesforce použít ke komunikaci s Azure AD B2C. Můžete použít výchozí certifikát.
5. Klikněte na **Uložit**.

### <a name="create-a-connected-app-in-salesforce"></a>Vytvoření připojené aplikace ve Snu Salesforce

1. Na stránce **Zprostředkovatel identity** se teď vyvěsí **poskytovatelé služeb pomocí připojených aplikací. Klikněte zde.**
2. V části **Základní informace**zadejte požadované hodnoty pro připojenou aplikaci.
3. V části **Nastavení webové aplikace**zaškrtněte políčko **Povolit saml.**
4. Do pole **ID entity** zadejte následující adresu URL. Ujistěte se, že `your-tenant` nahradíte hodnotu za název vašeho klienta Azure AD B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. Do pole **Adresa URL služby ACS** zadejte následující adresu URL. Ujistěte se, že `your-tenant` nahradíte hodnotu za název vašeho klienta Azure AD B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Přejděte na konec seznamu a klepněte na tlačítko **Uložit**.

### <a name="get-the-metadata-url"></a>Získání adresy URL metadat

1. Na stránce s přehledem připojené aplikace klikněte na **Spravovat**.
2. Zkopírujte hodnotu **koncového bodu zjišťování metadat**a uložte ji. Použijete ji později v tomto článku.

### <a name="set-up-salesforce-users-to-federate"></a>Nastavení federate uživatelů Salesforce

1. Na stránce **Spravovat** připojené aplikace klikněte na **Spravovat profily**.
2. Vyberte profily (nebo skupiny uživatelů), které chcete federate s Azure AD B2C. Jako správce systému zaškrtněte **políčko Správce systému,** abyste mohli federate používat svůj účet Salesforce.

## <a name="generate-a-signing-certificate"></a>Generování podpisového certifikátu

Požadavky odeslané do Salesforce musí být podepsány Azure AD B2C. Pokud chcete vygenerovat podpisový certifikát, otevřete Azure PowerShell a spusťte následující příkazy.

> [!NOTE]
> Ujistěte se, že aktualizujete název klienta a heslo v horních dvou řádcích.

```powershell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

Musíte uložit certifikát, který jste vytvořili v tenantovi Azure AD B2C.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C výběrem directory **+ předplatné** filtr v horní nabídce a výběrem adresáře, který obsahuje vašeho klienta.
3. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Na stránce Přehled vyberte **rozhraní Identity Experience Framework**.
5. Vyberte **klávesy zásad** a pak vyberte **Přidat**.
6. V části `Upload` **Možnosti**zvolte .
7. Zadejte **Název** zásady. Například SAMLSigningCert. Předpona `B2C_1A_` se automaticky přidá k názvu klíče.
8. Vyhledejte a vyberte certifikát B2CSigningCert.pfx, který jste vytvořili.
9. Zadejte **heslo** pro certifikát.
3. Klikněte na **Vytvořit**.

## <a name="add-a-claims-provider"></a>Přidání poskytovatele deklarací identity

Pokud chcete, aby se uživatelé přihlašovali pomocí účtu Salesforce, musíte definovat účet jako poskytovatele deklarací identity, se kterým může Azure AD B2C komunikovat prostřednictvím koncového bodu. Koncový bod poskytuje sadu deklarací, které používá Azure AD B2C k ověření, že konkrétní uživatel byl ověřen.

Účet Salesforce můžete definovat jako poskytovatele deklarací identity jeho přidáním do prvku **ClaimsProviders** v rozšiřujícím souboru zásad. Další informace naleznete [v tématu definování technického profilu SAML](saml-technical-profile.md).

1. Otevřete *soubor TrustFrameworkExtensions.xml*.
1. Najít **ClaimsProviders** element. Pokud neexistuje, přidejte jej pod kořenový prvek.
1. Přidejte nového **Zprostředkovatele deklarací** identity takto:

    ```XML
    <ClaimsProvider>
      <Domain>salesforce</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="salesforce">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Aktualizujte hodnotu **PartnerEntity** pomocí adresy URL metadat Salesforce, kterou jste zkopírovali dříve.
1. Aktualizujte hodnotu obou instancí **StorageReferenceId** na název klíče podpisového certifikátu. Například B2C_1A_SAMLSigningCert.
1. Vyhledejte `<ClaimsProviders>` oddíl a přidejte následující úryvek XML. Pokud vaše zásady `SM-Saml-idp` již technicképrofil obsahují, přejděte k dalšímu kroku. Další informace naleznete v [tématu správa relace jednotného přihlášení](custom-policy-reference-sso.md).

    ```XML
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```
1. Uložte soubor.

### <a name="upload-the-extension-file-for-verification"></a>Nahrát soubor rozšíření pro ověření

Teď už jste nakonfigurovali zásady tak, aby Azure AD B2C věděl, jak komunikovat s vaším účtem Salesforce. Zkuste nahrát soubor rozšíření zásad, abyste potvrdili, že zatím nemá žádné problémy.

1. Na stránce **Vlastní zásady** v tenantovi Azure AD B2C vyberte **Zásady nahrávání**.
2. Povolte **Přepište zásadu, pokud existuje**, a potom vyhledejte soubor *TrustFrameworkExtensions.xml* a vyberte jej.
3. Klikněte na **Odeslat**.

## <a name="register-the-claims-provider"></a>Registrace zprostředkovatele deklarací identity

V tomto okamžiku byl nastaven zprostředkovatel identity, ale není k dispozici v žádné z přihlašovacích nebo přihlašovacích obrazovek. Chcete-li ji zpřístupnit, vytvořte duplikát existující cesty uživatele šablony a pak ji upravte tak, aby měla také zprostředkovatele identity Salesforce.

1. Otevřete soubor *TrustFrameworkBase.xml* ze startovního balíčku.
2. Vyhledejte a zkopírujte celý obsah prvku `Id="SignUpOrSignIn"` **UserJourney,** který obsahuje .
3. Otevřete soubor *TrustFrameworkExtensions.xml* a vyhledejte element **UserJourneys.** Pokud prvek neexistuje, přidejte jeden.
4. Vložte celý obsah **UserJourney** prvek, který jste zkopírovali jako podřízený **UserJourneys** element.
5. Přejmenujte ID cesty uživatele. Například, `SignUpSignInSalesforce`.

### <a name="display-the-button"></a>Zobrazení tlačítka

**ClaimsProviderSelection** prvek je obdobou tlačítko zprostředkovatele identity na přihlašovací nebo přihlašovací obrazovce. Pokud přidáte **claimsProviderSelection** prvek pro účet LinkedIn, nové tlačítko se zobrazí, když uživatel přistane na stránce.

1. Najděte **orchestrationstep** prvek, který zahrnuje `Order="1"` v cestě uživatele, které jste právě vytvořili.
2. V části **ClaimsProviderSelects**přidejte následující prvek. Nastavte hodnotu **TargetClaimsExchangeId** na příslušnou hodnotu, například `SalesforceExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Propojení tlačítka s akcí

Nyní, když máte tlačítko na místě, musíte jej propojit s akcí. Akce, v tomto případě je pro Azure AD B2C komunikovat s účtem Salesforce přijímat token.

1. Najít **OrchestrationStep,** `Order="2"` který zahrnuje v cestě uživatele.
2. Přidejte následující **claimsexchange** prvek ujistěte se, že používáte stejnou hodnotu pro **ID,** které jste použili pro **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="salesforce" />
    ```

    Aktualizujte hodnotu **TechnicalProfileReferenceId** na **ID** technického profilu, který jste vytvořili dříve. Například, `LinkedIn-OAUTH`.

3. Uložte soubor *TrustFrameworkExtensions.xml* a znovu jej nahrajte k ověření.

## <a name="create-an-azure-ad-b2c-application"></a>Vytvoření aplikace Azure AD B2C

Komunikace s Azure AD B2C probíhá prostřednictvím aplikace, kterou zaregistrujete ve vašem tenantovi B2C. V této části jsou uvedeny volitelné kroky, které můžete provést a vytvořit testovací aplikaci, pokud jste tak ještě neučinili.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aktualizace a testování souboru předávající strany

Aktualizujte soubor předávající strany (RP), který iniciuje cestu uživatele, kterou jste právě vytvořili:

1. Vytvořte kopii *souboru SignUpOrSignIn.xml* ve svém pracovním adresáři a přejmenujte ji. Můžete jej například přejmenovat na *SignUpSignInSalesforce.xml*.
2. Otevřete nový soubor a aktualizujte hodnotu atributu **PolicyId** pro **TrustFrameworkPolicy** s jedinečnou hodnotou. Například, `SignUpSignInSalesforce`.
3. Aktualizujte hodnotu **PublicPolicyUri** pomocí identifikátoru URI pro zásadu. Například`http://contoso.com/B2C_1A_signup_signin_salesforce`
4. Aktualizujte hodnotu atributu **ReferenceId** v **DefaultUserJourney** tak, aby odpovídalo ID nové cesty uživatele, kterou jste vytvořili (SignUpSignInSalesforce).
5. Uložte změny, nahrajte soubor a vyberte novou zásadu v seznamu.
6. Ujistěte se, že aplikace Azure AD B2C, kterou jste vytvořili, je vybraná v poli **Vybrat aplikaci** a pak ji otestujte kliknutím na **spustit nyní**.

---
title: Koncový bod UserInfo | Microsoft Docs
description: Definujte koncový bod UserInfo ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 55d3f38405d8f03ea7c13077872c2b7f7bc30b72
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120653"
---
# <a name="userinfo-endpoint"></a>Koncový bod UserInfo

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Koncový bod UserInfo je součástí specifikace OIDC ( [OpenID Connect Standard](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo) ) a je navržený tak, aby vracel deklarace identity ověřeného uživatele. Koncový bod UserInfo je definovaný v zásadách předávající strany pomocí elementu [Endpoint](relyingparty.md#endpoints) .  

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="userinfo-endpoint-overview"></a>Přehled koncového bodu UserInfo

Informace o uživateli UserJourney určuje:

- **Autorizace**: koncový bod UserInfo je chráněný pomocí nosných tokenů. Vydaný přístupový token se zobrazí v autorizační hlavičce pro koncový bod UserInfo. Tato zásada určuje technický profil, který ověřuje příchozí tokeny a extrahuje deklarace identity, jako je například objectId uživatele. ObjectId uživatele se používá k načtení deklarací identity, které se mají vrátit v odpovědi na cestu koncového bodu UserInfo. 
- **Krok orchestrace**: 
  - Krok orchestrace slouží ke shromáždění informací o uživateli. Na základě deklarací identity v rámci příchozího přístupového tokenu vyvolá cesta uživatele [Azure Active Directory technický profil](active-directory-technical-profile.md) pro načtení dat o uživateli, například čtení uživatele pomocí identifikátoru objectID. 
  - **Volitelné kroky orchestrace** – k získání dalších informací o uživateli můžete přidat další kroky orchestrace, například REST API technický profil. 
  - **Vydavatel UserInfo** – určuje seznam deklarací identity, které koncový bod UserInfo vrátí.

## <a name="create-a-userinfo-endpoint"></a>Vytvoření koncového bodu UserInfo

### <a name="1-add-the-token-issuer-technical-profile"></a>1. Přidejte technický profil vystavitele tokenu.

1. Otevřete soubor *TrustFrameworkExtensions.xml* .
1. Pokud ještě neexistuje, přidejte element ClaimsProvider a jeho podřízené prvky jako první prvek pod prvkem BuildingBlocks.
1. Přidejte následujícího zprostředkovatele deklarací identity:

    ```xml
    <!-- 
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Token Issuer</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="UserInfoIssuer">
            <DisplayName>JSON Issuer</DisplayName>
            <Protocol Name="None" />
            <OutputTokenFormat>JSON</OutputTokenFormat>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <!-- The Below claims are what will be returned on the UserInfo Endpoint if in the Claims Bag-->
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId"/>
              <InputClaim ClaimTypeReferenceId="givenName"/>
              <InputClaim ClaimTypeReferenceId="surname"/>
              <InputClaim ClaimTypeReferenceId="displayName"/>
              <InputClaim ClaimTypeReferenceId="signInNames.emailAddress"/>
            </InputClaims>
          </TechnicalProfile>
          <TechnicalProfile Id="UserInfoAuthorization">
            <DisplayName>UserInfo authorization</DisplayName>
            <Protocol Name="None" />
            <InputTokenFormat>JWT</InputTokenFormat>
            <Metadata>
              <!-- Update the Issuer and Audience below -->
              <!-- Audience is optional, Issuer is required-->
              <Item Key="issuer">https://yourtenant.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/</Item>
              <Item Key="audience">[ "22222222-2222-2222-2222-222222222222", "33333333-3333-3333-3333-333333333333" ]</Item>
              <Item Key="client_assertion_type">urn:ietf:params:oauth:client-assertion-type:jwt-bearer</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
              <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" PartnerClaimType="email"/>
              <!-- Optional claims to read from the access token. -->
              <!-- <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
                 <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
                 <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/> -->
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!-- 
    </ClaimsProviders> -->
    ``` 

1. Oddíl InputClaims v rámci technického profilu **UserInfoIssuer** určuje atributy, které chcete vrátit. Technický profil UserInfoIssuer se volá na konci cesty uživatele. 
1. Technický profil **UserInfoAuthorization** ověří podpis, název vystavitele a cílovou skupinu tokenů a extrahuje deklaraci identity z příchozího tokenu. Změňte následující metadata tak, aby odrážela vaše prostředí:
    1. **Vystavitel** – tato hodnota musí být shodná s `iss` deklarací identity v rámci deklarace přístupového tokenu. Tokeny vydané Azure AD B2C používají Vystavitel ve formátu `https://yourtenant.b2clogin.com/your-tenant-id/v2.0/` . Přečtěte si další informace o [přizpůsobení tokenu](configure-tokens.md).
    1. **IdTokenAudience** -musí být identické s `aud` deklarací identity v rámci deklarace přístupového tokenu. V Azure AD B2C `aud` deklarace identity je ID vaší aplikace předávající strany. Tato hodnota je kolekce a podporuje více hodnot pomocí oddělovače čárky.

        V následujícím přístupovém tokenu `iss` je hodnota deklarace identity `https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/` . `aud`Hodnota deklarace identity je `22222222-2222-2222-2222-222222222222` .

        ```json
        {
          "exp": 1605549468,
          "nbf": 1605545868,
          "ver": "1.0",
          "iss": "https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/",
          "sub": "44444444-4444-4444-4444-444444444444",
          "aud": "22222222-2222-2222-2222-222222222222",
          "acr": "b2c_1a_signup_signin",
          "nonce": "defaultNonce",
          "iat": 1605545868,
          "auth_time": 1605545868,
          "name": "John Smith",
          "given_name": "John",
          "family_name": "Smith",
          "tid": "11111111-1111-1111-1111-111111111111"
        }
        ```
    
1.  Element OutputClaims Technical Profile **UserInfoAuthorization** určuje atributy, které chcete číst z přístupového tokenu. **ClaimTypeReferenceId** je odkaz na typ deklarace identity. Volitelná **PartnerClaimType** je název deklarace identity definované v přístupovém tokenu.



### <a name="2-add-the-userjourney-element"></a>2. Přidejte element UserJourney. 

Element [UserJourney](userjourneys.md) definuje cestu, kterou uživatel provede při interakci s aplikací. Přidejte element **userjourney** , pokud neexistuje s **UserJourney** identifikovaným jako `UserInfoJourney` :

```xml
<!-- 
<UserJourneys> -->
  <UserJourney Id="UserInfoJourney" DefaultCpimIssuerTechnicalProfileReferenceId="UserInfoIssuer">
    <Authorization>
      <AuthorizationTechnicalProfiles>
        <AuthorizationTechnicalProfile ReferenceId="UserInfoAuthorization" />
      </AuthorizationTechnicalProfiles>
    </Authorization>
    <OrchestrationSteps >
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>objectId</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges UserIdentity="false">
          <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="UserInfoIssuer" />
    </OrchestrationSteps>
  </UserJourney>
<!-- 
</UserJourneys> -->
```

### <a name="3-include-the-endpoint-to-the-relying-party-policy"></a>3. zahrňte koncový bod do zásady předávající strany.

Pokud chcete do aplikace předávající strany zahrnout koncový bod UserInfo, přidejte do souboru *SocialAndLocalAccounts/SignUpOrSignIn.xml* element [Endpoint](relyingparty.md#endpoints) . 

```xml
<Endpoints>
  <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
</Endpoints>
```

Dokončený element předávající strany bude následující:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="yourtenant.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin">
  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <Endpoints>
      <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
    </Endpoints>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

### <a name="4-upload-the-files"></a>4. Nahrajte soubory.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **architekturu prostředí identity**.
1. Na stránce **vlastní zásady** vyberte **nahrát vlastní zásadu**.
1. Vyberte možnost **Přepsat vlastní zásady, pokud už existuje**, a pak vyhledejte a vyberte soubor *TrustframeworkExtensions.xml* .
1. Klikněte na **Odeslat**.
1. Opakujte kroky 5 až 7 pro soubor předávající strany, například *SignUpOrSignIn.xml*.

## <a name="calling-the-userinfo-endpoint"></a>Volání koncového bodu UserInfo

Koncový bod UserInfo využívá standardní rozhraní API OAuth2 nosiče, které se volá pomocí přístupového tokenu, který jste dostali při získávání tokenu pro vaši aplikaci. Vrátí odpověď JSON obsahující deklarace identity uživatele. Koncový bod UserInfo je hostovaný na Azure AD B2C v:

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/openid/v2.0/userinfo
```

Pole obsahuje seznam/.well-known konfigurace koncového bodu (dokument zjišťování OpenID Connect) `userinfo_endpoint` . Koncový bod UserInfo můžete programově zjistit pomocí koncového bodu/.well-known konfigurace na adrese: 

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/v2.0/.well-known/openid-configuration 
```

### <a name="test-the-policy"></a>Testování zásad

1. V části **vlastní zásady** vyberte zásadu, se kterou jste mohli koncový bod UserInfo integrovat. Například *B2C_1A_SignUpOrSignIn*.
1. Vyberte **Spustit nyní**. 
1. V části **Vybrat aplikaci** vyberte aplikaci, kterou jste předtím zaregistrovali. V **možnosti vybrat adresu URL odpovědi** zvolte `https://jwt.ms` . Další informace najdete v tématu [registrace webové aplikace v Azure Active Directory B2C](tutorial-register-applications.md).
1. Zaregistrujte se nebo se přihlaste pomocí e-mailové adresy nebo účtu na sociálních sítích.
1. Zkopírujte id_token z webu z jeho kódovaného formátu [https://jwt.ms](https://jwt.ms) . Tuto možnost můžete použít k odeslání žádosti o získání na koncový bod UserInfo a načtení informací o uživateli.
1. Odešlete požadavek GET na koncový bod UserInfo a načtěte informace o uživateli.

```http
GET /yourtenant.onmicrosoft.com/b2c_1a_signup_signin/openid/v2.0/userinfo
Host: b2cninja.b2clogin.com
Authorization: Bearer <your ID token>
```

Úspěšná odpověď by vypadala takto:

```json
{
    "objectId": "44444444-4444-4444-4444-444444444444",
    "givenName": "John",
    "surname": "Smith",
    "displayName": "John Smith",
    "signInNames.emailAddress": "john.s@contoso.com"
}
```

## <a name="next-steps"></a>Další kroky

- Příklad zásad koncového bodu pro UserInfo najdete na [GitHubu](https://github.com/azure-ad-b2c/samples/tree/master/policies/user-info-endpoint).

::: zone-end

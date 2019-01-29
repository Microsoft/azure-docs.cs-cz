---
title: Nakonfigurujte tok přihlašovacího hesla vlastníka prostředku v Azure Active Directory B2C | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat tok přihlašovacího hesla vlastníka prostředku v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 7dffa1480be73f1dbf5e99d11fd8d33eb2ab9038
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55196408"
---
# <a name="configure-the-resource-owner-password-credentials-flow-in-azure-active-directory-b2c-using-a-custom-policy"></a>Konfigurace tok vlastníka prostředku heslo přihlašovacích údajů v Azure Active Directory B2C pomocí vlastních zásad

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V Azure Active Directory (Azure AD) B2C tok přihlašovacích údajů (ROPC) hesla vlastníka prostředku je tok, který standardní ověřování OAuth. V tomto toku aplikace, označované také jako přijímající strany vymění platné přihlašovací údaje pro tokeny. Přihlašovací údaje zahrnují ID uživatele a heslo. Jsou tokeny, vrátil ID token, přístupový token a aktualizační token. 

Ve službě ROPC flow jsou podporovány následující možnosti:

- **Nativní klient systému** – interakce s uživatelem během ověřování se stane, když kód běží na uživatele na straně zařízení.
- **Tok veřejným klientem** – jenom uživatele, přihlašovací údaje, které jsou shromážděné aplikace jsou odeslány ve volání rozhraní API. Přihlašovací údaje aplikace neodešlou.
- **Přidání nových deklarací identity** – ID tokenu obsah lze změnit pro přidání nových deklarací identity. 

Nejsou podporovány v následujících tocích:

- **Na serveru** -systému identity protection potřebuje shromážděných z volající (Nativní klient) jako součást interakce spolehlivé IP adresu. Ve volání rozhraní API na straně serveru je použít jenom IP adresa serveru. Pokud příliš mnoho přihlášení selže, může systém ochrany identit podívejte se na opakované IP adresu jako útočník.
- **Jedna stránka aplikace** -front-endové aplikace, která je primárně v JavaScriptu. Aplikace často, jsou zapsána pomocí architektury, jako jsou AngularJS, Ember.js nebo Durandal.
- **Tok důvěrnému klientovi** – ověřit ID klienta aplikace, ale není tajný klíč aplikace.

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v [začít pracovat s vlastními zásadami v Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Registrace aplikace

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**. 
4. Vyberte **aplikací**a pak vyberte **přidat**.
5. Zadejte název aplikace, jako například *ROPC_Auth_app*.
6. Vyberte **ne** pro **webová aplikace/webové rozhraní API**a pak vyberte **Ano** pro **nativního klienta**.
7. Nechte ostatní hodnoty, jak jsou a pak vyberte **vytvořit**.
8. Vyberte novou aplikaci a zaznamenejte ID aplikace pro pozdější použití.

##  <a name="create-a-resource-owner-policy"></a>Vytvoření zásady vlastníka prostředku

1. Otevřít *TrustFrameworkExtensions.xml* souboru.
2. Pokud již neexistuje, přidejte **ClaimsSchema** elementu a jeho podřízené prvky jako první prvek v rámci **BuildingBlocks** element:

    ```XML
    <ClaimsSchema>
      <ClaimType Id="logonIdentifier">
        <DisplayName>User name or email address that the user can use to sign in</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="resource">
        <DisplayName>The resource parameter passes to the ROPC endpoint</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokenIssuedOnDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokensValidFromDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
    </ClaimsSchema>
    ```

3. Po **ClaimsSchema**, přidejte **ClaimsTransformations** elementu a jeho podřízených prvků, které **BuildingBlocks** element:

    ```XML
    <ClaimsTransformations>
      <ClaimsTransformation Id="CreateSubjectClaimFromObjectID" TransformationMethod="CreateStringClaim">
        <InputParameters>
          <InputParameter Id="value" DataType="string" Value="Not supported currently. Use oid claim." />
        </InputParameters>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="sub" TransformationClaimType="createdClaim" />
        </OutputClaims>
      </ClaimsTransformation>
    
      <ClaimsTransformation Id="AssertRefreshTokenIssuedLaterThanValidFromDate" TransformationMethod="AssertDateTimeIsGreaterThan">
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" TransformationClaimType="leftOperand" />
          <InputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" TransformationClaimType="rightOperand" />
        </InputClaims>
        <InputParameters>
          <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
          <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
        </InputParameters>
      </ClaimsTransformation>
    </ClaimsTransformations>
    ```

4. Vyhledejte **ClaimsProvider** element, který má **DisplayName** z `Local Account SignIn` a přidání těchto technický profil:

    ```XML
    <TechnicalProfile Id="ResourceOwnerPasswordCredentials-OAUTH2">
      <DisplayName>Local Account SignIn</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
        <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
        <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
        <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
        <Item Key="response_types">id_token</Item>
        <Item Key="response_mode">query</Item>
        <Item Key="scope">email openid</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="logonIdentifier" PartnerClaimType="username" Required="true" DefaultValue="{OIDC:Username}"/>
        <InputClaim ClaimTypeReferenceId="password" Required="true" DefaultValue="{OIDC:Password}" />
        <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
        <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
        <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
        <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="00000000-0000-0000-0000-000000000000" />
        <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="00000000-0000-0000-0000-000000000000" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
        <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
      </OutputClaimsTransformations>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
    ```

    Nahraďte **DefaultValue** z **client_id** a **ID_prostředku** s ID aplikace, kterou jste vytvořili v aplikaci ProxyIdentityExperienceFramework požadovaný kurz.

5. Přidejte následující **ClaimsProvider** elementy na své technické profily **ClaimsProviders** element:

    ```XML
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserReadUsingObjectId-CheckRefreshTokenDate">
          <Metadata>
            <Item Key="Operation">Read</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="AssertRefreshTokenIssuedLaterThanValidFromDate" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
          </OutputClaimsTransformations>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-RefreshTokenReadAndSetup">
          <DisplayName>Trustframework Policy Engine Refresh Token Setup Technical Profile</DisplayName>
          <Protocol Name="None" />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" />
          </OutputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="JwtIssuer">
          <Metadata>
            <!-- Point to the redeem refresh token user journey-->
            <Item Key="RefreshTokenUserJourneyId">ResourceOwnerPasswordCredentials-RedeemRefreshToken</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>    
    ```

6. Přidat **Userjourney** elementu a jeho podřízených prvků, které **TrustFrameworkPolicy** element:

    ```XML
    <UserJourney Id="ResourceOwnerPasswordCredentials">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
        <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="ResourceOwnerFlow" TechnicalProfileReferenceId="ResourceOwnerPasswordCredentials-OAUTH2" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    <UserJourney Id="ResourceOwnerPasswordCredentials-RedeemRefreshToken">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="RefreshTokenSetupExchange" TechnicalProfileReferenceId="SM-RefreshTokenReadAndSetup" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="CheckRefreshTokenDateFromAadExchange" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId-CheckRefreshTokenDate" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    ```

7. Na **vlastní zásady** stránky ve vašem tenantovi Azure AD B2C, vyberte **nahrát zásady**.
8. Povolit **přepsat zásady, pokud existuje**a poté vyhledejte a vyberte *TrustFrameworkExtensions.xml* souboru.
9. Klikněte na **Odeslat**.

## <a name="create-a-relying-party-file"></a>Vytvořit soubor předávající strany

Dále aktualizujte soubor předávající strany, který zahájí cesty uživatele, který jste vytvořili:

1. Vytvořte kopii *SignUpOrSignin.xml* souborů ve vašem pracovním adresáři a přejmenujte ho na *ROPC_Auth.xml*.
2. Otevřete nový soubor a změňte hodnotu **PolicyId** atributu **TrustFrameworkPolicy** na jedinečnou hodnotu. ID zásad je název zásady. Například **B2C_1A_ROPC_Auth**.
3. Změňte hodnotu **ReferenceId** atribut **DefaultUserJourney** k `ResourceOwnerPasswordCredentials`.
4. Změnit **OutputClaims** element tak, aby obsahovala pouze následující deklarace:
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="givenName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="surname" DefaultValue="" />
    ```

5. Na **vlastní zásady** stránky ve vašem tenantovi Azure AD B2C, vyberte **nahrát zásady**.
6. Povolit **přepsat zásady, pokud existuje**a poté vyhledejte a vyberte *TrustFrameworkExtensions.xml* souboru.
7. Klikněte na **Odeslat**.

## <a name="test-the-policy"></a>Testování zásad

Generovat volání rozhraní API pomocí aplikace pro vývoj oblíbených rozhraní API a zkontrolovat odpověď, chcete-li ladit vaše zásady. Vytvoření volání jako v tomto příkladu pomocí následujících informací jako text požadavku POST:

`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

- Nahraďte `your-tenant-name` s názvem vašeho tenanta Azure AD B2C.
- Nahraďte `B2C_1A_ROPC_Auth` úplným názvem zásady pověření heslo vlastníka prostředku.

| Klíč | Value |
| --- | ----- |
| uživatelské jméno | `user-account` |
| heslo | `password1` |
| grant_type | heslo |
| scope | openid `application-id` offline_access |
| client_id | `application-id` |
| response_type | Token požadavku id_token |

- Nahraďte `user-account` s názvem účet uživatele ve vašem tenantovi.
- Nahraďte `password1` s heslem uživatelského účtu.
- Nahraďte `application-id` s ID aplikace z *ROPC_Auth_app* registrace. 
- *Offline_access* je volitelný, pokud chcete dostávat obnovovací token.

Skutečné požadavek POST bude vypadat jako v následujícím příkladu:

```HTTPS
POST /yourtenant.onmicrosoft.com/oauth2/v2.0/token?B2C_1_ROPC_Auth HTTP/1.1
Host: yourtenant.b2clogin.com
Content-Type: application/x-www-form-urlencoded

username=contosouser.outlook.com.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```


Úspěšná odpověď s offline přístupem bude vypadat jako v následujícím příkladu:

```JSON
{ 
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki...", 
    "token_type": "Bearer", 
    "expires_in": "3600", 
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6Ij...", 
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki..." 
} 
```

## <a name="redeem-a-refresh-token"></a>Uplatnění obnovovací token

Vytvoření volání POST podobný tomu, který je vidět tady. Pomocí informací v následující tabulce jako text žádosti:

`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

- Nahraďte `your-tenant-name` s názvem vašeho tenanta Azure AD B2C.
- Nahraďte `B2C_1A_ROPC_Auth` úplným názvem zásady pověření heslo vlastníka prostředku.

| Klíč | Value |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | `application-id` |
| prostředek | `application-id` |
| refresh_token | `refresh-token` |

- Nahraďte `application-id` s ID aplikace z *ROPC_Auth_app* registrace.
- Nahraďte `refresh-token` s **refresh_token** , který byl odeslán zpět v předchozí odpovědi. 

Úspěšná odpověď bude vypadat jako v následujícím příkladu:

```JSON
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQndhT...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQn...",
    "token_type": "Bearer",
    "not_before": 1533672990,
    "expires_in": 3600,
    "expires_on": 1533676590,
    "resource": "bef2222d56-552f-4a5b-b90a-1988a7d634c3",
    "id_token_expires_in": 3600,
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI1MTZmYzA2NS1mZjM2LTRiOTMtYWE1YS1kNmVlZGE3Y2JhYzgiLCJzdWIiOm51bGwsIm5hbWUiOiJEYXZpZE11IiwicHJlZmVycmVkX3VzZXJuYW1lIjpudWxsLCJpZHAiOiJMb2NhbEFjY291bnQifQ",
    "refresh_token": "eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMCIsInppcCI6IkRlZmxhdGUiLCJzZXIiOiIxLjAi...",
    "refresh_token_expires_in": 1209600
}
```

## <a name="use-a-native-sdk-or-app-auth"></a>Použít nativní SDK nebo ověřování aplikace

Azure AD B2C splňuje standardy veřejným klientem přihlašovacího hesla vlastníka prostředku OAuth 2.0 a by měl být kompatibilní s většina klientské sady SDK. Nejnovější informace najdete v tématu [Native App SDK pro OAuth 2.0 a OpenID Connect, implementace moderní osvědčené postupy](https://appauth.io/).

## <a name="next-steps"></a>Další postup

- Zobrazit kompletní příklad v tomto scénáři [Úvodní sada vlastních zásad pro Azure Active Directory B2C](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/source/aadb2c-ief-ropc).
- Další informace o tokeny, které používají Azure Active Directory B2C v [Token odkaz](active-directory-b2c-reference-tokens.md).



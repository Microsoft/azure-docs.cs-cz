---
title: Nastavení toku přihlašovacích údajů k heslům vlastníka prostředku
titleSuffix: Azure AD B2C
description: Přečtěte si, jak v Azure Active Directory B2C nastavit tok pověření pro heslo vlastníka prostředku (ROPC).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/12/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 6afe9814757d894c61ab3154bbff02791a830ddd
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895066"
---
# <a name="set-up-a-resource-owner-password-credentials-flow-in-azure-active-directory-b2c"></a>Nastavení toku přihlašovacích údajů pro heslo vlastníka prostředku v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

V Azure Active Directory B2C (Azure AD B2C) je tok přihlašovacích údajů vlastníka prostředku (ROPC) standardním ověřovacím tokem protokolu OAuth. V tomto toku aplikace, označované také jako předávající strana, vyměňuje platné přihlašovací údaje pro tokeny. Přihlašovací údaje zahrnují ID uživatele a heslo. Vrácené tokeny jsou token ID, přístupový token a obnovovací token.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]


## <a name="ropc-flow-notes"></a>Poznámky ke ROPC toku

V Azure Active Directory B2C (Azure AD B2C) jsou podporovány následující možnosti:

- **Nativní klient**: během ověřování dojde k interakci uživatele, když se kód spouští na zařízení na straně uživatele. Zařízení může být mobilní aplikace, která běží v nativním operačním systému, jako je Android a iOS.
- **Veřejný klientský tok**: v volání rozhraní API se odešlou jenom přihlašovací údaje uživatele shromážděné aplikací. Přihlašovací údaje aplikace se neodesílají.
- **Přidat nové deklarace identity**: obsah TOKENu ID se dá změnit tak, aby se přidaly nové deklarace identity.

Následující toky nejsou podporovány:

- **Server-Server**: systém ochrany identit potřebuje spolehlivou IP adresu získanou od volajícího (nativního klienta) v rámci interakce. V případě volání rozhraní API na straně serveru se používá jenom IP adresa serveru. Pokud dojde k překročení dynamické prahové hodnoty neúspěšných ověření, může systém Identity Protection identifikovat opakovanou IP adresu jako útočníka.
- **Důvěrný tok klienta**: ID klienta aplikace je ověřeno, ale tajný klíč aplikace není ověřen.

Při použití toku ROPC Vezměte v úvahu následující skutečnosti:

- ROPC nefunguje, pokud dojde k přerušení ověřovacího toku, který potřebuje zásah uživatele. Například pokud vypršela platnost hesla nebo je nutné ho změnit, je vyžadováno [ověřování Multi-Factor Authentication](multi-factor-authentication.md) nebo další informace, které je potřeba shromáždit během přihlašování (například souhlas uživatele).
- ROPC podporuje pouze místní účty. Uživatelé se nemůžou přihlašovat pomocí [federovaných zprostředkovatelů identity](add-identity-provider.md) , jako je Microsoft, Google +, Twitter, AD-FS nebo Facebook.
- [Správa relací](session-behavior.md), včetně [zůstatku přihlášeného (políčko zůstat přihlášeni)](session-behavior.md#enable-keep-me-signed-in-kmsi), se nedá použít.


## <a name="register-an-application"></a>Registrace aplikace

[!INCLUDE [active-directory-b2c-appreg-ropc](../../includes/active-directory-b2c-appreg-ropc.md)]

::: zone pivot="b2c-user-flow"

##  <a name="create-a-resource-owner-user-flow"></a>Vytvoření toku uživatele vlastníka prostředku

1. Přihlaste se k webu Azure Portal jako globální správce vašeho tenanta Azure AD B2C.
2. Pokud chcete přepnout na svého tenanta Azure AD B2C, vyberte v pravém horním rohu portálu adresář B2C.
3. Vyberte **toky uživatelů** a vyberte **Nový tok uživatele**.
4. Vyberte možnost **Přihlásit se pomocí přihlašovacích údajů k heslu vlastníka prostředku (ROPC)**.
5. V části **verze** se ujistěte, že je vybraná možnost **Náhled** , a pak vyberte **vytvořit**.
7. Zadejte název toku uživatele, například *ROPC_Auth*.
8. V části **deklarace aplikací** klikněte na **Zobrazit více**.
9. Vyberte deklarace identity aplikace, které potřebujete pro vaši aplikaci, například zobrazované jméno, e-mailovou adresu a poskytovatele identity.
10. Vyberte **OK** a potom vyberte **Vytvořit**.

::: zone-end

::: zone pivot="b2c-custom-policy"

##  <a name="create-a-resource-owner-policy"></a>Vytvoření zásady vlastníka prostředku

1. Otevřete soubor *TrustFrameworkExtensions.xml* .
2. Pokud ještě neexistuje, přidejte element **ClaimsSchema** a jeho podřízené prvky jako první prvek pod prvkem **BuildingBlocks** :

    ```xml
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

3. Po **ClaimsSchema** přidejte element **ClaimsTransformations** a jeho podřízené prvky do elementu **BuildingBlocks** :

    ```xml
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

4. Vyhledejte element **ClaimsProvider** , který má **DisplayName** `Local Account SignIn` a přidejte následující technický profil:

    ```xml
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
        <Item Key="grant_type">password</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="logonIdentifier" PartnerClaimType="username" Required="true" DefaultValue="{OIDC:Username}"/>
        <InputClaim ClaimTypeReferenceId="password" Required="true" DefaultValue="{OIDC:Password}" />
        <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
        <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
        <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
        <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppId" />
        <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppId" />
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

    Nahraďte  hodnotu DefaultValue **client_id** číslem ID aplikace ProxyIdentityExperienceFramework, kterou jste vytvořili v kurzu požadavků. Potom nahraďte parametr **DefaultValue** **resource_id** ID aplikace aplikace IdentityExperienceFramework, kterou jste vytvořili také v kurzu požadavků.

5. Přidejte následující prvky **ClaimsProvider** s jejich technickými profily do prvku **ClaimsProviders** :

    ```xml
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

6. Přidejte element **userjourney** a jeho podřízené prvky do elementu **TrustFrameworkPolicy** :

    ```xml
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

7. Na stránce **vlastní zásady** ve vašem tenantovi Azure AD B2C vyberte **Odeslat zásadu**.
8. Pokud existuje, zapněte **zásadu přepsat** a pak vyhledejte a vyberte soubor *TrustFrameworkExtensions.xml* .
9. Klikněte na **Odeslat**.

## <a name="create-a-relying-party-file"></a>Vytvoření souboru předávající strany

Dále aktualizujte soubor předávající strany, který zahájí cestu uživatele, kterou jste vytvořili:

1. Vytvořte kopii souboru *SignUpOrSignin.xml* v pracovním adresáři a přejmenujte ho na *ROPC_Auth.xml*.
2. Otevřete nový soubor a změňte hodnotu atributu **PolicyId** pro **TrustFrameworkPolicy** na jedinečnou hodnotu. ID zásady je název vaší zásady. Například **B2C_1A_ROPC_Auth**.
3. Změňte hodnotu atributu **ReferenceId** v **DefaultUserJourney** na `ResourceOwnerPasswordCredentials` .
4. Změňte element **OutputClaims** tak, aby obsahoval pouze následující deklarace identity:

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="givenName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="surname" DefaultValue="" />
    ```

5. Na stránce **vlastní zásady** ve vašem tenantovi Azure AD B2C vyberte **Odeslat zásadu**.
6. Pokud existuje, zapněte **zásadu přepsat** a pak vyhledejte a vyberte soubor *ROPC_Auth.xml* .
7. Klikněte na **Odeslat**.


::: zone-end

## <a name="test-the-ropc-flow"></a>Testování toku ROPC

Použijte svou oblíbenou aplikaci pro vývoj rozhraní API k vygenerování volání rozhraní API a Projděte si odpověď pro ladění zásad. Sestavte volání jako v tomto příkladu s následujícími informacemi jako text požadavku POST:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

- Nahraďte `<tenant-name>` názvem vašeho tenanta Azure AD B2C.
- Nahraďte `B2C_1A_ROPC_Auth` úplným názvem zásady pro přihlašovací údaje hesla vlastníka prostředku.

| Klíč | Hodnota |
| --- | ----- |
| username | `user-account` |
| heslo | `password1` |
| grant_type | heslo |
| scope | OpenID `application-id` offline_access |
| client_id | `application-id` |
| response_type | id_token tokenu |

- Nahraďte `user-account` názvem uživatelského účtu ve vašem tenantovi.
- Nahraďte `password1` heslem uživatelského účtu.
- Nahraďte `application-id` ID aplikace z registrace *ROPC_Auth_app* .
- *Offline_access* je volitelné, pokud chcete získat obnovovací token.

Skutečný požadavek POST vypadá jako v následujícím příkladu:

```https
POST /<tenant-name>.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1A_ROPC_Auth HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

username=contosouser.outlook.com.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```

Úspěšná odpověď s offline přístupem vypadá jako v následujícím příkladu:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki...",
    "token_type": "Bearer",
    "expires_in": "3600",
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6Ij...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki..."
}
```

## <a name="redeem-a-refresh-token"></a>Uplatnění obnovovacího tokenu

Sestavte následné volání jako tu, který je zde zobrazen. Použijte informace v následující tabulce jako text žádosti:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

- Nahraďte `<tenant-name>` názvem vašeho tenanta Azure AD B2C.
- Nahraďte `B2C_1A_ROPC_Auth` úplným názvem zásady pro přihlašovací údaje hesla vlastníka prostředku.

| Klíč | Hodnota |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | `application-id` |
| prostředek | `application-id` |
| refresh_token | `refresh-token` |

- Nahraďte `application-id` ID aplikace z registrace *ROPC_Auth_app* .
- Nahraďte `refresh-token` **refresh_token** , která se vrátila zpět v předchozí odpovědi.

Úspěšná odpověď vypadá jako v následujícím příkladu:

```json
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

## <a name="use-a-native-sdk-or-app-auth"></a>Použití nativní sady SDK nebo App-Auth

Azure AD B2C splňuje předpisy OAuth 2,0 pro přihlašovací údaje k heslu veřejného klienta a měly by být kompatibilní s většinou klientských sad SDK. Nejnovější informace najdete v tématu [nativní sada App SDK pro OAuth 2,0 a OpenID Connect implementující moderní osvědčené postupy](https://appauth.io/).

## <a name="next-steps"></a>Další kroky

Stáhněte si pracovní ukázky, které jsou nakonfigurované pro použití s Azure AD B2C z GitHubu, [pro Android](https://aka.ms/aadb2cappauthropc) a [iOS](https://aka.ms/aadb2ciosappauthropc).

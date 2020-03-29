---
title: Konfigurace změny hesla pomocí vlastních zásad
titleSuffix: Azure AD B2C
description: Zjistěte, jak uživatelům povolit změnu hesla pomocí vlastních zásad ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2c351f8a95110a32c53c68c5eb6095918578bc5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189170"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurace změny hesla pomocí vlastních zásad ve službě Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ve službě Azure Active Directory B2C (Azure AD B2C) můžete uživatelům, kteří jsou přihlášeni pomocí místního účtu, povolit, aby si změnili heslo, aniž by museli prokázat svou pravost ověřením e-mailu. Pokud relace vyprší v době, kdy se uživatel dostane k toku změny hesla, zobrazí se výzva k opětovnému přihlášení. Tento článek ukazuje, jak nakonfigurovat změnu hesla ve [vlastních zásadách](custom-policy-overview.md). Je také možné nakonfigurovat [samoobslužné resetování hesla](user-flow-self-service-password-reset.md) pro toky uživatelů.

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v [části Začínáme s vlastními zásadami ve službě Active Directory B2C](custom-policy-get-started.md).

## <a name="add-the-elements"></a>Přidejte prvky

1. Otevřete soubor *TrustframeworkExtensions.xml* a přidejte následující **claimtype** element s identifikátorem `oldPassword` prvku [ClaimsSchema:](claimsschema.md)

    ```XML
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. A [ClaimsProvider](claimsproviders.md) Element obsahuje technický profil, který ověřuje uživatele. Do prvku ClaimsProviders přidejte následující zprostředkovatele deklarací **identity:**

    ```XML
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive-PasswordChange">
            <DisplayName>Local Account SignIn</DisplayName>
            <Protocol Name="OpenIdConnect" />
            <Metadata>
              <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
              <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
              <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
              <Item Key="ProviderName">https://sts.windows.net/</Item>
              <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
              <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
              <Item Key="response_types">id_token</Item>
              <Item Key="response_mode">query</Item>
              <Item Key="scope">email openid</Item>
              <Item Key="UsePolicyInRedirectUri">false</Item>
              <Item Key="HttpBinding">POST</Item>
              <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
              <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="username" Required="true" />
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
              <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
              <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
              <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
              <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
              <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid" />
              <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
              <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
              <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
              <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
              <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
      <ClaimsProvider>
        <DisplayName>Local Account Password Change</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
            <DisplayName>Change password (username)</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
              <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
            </OutputClaims>
            <ValidationTechnicalProfiles>
              <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
              <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
            </ValidationTechnicalProfiles>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

    Nahraďte `IdentityExperienceFrameworkAppId` ID aplikace IdentityExperienceFramework, kterou jste vytvořili v kurzu předpokladu. Nahraďte `ProxyIdentityExperienceFrameworkAppId` ID aplikace proxyIdentityExperienceFramework, kterou jste také dříve vytvořili.

3. [UserJourney](userjourneys.md) Element definuje cestu, kterou uživatel provede při interakci s vaší aplikací. Přidejte Element **UserJourneys,** pokud neexistuje s **UserJourney** identifikován jako `PasswordChange`:

    ```XML
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

4. Uložte soubor zásad *TrustFrameworkExtensions.xml.*
5. Zkopírujte soubor *ProfileEdit.xml,* který jste stáhli se startovacím balíčkem, a pojmenujte jej *ProfileEditPasswordChange.xml*.
6. Otevřete nový soubor a aktualizujte atribut **PolicyId** s jedinečnou hodnotou. Tato hodnota je název zásady. Například *B2C_1A_profile_edit_password_change*.
7. Upravte atribut **ReferenceId** v aplikaci `<DefaultUserJourney>` tak, aby odpovídal id nové cesty uživatele, kterou jste vytvořili. Například *PasswordChange*.
8. Uložte provedené změny.

Vzorové zásady naleznete [zde](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change).

## <a name="test-your-policy"></a>Otestujte své zásady

Při testování aplikací v Azure AD B2C, může být užitečné mít token `https://jwt.ms` Azure AD B2C vrácena, aby bylo možné zkontrolovat deklarace identity v něm.

### <a name="upload-the-files"></a>Nahrání souborů

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C výběrem directory **+ předplatné** filtr v horní nabídce a výběrem adresáře, který obsahuje vašeho klienta.
3. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **rozhraní Identity Experience Framework**.
5. Na stránce Vlastní zásady klikněte na **Nahrát zásady**.
6. Vyberte **Přepsat zásadu, pokud existuje**, a pak vyhledejte a vyberte soubor *TrustframeworkExtensions.xml.*
7. Klikněte na **Odeslat**.
8. Opakujte kroky 5 až 7 pro soubor předávající strany, například *ProfileEditPasswordChange.xml*.

### <a name="run-the-policy"></a>Spuštění zásady

1. Otevřete zásadu, kterou jste změnili. Například *B2C_1A_profile_edit_password_change*.
2. V **aplikaci**vyberte aplikaci, kterou jste dříve zaregistrovali. Chcete-li zobrazit **Reply URL** token, měla `https://jwt.ms`by se zobrazit adresa URL odpovědi .
3. Klikněte na **Spustit**. Přihlaste se pomocí acouuntu, který jste dříve vytvořili. Nyní byste měli mít možnost změnit heslo.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak můžete [nakonfigurovat složitost hesla pomocí vlastních zásad ve službě Azure Active Directory B2C](custom-policy-password-complexity.md).

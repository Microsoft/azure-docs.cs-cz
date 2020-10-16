---
title: Konfigurace změny hesla pomocí vlastních zásad
titleSuffix: Azure AD B2C
description: Naučte se, jak uživatelům povolit změnu hesla pomocí vlastních zásad v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1c942d52bbcdad711115d81a78395979c507784b
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131747"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurace změny hesla pomocí vlastních zásad v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V Azure Active Directory B2C (Azure AD B2C) můžete uživatelům, kteří jsou přihlášení pomocí místního účtu, povolit změnu hesla, aniž by museli dokázat ověření pomocí e-mailu. Pokud platnost relace vyprší až do doby, kdy uživatel získá tok změny hesla, zobrazí se výzva k opětovnému přihlášení. V tomto článku se dozvíte, jak nakonfigurovat změnu hesla ve [vlastních zásadách](custom-policy-overview.md). Pro toky uživatelů je také možné nakonfigurovat [Samoobslužné resetování hesla](user-flow-self-service-password-reset.md) .

## <a name="prerequisites"></a>Předpoklady

Proveďte kroky v části Začínáme [s vlastními zásadami v Active Directory B2C](custom-policy-get-started.md).

## <a name="add-the-elements"></a>Přidat elementy

1. Otevřete soubor *TrustframeworkExtensions.xml* a přidejte následující element **ClaimType** s identifikátorem `oldPassword` do prvku [ClaimsSchema](claimsschema.md) :

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter your old password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Element [ClaimsProvider](claimsproviders.md) obsahuje technický profil, který uživatele ověřuje. Do prvku **ClaimsProviders** přidejte následující zprostředkovatele deklarací identity:

    ```xml
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
              <Item Key="grant_type">password</Item>
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

    Nahraďte `IdentityExperienceFrameworkAppId` ID aplikace IdentityExperienceFramework, kterou jste vytvořili v kurzu požadavků. Nahraďte `ProxyIdentityExperienceFrameworkAppId` ID aplikace aplikace ProxyIdentityExperienceFramework, kterou jste ještě vytvořili.

3. Element [UserJourney](userjourneys.md) definuje cestu, kterou uživatel provede při interakci s aplikací. Přidejte element **userjourney** , pokud neexistuje s **UserJourney** identifikovaným jako `PasswordChange` :

    ```xml
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

4. Uložte soubor zásad *TrustFrameworkExtensions.xml* .
5. Zkopírujte soubor *ProfileEdit.xml* , který jste stáhli pomocí úvodní sady, a pojmenujte ho *ProfileEditPasswordChange.xml*.
6. Otevřete nový soubor a aktualizujte atribut **PolicyId** o jedinečnou hodnotu. Tato hodnota je název vaší zásady. Například *B2C_1A_profile_edit_password_change*.
7. Upravte atribut **ReferenceId** v nástroji `<DefaultUserJourney>` tak, aby odpovídal ID nové cesty uživatele, kterou jste vytvořili. Například *PasswordChange*.
8. Uložte provedené změny.

Ukázkovou zásadu najdete [tady](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change).

## <a name="test-your-policy"></a>Testování zásad

Při testování aplikací v Azure AD B2C může být užitečné, aby byl token Azure AD B2C vrácen, aby bylo možné `https://jwt.ms` zkontrolovat deklarace identity v ní.

### <a name="upload-the-files"></a>Nahrání souborů

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **architekturu prostředí identity**.
5. Na stránce vlastní zásady klikněte na **nahrát zásadu**.
6. Vyberte **přepsat zásadu, pokud existuje**, a pak vyhledejte a vyberte soubor *TrustframeworkExtensions.xml* .
7. Klikněte na **Odeslat**.
8. Opakujte kroky 5 až 7 pro soubor předávající strany, například *ProfileEditPasswordChange.xml*.

### <a name="run-the-policy"></a>Spustit zásadu

1. Otevřete zásadu, kterou jste změnili. Například *B2C_1A_profile_edit_password_change*.
2. V případě **aplikace**vyberte svou aplikaci, kterou jste předtím zaregistrovali. Pro zobrazení tokenu by se měla zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
3. Klikněte na **Spustit**. Přihlaste se pomocí účtu, který jste vytvořili dříve. Nyní byste měli mít možnost změnit heslo.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak můžete [nakonfigurovat složitost hesla pomocí vlastních zásad v Azure Active Directory B2C](custom-policy-password-complexity.md).

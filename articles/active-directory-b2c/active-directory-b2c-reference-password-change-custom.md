---
title: Konfigurace Změna hesla pomocí vlastních zásad v Azure Active Directory B2C | Dokumentace Microsoftu
description: Zjistěte, jak povolit uživatelům změnit si heslo pomocí vlastních zásad v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: aa5be1df1737c56689786f5255203b771b15d179
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54843664"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurace Změna hesla pomocí vlastních zásad v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V Azure Active Directory (Azure AD) B2C můžete povolit uživatele, kteří jsou přihlášení pomocí místního účtu ke změně hesla bez nutnosti jejich pravost ověření e-mailu. Pokud platnosti relace v době, kdy uživatel získá heslo změnit toku, mu výzva k akci. Tento článek ukazuje, jak nakonfigurovat Změna hesla [vlastní zásady](active-directory-b2c-overview-custom.md). Je také možné nakonfigurovat [samoobslužné resetování hesla](active-directory-b2c-reference-sspr.md) pro toky uživatelů.

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v [začít pracovat s vlastními zásadami v Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="add-the-elements"></a>Přidat prvky 

1. Otevřete váš *TrustframeworkExtensions.xml* soubor a přidejte následující **typu deklarace identity** element s identifikátorem `oldPassword` k [ClaimsSchema](claimsschema.md) element: 

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

2. A [ClaimsProvider](claimsproviders.md) prvek obsahuje technický profil, který ověřuje uživatele. Přidejte následující deklarace poskytovatelům **ClaimsProviders** element:

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

    Nahraďte `IdentityExperienceFrameworkAppId` s ID aplikace, kterou jste vytvořili v požadovaném kurzu IdentityExperienceFramework aplikace. Nahraďte `ProxyIdentityExperienceFrameworkAppId` s ID aplikace ProxyIdentityExperienceFramework aplikace, kterou jste předtím vytvořili.

3. [UserJourney](userjourneys.md) element definuje cestu, která uživatel provede při interakci s vaší aplikací. Přidat **Userjourney** elementu, jestliže neexistuje s **UserJourney** identifikována jako `PasswordChange`:

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

4. Uložit *TrustFrameworkExtensions.xml* soubor zásad.
5. Kopírovat *ProfileEdit.xml* soubor stáhne společně s starter pack a pojmenujte ho *ProfileEditPasswordChange.xml*.
6. Otevřete nový soubor a aktualizace **PolicyId** atribut s jedinečnou hodnotu. Tato hodnota je název zásady. Například *B2C_1A_profile_edit_password_change*.
7. Upravit **ReferenceId** atribut `<DefaultUserJourney>` tak, aby odpovídaly ID nové cesty uživatele, který jste vytvořili. Například *PasswordChange*.
8. Uložte provedené změny.

Můžete najít ukázkové zásady [tady](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change). 

## <a name="test-your-policy"></a>Vaše zásada testování

Při testování aplikací v Azure AD B2C, může být užitečné mít tokenu Azure AD B2C vrátí `https://jwt.ms` být schopni zkontrolovat deklarace identity v ní.

### <a name="upload-the-files"></a>Nahrání souborů

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **architekturu rozhraní identit**.
5. Na stránce vlastní zásady, klikněte na tlačítko **nahrát zásady**.
6. Vyberte **přepsat zásady, pokud existuje**a poté vyhledejte a vyberte *TrustframeworkExtensions.xml* souboru.
7. Klikněte na **Odeslat**.
8. Opakujte kroky 5 až 7 pro souboru předávající strany, jako například *ProfileEditPasswordChange.xml*.

### <a name="run-the-policy"></a>Spustit zásady

1. Otevřete zásadu, kterou jste změnili. Například *B2C_1A_profile_edit_password_change*.
2. Pro **aplikace**, vyberte aplikace, které jste dříve zaregistrovali. Chcete-li zobrazit token, **adresy URL odpovědi** by se zobrazit `https://jwt.ms`.
3. Klikněte na **Spustit**. Přihlaste se pomocí acouunt, který jste dříve vytvořili. Teď byste měli mít možnost ke změně hesla. 

## <a name="next-steps"></a>Další postup

- Další informace o tom, jak [nakonfigurujte složitost hesla pomocí vlastních zásad v Azure Active Directory B2C](active-directory-b2c-reference-password-complexity-custom.md). 
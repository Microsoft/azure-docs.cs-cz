---
title: 'Azure Active Directory B2C: Změna hesla pomocí samoobslužné služby | Microsoft Docs'
description: Téma, který ukazuje, jak nastavit změnu hesla pomocí samoobslužné služby pro uživatele v Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 09/05/2016
ms.author: davidmu
ms.openlocfilehash: 4c54abe8c3375b49b786c7849c7e963e5849fa3a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-configure-password-change-in-custom-policies"></a>Azure Active Directory B2C: Konfigurace Změna hesla v vlastní zásady  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Funkce změnu hesla s přihlášeného příjemci (pomocí místní účty) můžete změnit jejich hesla bez nutnosti potvrzení jejich pravosti pomocí ověření e-mailu, jak je popsáno v [toku resetování hesla pomocí samoobslužné služby.](active-directory-b2c-reference-sspr.md) Pokud relace vyprší v čase příjemce získá heslo změnit toku, uživatel je vyzván, aby znovu přihlásit. 

## <a name="prerequisites"></a>Požadavky

Klient služby Azure AD B2C, nakonfigurované k dokončení registrace-množství nebo přihlášení, místní účet, jak je popsáno v [Začínáme](active-directory-b2c-get-started-custom.md).

## <a name="how-to-configure-password-change-in-custom-policy"></a>Postup konfigurace ve vlastních zásadách pro změnu hesla

Změna hesla ve vlastních zásadách pro konfigurace proveďte následující změny v zásadách rozšíření framework vztahu důvěryhodnosti 

## <a name="define-a-claimtype-oldpassword"></a>Definování typ ClaimType 'Původní_heslo.

Celková struktura vaše vlastní zásada musí obsahovat `ClaimsSchema`a definovat novou `ClaimType` 'Původní_heslo, jak je uvedeno níže, 

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

Účelem těchto prvků vypadá takto:

- `ClaimsSchema` Definuje, které deklarace identity je ověřován.  V takovém případě bude ověřeno staré heslo. 

## <a name="add-a-password-change-claims-provider-with-its-supporting-elements"></a>Přidání poskytovatele deklarací identity změnu hesla s jeho podpůrné elementy

Deklarace identity, které bude zprostředkovatel pro změnu hesla

1. Ověření uživatele vůči staré heslo
2. A pokud 'nové heslo, odpovídá "potvrzení nového hesla", tato hodnota je uložena v úložišti dat B2C, a proto je heslo úspěšně změněno. 

![obrázek](images/passwordchange.jpg)

Přidejte následující zprostředkovatele deklarací identity do vaší zásady rozšíření. 

```XML
<ClaimsProviders>
    <ClaimsProvider>
      <DisplayName>Local Account SignIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="login-NonInteractive">
          <Metadata>
           <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
           <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
          </InputClaims>
        </TechnicalProfile>
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



### <a name="add-the-application-ids-to-your-custom-policy"></a>Přidat ID aplikace do vlastních zásad

Přidat ID aplikace souboru rozšíření (`TrustFrameworkExtensions.xml`):

1. V souboru rozšíření (TrustFrameworkExtensions.xml) nalezen element `<TechnicalProfile Id="login-NonInteractive">` a `<TechnicalProfile Id="login-NonInteractive-PasswordChange">`

2. Nahraďte všechny výskyty `IdentityExperienceFrameworkAppId` s ID aplikace Framework prostředí Identity aplikace, jak je popsáno v [Začínáme](active-directory-b2c-get-started-custom.md). Zde naleznete příklad:

   ```
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```

3. Nahraďte všechny výskyty `ProxyIdentityExperienceFrameworkAppId` s ID aplikace Framework prostředí Identity Proxy aplikace, jak je popsáno v [Začínáme](active-directory-b2c-get-started-custom.md).

4. Uložte soubor rozšíření.



## <a name="create-a-password-change-user-journey"></a>Vytvoření cesty uživatel změnit heslo

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

Dokončení úpravy souboru rozšíření. Uložte a nahrajte tento soubor. Ujistěte se, že všechny ověření úspěšné.



## <a name="create-a-relying-party-rp-file"></a>Vytvořte soubor předávající stranu

Potom aktualizujte soubor předávající stranu, který iniciuje cesty uživatele, který jste vytvořili:

1. Vytvořte kopii ProfileEdit.xml v pracovní adresář. Potom přejmenujte ji (například PasswordChange.xml).
2. Otevřete nový soubor a aktualizace `PolicyId` atribut pro `<TrustFrameworkPolicy>` s jedinečnou hodnotu. Toto je název vaší zásady (například PasswordChange).
3. Změnit `ReferenceId` atribut `<DefaultUserJourney>` tak, aby odpovídala `Id` nové cesty uživatele, který jste vytvořili (například PasswordChange).
4. Uložte změny a potom soubor odešlete.
5. Pokud chcete otestovat vlastní zásady, který jste nahráli, na portálu Azure, přejděte do okna zásady a pak klikněte na tlačítko **spustit nyní**.




## <a name="link-to-password-change-sample-policy"></a>Odkaz na heslo změnit ukázkové zásady

Můžete najít ukázkové zásady [zde](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change). 











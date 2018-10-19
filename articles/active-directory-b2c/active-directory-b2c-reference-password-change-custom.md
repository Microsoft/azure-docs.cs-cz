---
title: Změna hesla pomocí samoobslužné služby v Azure Active Directory B2C | Dokumentace Microsoftu
description: Téma ukazuje, jak nastavit Změna hesla pomocí samoobslužné služby pro uživatele v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: fdbf2d9bebb26a36e3f83e1149c4f97921aeaa1d
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49407023"
---
# <a name="azure-active-directory-b2c-configure-password-change-in-custom-policies"></a>Azure Active Directory B2C: Konfigurace Změna hesla ve vlastní zásady  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Díky funkci změnu hesla přihlášeného uživatele (pomocí místních účtů) mohou změnit svá hesla bez nutnosti potvrzení jejich pravosti pomocí ověření e-mailu, jak je popsáno v [resetování hesla pomocí samoobslužné služby flow.](active-directory-b2c-reference-sspr.md) Pokud vyprší platnost relace době získá uživatel heslo změnit toku, uživatel je vyzván k znovu se přihlaste. 

## <a name="prerequisites"></a>Požadavky

Klient služby Azure AD B2C nakonfigurovaný tak, aby dokončit místní účet přihlášení-registrace/přihlášení, jak je popsáno v [Začínáme](active-directory-b2c-get-started-custom.md).

## <a name="how-to-configure-password-change-in-custom-policy"></a>Jak nakonfigurovat ve vlastních zásadách pro změnu hesla

Změna hesla ve vlastních zásadách konfigurace proveďte následující změny v zásadách zabezpečení framework rozšíření 

## <a name="define-a-claimtype-oldpassword"></a>Definování typu deklarace identity "oldPassword.

Celková struktura vaše vlastní zásada musí obsahovat `ClaimsSchema`a definovat novou `ClaimType` "oldPassword" jak je uvedeno níže, 

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

Účelem těchto prvků je následujícím způsobem:

- `ClaimsSchema` Definuje, jaké deklarace identity se ověřuje.  V takovém případě bude ověřeno staré heslo. 

## <a name="add-a-password-change-claims-provider-with-its-supporting-elements"></a>Přidat zprostředkovatele deklarací identity změnu hesla s její podpůrné prvky

Změna deklarace identit, které bude zprostředkovatel hesla

1. Ověřit uživatele vůči staré heslo
2. A pokud "nové heslo" odpovídá "Potvrdit nové heslo", tato hodnota bude uložena v úložišti dat B2C, a proto je heslo úspěšně změněno. 

![obrázek](images/passwordchange.jpg)

Přidejte následující zprostředkovatele deklarací identity k rozšíření zásady. 

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



### <a name="add-the-application-ids-to-your-custom-policy"></a>ID aplikace přidat do vlastní zásady

Přidejte do souboru rozšíření ID aplikace (`TrustFrameworkExtensions.xml`):

1. V souboru rozšíření (TrustFrameworkExtensions.xml) najděte prvek `<TechnicalProfile Id="login-NonInteractive">` a `<TechnicalProfile Id="login-NonInteractive-PasswordChange">`

2. Nahraďte všechny výskyty `IdentityExperienceFrameworkAppId` s ID aplikace, architekturu rozhraní identit aplikace, jak je popsáno v [Začínáme](active-directory-b2c-get-started-custom.md). Zde naleznete příklad:

   ```
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```

3. Nahraďte všechny výskyty `ProxyIdentityExperienceFrameworkAppId` s ID aplikace, architekturu rozhraní identit Proxy aplikace, jak je popsáno v [Začínáme](active-directory-b2c-get-started-custom.md).

4. Uložte soubor rozšíření.



## <a name="create-a-password-change-user-journey"></a>Vytvoření cesty uživatele změnu hesla

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

Dokončení úpravy souboru rozšíření. Uložte a odešlete tento soubor. Ujistěte se, že všechny ověření úspěšné.



## <a name="create-a-relying-party-rp-file"></a>Vytvořit soubor předávající stranu

V dalším kroku aktualizujte předávající stranu soubor, který iniciuje cesty uživatele, který jste vytvořili:

1. Vytvořte kopii ProfileEdit.xml ve svém pracovním adresáři. Přejmenujte (například PasswordChange.xml).
2. Otevřete nový soubor a aktualizace `PolicyId` atributu `<TrustFrameworkPolicy>` s jedinečnou hodnotu. Toto je název zásady (například PasswordChange).
3. Upravit `ReferenceId` atribut `<DefaultUserJourney>` tak, aby odpovídaly `Id` nové cesty uživatele, který jste vytvořili (například PasswordChange).
4. Uložte změny a pak nahrajte soubor.
5. Vlastní zásady, které jste nahráli, otestovat na webu Azure Portal, přejděte na okno zásad a pak klikněte na tlačítko **spustit nyní**.




## <a name="link-to-password-change-sample-policy"></a>Odkaz na zásady ukázka změnit heslo

Můžete najít ukázkové zásady [tady](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change). 











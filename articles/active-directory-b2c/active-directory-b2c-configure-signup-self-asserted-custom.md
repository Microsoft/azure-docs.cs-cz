---
title: Upravit přihlašovací nahoru v vlastní zásady a nakonfigurovat vlastní prohlašovanou zprostředkovatele | Microsoft Docs
description: Návod k přidání deklarací zaregistrovat a nakonfigurovat uživatelský vstup
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/29/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9d8f644e819ceb83f0b436789d6d8610ed01f6a6
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "34710793"
---
# <a name="azure-active-directory-b2c-modify-sign-up-to-add-new-claims-and-configure-user-input"></a>Azure Active Directory B2C: Upravte přihlašovací nahoru k přidání nových deklarací identity a konfiguraci vstup uživatele.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku přidáte nový záznam uživatel zadal (deklarace identity) vám dobře slouží registrace uživatele.  Bude nakonfigurovat položku jako rozevírací seznam a definovat v případě potřeby.

## <a name="prerequisites"></a>Požadavky

* Proveďte kroky v následujícím článku [Začínáme se zásadami vlastní](active-directory-b2c-get-started-custom.md).  Otestujte cesty registrace nebo přihlášení uživatele k registraci nového místní účet, než budete pokračovat.


Shromažďování dat prvotní od uživatelů se dosahuje prostřednictvím registrace nebo přihlášení.  Další deklarace identity se dají shromáždit později prostřednictvím profilu upravit uživatele cesty. Kdykoliv Azure AD B2C shromáždí informace o interaktivním přímo od uživatele, používá rozhraní prostředí Identity jeho `selfasserted provider`. Následující postup použít, kdykoliv se používá tohoto zprostředkovatele.


## <a name="define-the-claim-its-display-name-and-the-user-input-type"></a>Zadejte deklarace identity, jeho zobrazovaný název a typ vstupu uživatele
Umožňuje požádat uživatele pro jejich město.  Přidejte následující elementu, který chcete `<ClaimsSchema>` element v souboru TrustFrameworkBase zásad:

```xml
<ClaimType Id="city">
  <DisplayName>city</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```
Existují další možnosti, které můžete provést zde přizpůsobit deklarace identity.  Úplné schéma, najdete v části **Identity rozhraní Framework technické referenční příručka**.  Tato příručka brzy zveřejníme v části odkaz.

* `<DisplayName>` je řetězec, který definuje uživatelsky orientovaný *popisek*

* `<UserHelpText>` pomáhá uživateli pochopit, co je požadováno

* `<UserInputType>` obsahuje následující čtyři možnosti zvýrazněná níže:
    * `TextBox`
```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

    * `RadioSingleSelectduration` -Vynucuje jednoho výběru.
```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>RadioSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

    * `DropdownSingleSelect` -Umožňuje výběr pouze platnou hodnotu.

![Snímek obrazovky možností rozevíracího seznamu](./media/active-directory-b2c-configure-signup-self-asserted-custom/dropdown-menu-example.png)


```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```


* `CheckboxMultiSelect` Umožňuje výběr jednoho nebo více hodnot.

![Snímek obrazovky možnost vícenásobného výběru.](./media/active-directory-b2c-configure-signup-self-asserted-custom/multiselect-menu-example.png)


```xml
<ClaimType Id="city">
  <DisplayName>Receive updates from which cities?</DisplayName>
  <DataType>string</DataType>
  <UserInputType>CheckboxMultiSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

## <a name="add-the-claim-to-the-sign-upsign-in-user-journey"></a>Přidá deklaraci k přihlašovací nahoru nebo přihlášení uživatele cesty

1. Přidání deklarace identity jako `<OutputClaim ClaimTypeReferenceId="city"/>` k TechnicalProfile `LocalAccountSignUpWithLogonEmail` (nalezené v souboru TrustFrameworkBase zásad).  Všimněte si, že tento TechnicalProfile používá SelfAssertedAttributeProvider.

  ```xml
  <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
    <DisplayName>Email signup</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
      <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
      <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
      <Item Key="language.button_continue">Create</Item>
    </Metadata>
    <CryptographicKeys>
      <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
    </CryptographicKeys>
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" />
      <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
      <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
      <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
      <OutputClaim ClaimTypeReferenceId="authenticationSource" />
      <OutputClaim ClaimTypeReferenceId="newUser" />
      <!-- Optional claims, to be collected from the user -->
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surName" />
      <OutputClaim ClaimTypeReferenceId="city"/>
    </OutputClaims>
    <ValidationTechnicalProfiles>
      <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
    </ValidationTechnicalProfiles>
    <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
  </TechnicalProfile>
  ```

2. Přidá deklaraci do AAD-UserWriteUsingLogonEmail jako `<PersistedClaim ClaimTypeReferenceId="city" />` zápis deklarace identity k adresáři AAD po shromáždění od uživatele. Pokud nechcete zachovat deklarace identity v adresáři pro budoucí použití, může tento krok přeskočte.

  ```xml
  <!-- Technical profiles for local accounts -->
  <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
    <Metadata>
      <Item Key="Operation">Write</Item>
      <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    </Metadata>
    <IncludeInSso>false</IncludeInSso>
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" Required="true" />
    </InputClaims>
    <PersistedClaims>
      <!-- Required claims -->
      <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
      <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password" />
      <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
      <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />
      <!-- Optional claims. -->
      <PersistedClaim ClaimTypeReferenceId="givenName" />
      <PersistedClaim ClaimTypeReferenceId="surname" />
      <PersistedClaim ClaimTypeReferenceId="city" />
    </PersistedClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" />
      <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
      <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
      <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
      <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    </OutputClaims>
    <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
  </TechnicalProfile>
  ```

3. Přidá deklaraci k TechnicalProfile, který čte z adresáře, když se uživatel přihlásí jako `<OutputClaim ClaimTypeReferenceId="city" />`

  ```xml
  <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
    <Metadata>
      <Item Key="Operation">Read</Item>
      <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">An account could not be found for the provided user ID.</Item>
    </Metadata>
    <IncludeInSso>false</IncludeInSso>
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames" Required="true" />
    </InputClaims>
    <OutputClaims>
      <!-- Required claims -->
      <OutputClaim ClaimTypeReferenceId="objectId" />
      <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
      <!-- Optional claims -->
      <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="otherMails" />
      <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
      <OutputClaim ClaimTypeReferenceId="city" />
    </OutputClaims>
    <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  </TechnicalProfile>
  ```

4. Přidat `<OutputClaim ClaimTypeReferenceId="city" />` zásad RP souboru SignUporSignIn.xml tak tuto deklaraci posílá po úspěšné uživatele cestu aplikace v tokenu.

  ```xml
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
  ```

## <a name="test-the-custom-policy-using-run-now"></a>Otestovat vlastní zásady pomocí "Spustit nyní"

1. Otevřete **okno Azure AD B2C** a přejděte do **Identity rozhraní Framework > vlastní zásady**.
2. Vyberte vlastní zásady, který jste nahráli a klikněte na **spustit nyní** tlačítko.
3. Nyní byste měli mít zaregistrovat pomocí e-mailovou adresu.

Na obrazovce registrace v testovacím režimu by měl vypadat podobně jako tento:

![Snímek obrazovky upravené možnost zápisu](./media/active-directory-b2c-configure-signup-self-asserted-custom/signup-with-city-claim-dropdown-example.png)

  Token zpět do aplikace bude teď obsahovat `city` deklarace identity, jak je uvedeno níže
```json
{
  "exp": 1493596822,
  "nbf": 1493593222,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "9c2a3a9e-ac65-4e46-a12d-9557b63033a9",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustf_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1493593222,
  "auth_time": 1493593222,
  "email": "joe@outlook.com",
  "given_name": "Joe",
  "family_name": "Ras",
  "city": "Bellevue",
  "name": "unknown"
}
```

## <a name="optional-remove-email-verification-from-signup-journey"></a>Volitelné: Ověření e-mailu odeberte z cesty registrace

Pokud chcete přeskočit ověření e-mailu, Autor zásady můžete odebrat `PartnerClaimType="Verified.Email"`. E-mailová adresa bude potřeba ale není ověřen, pokud "Požadavky" = true se odebere.  Pečlivě zvažte, pokud tato možnost není pravé pro vaše případy použití!

Ověřit e-mailu je povolena ve výchozím nastavení `<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">` v souboru TrustFrameworkBase zásad sadě starter:
```xml
<OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
```

## <a name="next-steps"></a>Další postup

Přidejte novou deklaraci na toky pro sociálních účet přihlášení změnou TechnicalProfiles uvedené níže. Ty se používají sociálního/federovaný účet přihlášení k zápisu a čtení dat uživatele pomocí alternativeSecurityId jako Lokátor.
```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```

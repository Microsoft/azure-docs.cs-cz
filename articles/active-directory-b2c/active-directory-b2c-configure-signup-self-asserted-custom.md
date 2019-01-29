---
title: Upravit přihlášení do vlastní zásady a nakonfigurovat vlastní s prohlašovanou poskytovatele | Dokumentace Microsoftu
description: Postup přidání deklarace identity se zaregistrovat a konfigurace uživatelského vstupu
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 2989af12407bdddf6e55e8967a0a574fff690208
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55179204"
---
# <a name="azure-active-directory-b2c-modify-sign-up-to-add-new-claims-and-configure-user-input"></a>Azure Active Directory B2C: Upravte přihlašování k přidání nových deklarací identity a konfigurace uživatelského vstupu.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku se přidá nový záznam zadaného uživatelem (deklarace identity) na vaší cestě registrace uživatele.  Bude nakonfigurujte položka rozevíracího seznamu a definovat, jestli je povinné.

## <a name="prerequisites"></a>Požadavky

* Proveďte kroky v následujícím článku [Začínáme se zásadami vlastní](active-directory-b2c-get-started-custom.md).  Otestujte cestu registrace/přihlášení uživatele k registraci nový místní účet, než budete pokračovat.


Shromažďování počáteční data od uživatelů se dosahuje prostřednictvím registrace/přihlášení.  Další deklarace identity se dají shromáždit později pomocí cesty uživatele úpravy profilu. Když Azure AD B2C interaktivně shromažďuje informace přímo od uživatele, používá architekturu rozhraní identit jeho `selfasserted provider`. Následující postup použít, kdykoli se používá tento zprostředkovatel.


## <a name="define-the-claim-its-display-name-and-the-user-input-type"></a>Zadejte deklaraci identity, její zobrazovaný název a typ uživatelského vstupu
Umožňuje požádat uživatele o jejich město.  Přidejte následující prvek k `<ClaimsSchema>` element v souboru TrustFrameworkBase zásad:

```xml
<ClaimType Id="city">
  <DisplayName>city</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```
Existují další možnosti, můžete tady provedené přizpůsobení deklarace identity.  Úplného schématu, najdete **Identity prostředí Framework technické referenční příručka**.  Tento průvodce bude brzy zveřejníme v referenční části.

* `<DisplayName>` řetězec, který definuje uživatele přístupných *popisek*

* `<UserHelpText>` pomáhá uživatelům pochopit, co je potřeba

* `<UserInputType>` obsahuje následující čtyři možnosti jejichž přehled najdete níže:
    * `TextBox`
```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

    * `RadioSingleSelectduration` -Vynucuje jeden výběr.
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

    * `DropdownSingleSelect` – Umožňuje vybrat pouze platná hodnota.

![Snímek obrazovky s rozevírací seznam možností](./media/active-directory-b2c-configure-signup-self-asserted-custom/dropdown-menu-example.png)


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


* `CheckboxMultiSelect` Umožňuje vybrat jednu nebo více hodnot.

![Snímek obrazovky s možností vícenásobného výběru](./media/active-directory-b2c-configure-signup-self-asserted-custom/multiselect-menu-example.png)


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

## <a name="add-the-claim-to-the-sign-upsign-in-user-journey"></a>Přidá deklaraci k znaménko up/sign v cestě uživatele

1. Přidání deklarace identity jako `<OutputClaim ClaimTypeReferenceId="city"/>` na technický profil `LocalAccountSignUpWithLogonEmail` (tuto možnost najdete v souboru zásad TrustFrameworkBase).  Všimněte si, že tento technický profil používá SelfAssertedAttributeProvider.

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

2. Přidá deklaraci k UserWriteUsingLogonEmail AAD jako `<PersistedClaim ClaimTypeReferenceId="city" />` zápis deklarace identity do služby AAD po shromáždění od uživatele. Pokud nechcete zachovat pro budoucí použití deklarací identity v adresáři, může tento krok přeskočit.

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

3. Přidá deklaraci k technický profil, který čte z adresáře, když se uživatel přihlásí na jako `<OutputClaim ClaimTypeReferenceId="city" />`

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

4. Přidat `<OutputClaim ClaimTypeReferenceId="city" />` RP zásad souborů SignUporSignIn.xml, takže tato deklarace identity se odešlou do aplikace v tokenu po cesty uživatele úspěšné.

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

## <a name="test-the-custom-policy-using-run-now"></a>Vlastní zásady "Spustit nyní" pomocí testování

1. Otevřít **okno Azure AD B2C** a přejděte do **architekturu rozhraní identit > vlastní zásady**.
2. Vyberte vlastní zásady, které jste nahráli a klikněte na tlačítko **spustit nyní** tlačítko.
3. Měli byste být schopni registrace pomocí e-mailovou adresu.

Obrazovka registrace v režimu testu by měla vypadat nějak takto:

![Snímek obrazovky s upravenou možnost registrace](./media/active-directory-b2c-configure-signup-self-asserted-custom/signup-with-city-claim-dropdown-example.png)

  Teď bude zahrnovat token zpět do aplikace `city` deklarace identity, jak je znázorněno níže
```json
{
  "exp": 1493596822,
  "nbf": 1493593222,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
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

## <a name="optional-remove-email-verification-from-signup-journey"></a>Volitelné: Odebrání cesty registrace ověření e-mailu

Chcete-li přeskočit ověření e-mailu, Autor zásad můžete odebrat `PartnerClaimType="Verified.Email"`. E-mailová adresa se vyžaduje, ale nebyla ověřena, není-li "Required" = true se odebere.  Pečlivě zvažte, jestli tato možnost je vhodná pro vaše případy použití!

Ověřit e-mailu je povolena ve výchozím nastavení `<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">` TrustFrameworkBase souboru zásad v starter pack:
```xml
<OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
```

## <a name="next-steps"></a>Další postup

Pokud vaše zásady podporuje účty sociálních sítí, přidejte novou deklaraci do toků pro přihlášení účtu na sociální síti tak, že změníte technické profilů uvedených níže. Tyto deklarace používají přihlašovací údaje účtu na sociální síti shromažďuje a zapisuje data od uživatele.

1. Vyhledejte technický profil **SelfAsserted sociálního** a přidá deklaraci výstupu. Pořadí deklarace identity v **OutputClaims** určuje pořadí, že Azure AD B2C vykreslí deklarace identity na obrazovce. Například, `<OutputClaim ClaimTypeReferenceId="city" />`.
2. Vyhledejte technický profil **AAD UserWriteUsingAlternativeSecurityId** a přidá deklaraci persist. Například, `<PersistedClaim ClaimTypeReferenceId="city" />`.
3. Vyhledejte technický profil **AAD UserReadUsingAlternativeSecurityId** a přidá deklaraci výstupu. Například, `<OutputClaim ClaimTypeReferenceId="city" />`.

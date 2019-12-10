---
title: Definování technického profilu s vlastním uplatněním v vlastní zásadě
titleSuffix: Azure AD B2C
description: V Azure Active Directory B2C ve vlastních zásadách definujte technický profil s vlastním uplatněním.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bfa8982fb49b31540d1926bdeb75a96dc1d79cf0
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950897"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu s vlastním uplatněním v Azure Active Directory B2C vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Všechny interakce v Azure Active Directory B2C (Azure AD B2C), kde se očekává, že uživatel zadává vstup, jsou technické profily s vlastním uplatněním. Například stránku pro registraci, přihlašovací stránku nebo stránku pro resetování hesla.

## <a name="protocol"></a>Protocol (Protokol)

Atribut **Name** elementu **Protocol** musí být nastaven na `Proprietary`. Atribut **obslužné rutiny** musí obsahovat plně kvalifikovaný název sestavení obslužné rutiny protokolu, které je používáno Azure AD B2C, pro použití s vlastním kontrolním výrazem: `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

Následující příklad ukazuje technický profil s vlastním uplatněním pro registraci e-mailu:

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Vstupní deklarace identity

V technickém profilu s vlastním výkonem můžete pomocí prvků **InputClaims** a **InputClaimsTransformations** předem vyplnit hodnotu deklarací identity, které se zobrazí na stránce s vlastním uplatněním (výstupní deklarace). Například v zásadě upravit zásadu profilace uživatele nejprve načte profil uživatele z adresářové služby Azure AD B2C, potom technický profil s vlastním uplatněním nastaví vstupní deklarace identity s uživatelskými daty uloženými v profilu uživatele. Tyto deklarace se shromažďují z profilu uživatele a pak se zobrazí uživateli, který pak může upravovat stávající data.

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```


## <a name="output-claims"></a>Deklarace výstupů

Element **OutputClaims** obsahuje seznam deklarací identity, které mají být prezentovány pro shromažďování dat od uživatele. Pokud chcete předem naplnit výstupní deklarace s některými hodnotami, použijte vstupní deklarace, které byly dříve popsané. Element může obsahovat také výchozí hodnotu. Pořadí deklarací v **OutputClaims** řídí pořadí, které Azure AD B2C vykreslí deklarace na obrazovce. Atribut **DefaultValue** se uplatní pouze v případě, že deklarace identity nebyla nikdy nastavena před. Ale pokud byl nastaven před předchozí krok orchestrace, i když uživatel opustí hodnotu prázdnou, výchozí hodnota se neprojeví. Chcete-li vynutit použití výchozí hodnoty, nastavte atribut **AlwaysUseDefaultValue** na hodnotu `true`. Pokud chcete vynutit, aby uživatel poskytoval hodnotu pro konkrétní výstupní deklaraci identity, nastavte **požadovaný** atribut prvku **OutputClaims** na `true`.

Element **ClaimType** v kolekci **OutputClaims** musí nastavit element **UserInputType** na libovolný typ vstupu uživatele, který podporuje Azure AD B2C, jako je například `TextBox` nebo `DropdownSingleSelect`. Nebo element **OutputClaim** musí mít nastavenou hodnotu **DefaultValue**.

Element **OutputClaimsTransformations** může obsahovat kolekci prvků **OutputClaimsTransformation** , které se používají k úpravě výstupních deklarací identity nebo k vygenerování nových.

Následující výstupní deklarace identity je vždycky nastavená na `live.com`:

```XML
<OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" AlwaysUseDefaultValue="true" />
```

### <a name="use-case"></a>Případ použití

K dispozici jsou čtyři scénáře pro výstupní deklarace identity:

- **Shromažďování deklarací výstupů od uživatele** – Pokud potřebujete shromažďovat informace od uživatele, například datum narození, měli byste přidat deklaraci do kolekce **OutputClaims** . Deklarace identity, které jsou prezentovány uživateli, musí určovat **UserInputType**, například `TextBox` nebo `DropdownSingleSelect`. Pokud technický profil s vlastním uplatněním obsahuje profil ověření, který má stejnou deklaraci identity, Azure AD B2C neprezentuje tuto deklaraci uživateli. Pokud není k dispozici žádná výstupní deklarace identity pro uživatele, Azure AD B2C přeskočí technický profil.
- **Nastavení výchozí hodnoty ve výstupní deklaraci** – bez shromažďování dat od uživatele nebo vrácení dat z technického profilu ověření. `LocalAccountSignUpWithLogonEmail` technický profil s vlastním uplatněním nastavuje deklaraci **SelfAsserted-Input** na `true`.
- **Technický profil ověření vrací deklarace identity** – váš technický profil může zavolat technický profil ověření, který vrací některé deklarace identity. Je možné, že budete chtít deklarace identity a vrátit je k dalším krokům orchestrace v cestě uživatele. Například když se přihlašujete pomocí místního účtu, technický profil s názvem `SelfAsserted-LocalAccountSignin-Email` volá technický profil ověřování s názvem `login-NonInteractive`. Tento technický profil ověří přihlašovací údaje uživatele a také vrátí profil uživatele. Například "userPrincipalName", "DisplayName", "dodaný" a "příjmení".
- **Výstup deklarací přes transformaci výstupních deklarací**

V následujícím příkladu předvádí `LocalAccountSignUpWithLogonEmail` technický profil s vlastním uplatněním, který ukazuje použití výstupních deklarací identity a nastavuje **rutinu SelfAsserted-Input** na `true`. `objectId`, `authenticationSource`, `newUser` deklarace identity jsou výstupem `AAD-UserWriteUsingLogonEmail` technického profilu ověření a nezobrazují se uživateli.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
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
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surName" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>

```

## <a name="persist-claims"></a>Zachovat deklarace identity

Pokud chybí element **PersistedClaims** , technický profil s vlastním uplatněním neuchovává data Azure AD B2C. Místo toho je provedeno volání na technický profil ověření, který je zodpovědný za uchování dat. Například zásada registrace používá ke shromáždění nového profilu uživatele `LocalAccountSignUpWithLogonEmail` technický profil s vlastním uplatněním. Technický profil `LocalAccountSignUpWithLogonEmail` volá technický profil ověření pro vytvoření účtu v Azure AD B2C.

## <a name="validation-technical-profiles"></a>Technické profily ověřování

Pro ověření některých nebo všech výstupních deklarací odkazujícího technického profilu se používá ověřovací technický profil. Vstupní deklarace technického profilu ověření se musí objevit ve výstupních deklaracích technického profilu s vlastním uplatněním. Technický profil ověření ověřuje vstup uživatele a může uživateli vrátit chybu.

Technický profil ověření může být jakýkoliv technický profil v zásadách, například [Azure Active Directory](active-directory-technical-profile.md) nebo [REST API](restful-technical-profile.md) technickými profily. V předchozím příkladu `LocalAccountSignUpWithLogonEmail` technický profil ověří, že signinName v adresáři neexistuje. Pokud ne, technický profil ověření vytvoří místní účet a vrátí objectId, authenticationSource, Nový_uživatel. Technický profil `SelfAsserted-LocalAccountSignin-Email` volá technický profil ověření `login-NonInteractive` a ověří přihlašovací údaje uživatele.

Pomocí obchodní logiky můžete také volat REST API technický profil, přepsat vstupní deklarace identity nebo rozšířit uživatelská data další integrací s podnikovou obchodní aplikací. Další informace najdete v tématu [technický profil ověření](validation-technical-profile.md) .

## <a name="metadata"></a>Metadata

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| setting.showContinueButton | Ne | Zobrazí tlačítko pokračovat. Možné hodnoty: `true` (výchozí) nebo `false` |
| setting.showCancelButton | Ne | Zobrazí tlačítko Storno. Možné hodnoty: `true` (výchozí) nebo `false` |
| nastavení. operatingMode | Ne | Pro přihlašovací stránku Tato vlastnost řídí chování pole username, jako je například ověřování vstupu a chybové zprávy. Očekávané hodnoty: `Username` nebo `Email`. |
| ContentDefinitionReferenceId | Ano | Identifikátor [definice obsahu](contentdefinitions.md) přidruženého k tomuto technickému profilu. |
| EnforceEmailVerification | Ne | Pro registraci nebo úpravy profilu vynutilo ověřování e-mailů. Možné hodnoty: `true` (výchozí) nebo `false`. |
| nastavení. showSignupLink | Ne | Zobrazí tlačítko pro registraci. Možné hodnoty: `true` (výchozí) nebo `false` |
| nastavení. retryLimit | Ne | Určuje počet pokusů, kolikrát se uživatel může pokusit zadat data, která jsou zkontrolována na technický profil ověření. Uživatel se například pokusí zaregistrovat pomocí účtu, který už existuje, a pokračuje v tom, dokud nedosáhne limitu.
| SignUpTarget | Ne | Identifikátor cílového Exchange registrace. Když uživatel klikne na tlačítko pro registraci, Azure AD B2C spustí zadaný identifikátor Exchange. |

## <a name="cryptographic-keys"></a>Kryptografické klíče

Element **CryptographicKeys** se nepoužívá.














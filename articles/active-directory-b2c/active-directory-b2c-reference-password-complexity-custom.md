---
title: Složitost hesla ve vlastních zásad v Azure Active Directory B2C | Dokumentace Microsoftu
description: Jak konfigurovat požadavky na složitost hesel ve vlastních zásadách.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/16/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c6b8312a08d1d92bccf70e7d3dda5f01811b4f87
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52848523"
---
# <a name="configure-password-complexity-in-custom-policies"></a>Nakonfigurujte složitost hesla ve vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek je pokročilá popis toho, jak funguje složitost hesla a se aktivuje pomocí vlastních zásad Azure AD B2C.

## <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Azure AD B2C: Konfigurovat požadavky na složitost hesel

Azure Active Directory B2C (Azure AD B2C) podporuje se měnící požadavky na složitost hesel poskytnutých koncového uživatele při vytváření účtu.  Ve výchozím nastavení, Azure AD B2C používá **silné** hesla.  Azure AD B2C podporuje také možnosti konfigurace pro řízení složitosti hesla, které můžou zákazníci využívat.  Tento článek hovoří o tom, jak nakonfigurovat složitost hesla ve vlastní zásady.  Je také možné použít [nakonfigurujte složitost hesla v předdefinované zásady](active-directory-b2c-reference-password-complexity.md).

## <a name="prerequisites"></a>Požadavky

Klient služby Azure AD B2C nakonfigurovaný tak, aby dokončit místní účet přihlášení-registrace/přihlášení, jak je popsáno v [Začínáme](active-directory-b2c-get-started-custom.md).

## <a name="how-to-configure-password-complexity-in-custom-policy"></a>Jak nakonfigurovat ve vlastních zásadách pro složitost hesla

Ke konfiguraci složitosti hesla ve vlastních zásadách, musí obsahovat celkovou strukturu vlastní zásadu `ClaimsSchema`, `Predicates`, a `InputValidations` element v rámci `BuildingBlocks`.

```XML
  <BuildingBlocks>
    <ClaimsSchema>...</ClaimsSchema>
    <Predicates>...</Predicates>
    <InputValidations>...</InputValidations>
  </BuildingBlocks>
```

Účelem těchto prvků je následujícím způsobem:

- Každý `Predicate` element definuje ověření základní řetězec, který vrací hodnotu true nebo false.
- `InputValidations` Element má jeden nebo více `InputValidation` elementy.  Každý `InputValidation` byl vytvořen pomocí řady `Predicate` elementy. Tento prvek umožňuje provádět logická agregace (podobně jako `and` a `or`).
- `ClaimsSchema` Definuje, jaké deklarace identity se ověřuje.  Potom definuje, které `InputValidation` pravidlo se používá k ověření tuto žádost.

### <a name="defining-a-predicate-element"></a>Definování predikátu element

Predikáty má dva typy metoda: IsLengthRange nebo MatchesRegex. Pojďme se podívat na příklad každého.  Máme první příklad MatchesRegex, který se používá k odpovídají regulárnímu výrazu.  V tomto příkladu odpovídá řetězec, který obsahuje čísla.

```XML
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
```

Další Pojďme se podívat na příklad IsLengthRange.  Tato metoda přebírá řetězec minimální a maximální délku.

```XML
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
```

Použití `HelpText` atribut poskytněte chybovou zprávu pro koncové uživatele, pokud kontrola selže.  Tento řetězec může být lokalizovány pomocí [funkci přizpůsobení jazyka](active-directory-b2c-reference-language-customization.md).

### <a name="defining-an-inputvalidation-element"></a>Definování InputValidation elementu

`InputValidation` Souhrn `PredicateReferences`. Každý `PredicateReferences` musí být splněné, aby `InputValidation` proběhla úspěšně.  Ale uvnitř `PredicateReferences` element použijte atribut volá `MatchAtLeast` k určení kolik `PredicateReference` kontroly musí vrátit hodnotu true.  Volitelně můžete definovat `HelpText` definován atribut přepsat chybovou zprávu v `Predicate` prvky, na které odkazuje.

```XML
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
```

### <a name="defining-a-claimsschema-element"></a>Definování ClaimsSchema element

Typy deklarací identity `newPassword` a `reenterPassword` jsou považovány za zvláštní, tak názvy beze změny.  Uživatelské rozhraní ověřuje uživatele správně znovu zadat heslo během vytváření účtu založené na těchto `ClaimType` elementy.  K vyhledání stejné `ClaimType` prvky, podívejte se v TrustFrameworkBase.xml sady starter.  Co je nového v tomto příkladu je, že jsme se přepsání těchto prvků můžete definovat `InputValidationReference`. `ID` Atribut tento nový element odkazující `InputValidation` element, který jsme definovali.

```XML
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
```

### <a name="putting-it-all-together"></a>Spojení všech součástí dohromady

Tento příklad ukazuje, jak všechno zapadají do formuláře pracovní zásady.  Chcete-li použít tento příklad:

1. Postupujte podle pokynů v požadovanou přípravu popsanou [Začínáme](active-directory-b2c-get-started-custom.md) ke stažení, konfiguraci a nahrání TrustFrameworkBase.xml a TrustFrameworkExtensions.xml
1. Vytvořte soubor SignUporSignIn.xml pomocí příkladu obsah v této části.
1. Aktualizace nahrazující SignUporSignIn.xml `yourtenant` názvem vašeho tenanta Azure AD B2C.
1. Nahrajte soubor zásad SignUporSignIn.xml poslední.

V tomto příkladu obsahuje ověření pro kód pin, hesla a jeden pro silná hesla:

- Vyhledejte `PINpassword`. To `InputValidation` element ověří PIN kód z jakékoli délky.  Není použit v okamžiku, protože se neodkazuje v `InputValidationReference` element v rámci `ClaimType`. 
- Vyhledejte `PasswordValidation`. To `InputValidation` element ověřuje hesla je 8 až 16 znaků a obsahuje 3 ze 4 velká písmena, malá písmena, číslice nebo symboly.  Je odkazováno v `ClaimType`.  Proto se toto pravidlo vynucuje se v této zásadě.

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="yourtenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin">
 <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <BuildingBlocks>
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
    <Predicates>
      <Predicate Id="Lowercase" Method="MatchesRegex" HelpText="a lowercase">
        <Parameters>
          <Parameter Id="RegularExpression">[a-z]+</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="MatchesRegex" HelpText="an uppercase">
        <Parameters>
          <Parameter Id="RegularExpression">[A-Z]+</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="MatchesRegex" HelpText="a number">
        <Parameters>
          <Parameter Id="RegularExpression">[0-9]+</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="MatchesRegex" HelpText="a symbol">
        <Parameters>
          <Parameter Id="RegularExpression">[!@#$%^*()]+</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    <InputValidations>
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
      <InputValidation Id="PINpassword">
        <PredicateReferences Id="PINGroup">
          <PredicateReference Id="PIN" />
        </PredicateReferences>
      </InputValidation>
    </InputValidations>
  </BuildingBlocks>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

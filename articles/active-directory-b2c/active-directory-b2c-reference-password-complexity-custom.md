---
title: Složitost hesla v vlastní zásady – Azure AD B2C | Microsoft Docs
description: Jak konfigurovat požadavky na složitost hesel v vlastní zásady
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.openlocfilehash: 9f8d576cbc5c2bb2fe4109086b04711422911390
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="configure-password-complexity-in-custom-policies"></a>Nakonfigurujte složitost hesel v vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek k pokročilé popis toho, jak funguje složitost hesla a je povolit pomocí vlastních zásad Azure AD B2C.

## <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Azure AD B2C: Konfigurujte požadavky na složitost hesel

Azure Active Directory B2C (Azure AD B2C) podporuje změna požadavky na složitost hesla poskytl koncového uživatele při vytváření účtu.  Ve výchozím nastavení používá Azure AD B2C **silným** hesla.  Azure AD B2C podporuje také konfigurační možnosti pro řízení složitosti hesla, které zákazníci mohou používat.  V tomto článku bude zmíněn konfiguraci složitosti hesla v vlastní zásady.  Je také možné použít [nakonfigurujte složitost hesel v integrovaných zásad](active-directory-b2c-reference-password-complexity.md).

## <a name="prerequisites"></a>Požadavky

Klient služby Azure AD B2C, nakonfigurované k dokončení registrace-množství nebo přihlášení, místní účet, jak je popsáno v [Začínáme](active-directory-b2c-get-started-custom.md).

## <a name="how-to-configure-password-complexity-in-custom-policy"></a>Postup konfigurace složitost hesla ve vlastních zásadách

Nakonfigurujte složitost hesla ve vlastních zásadách, musí obsahovat celková struktura vlastní zásady `ClaimsSchema`, `Predicates`, a `InputValidations` prvku v `BuildingBlocks`.

```XML
  <BuildingBlocks>
    <ClaimsSchema>...</ClaimsSchema>
    <Predicates>...</Predicates>
    <InputValidations>...</InputValidations>
  </BuildingBlocks>
```

Účelem těchto prvků vypadá takto:

- Každý `Predicate` element definuje kontrolu ověření základní řetězec, který vrací hodnotu true nebo false.
- `InputValidations` Element má jeden nebo více `InputValidation` elementy.  Každý `InputValidation` je vytvořený pomocí řady `Predicate` elementy. Tento element umožňuje provádět boolean agregace (podobně jako `and` a `or`).
- `ClaimsSchema` Definuje, které deklarace identity je ověřován.  Potom definuje, které `InputValidation` pravidlo se používá k ověření, že deklarace identity.

### <a name="defining-a-predicate-element"></a>Definování predikátu element

Predikáty má dva typy metoda: IsLengthRange nebo MatchesRegex. Pojďme si každý příkladem.  Nejprve máme příkladem MatchesRegex, který se používá k odpovídají regulárnímu výrazu.  V tomto příkladu odpovídá řetězec, který obsahuje čísla.

```XML
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
```

Další pojďme si příklad IsLengthRange.  Tato metoda přebírá řetězec minimální a maximální délku.

```XML
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
```

Použití `HelpText` atribut poskytněte chybovou zprávu pro koncové uživatele, pokud kontrola selže.  Tento řetězec je možné lokalizovat pomocí [jazyk přizpůsobení funkce](active-directory-b2c-reference-language-customization.md).

### <a name="defining-an-inputvalidation-element"></a>Definování InputValidation element

`InputValidation` Je agregaci `PredicateReferences`. Každý `PredicateReferences` musí být splněné, aby `InputValidation` proběhla úspěšně.  Ale uvnitř `PredicateReferences` element použít atribut `MatchAtLeast` k určení kolik `PredicateReference` kontroly true musí vracet.  Volitelně můžete definovat `HelpText` definován atribut přepsat chybovou zprávu v `Predicate` elementy, které odkazuje.

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

Typy deklarací identity `newPassword` a `reenterPassword` jsou považovány za zvláštní, takže názvy beze změny.  Uživatelské rozhraní ověří uživatele správně znovu zadat hesla během vytváření účtu založené na těchto `ClaimType` elementy.  K vyhledání stejné `ClaimType` elementy, vyhledejte v TrustFrameworkBase.xml v svůj balíček starter.  Co je nového v tomto příkladu je potlačuje tyto prvky můžete definovat `InputValidationReference`. `ID` Atribut tohoto nového elementu odkazuje `InputValidation` element, který jsme definovali.

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

### <a name="putting-it-all-together"></a>Třeba umisťovat všechny společně

Tento příklad ukazuje, jak všechny části zapadají k vytvoření pracovních zásad.  Použití tohoto příkladu:

1. Postupujte podle pokynů v nezbytné [Začínáme](active-directory-b2c-get-started-custom.md) Pokud chcete stáhnout, konfigurace a nahrajte TrustFrameworkBase.xml a TrustFrameworkExtensions.xml
1. Vytvořte soubor SignUporSignIn.xml pomocí příklad obsah v této části.
1. Aktualizovat nahrazení SignUporSignIn.xml `yourtenant` s název vašeho klienta Azure AD B2C.
1. Nahrajte soubor zásad SignUporSignIn.xml poslední.

Tento příklad obsahuje ověření pro hesla PIN kód a jeden pro silná hesla:

- Vyhledejte `PINpassword`. To `InputValidation` element ověří PIN kód o libovolné délce.  Není použit v okamžiku, protože není odkazovaný ve `InputValidationReference` prvku v `ClaimType`. 
- Vyhledejte `PasswordValidation`. To `InputValidation` element ověří hesla je 8 až 16 znaků a obsahuje hodnotu 3 4 velkých a malých písmen, čísel nebo symboly.  Se odkazuje v `ClaimType`.  Toto pravidlo je proto vynucován v této zásadě.

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
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

---
title: Definování technický profil transformace deklarací identity ve vlastních zásadách v Azure Active Directory B2C | Dokumentace Microsoftu
description: Definujte technický profil transformace deklarací identity ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: cd17c146091cd9d35ce35cf2099aa7c6109c9e34
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54303312"
---
# <a name="define-a-claims-transformation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování profilu technické transformace deklarací identity ve vlastních zásadách pro Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Technický profil transformace deklarací identity umožňuje volat výstupní deklarace identit transformací pro manipulaci s hodnotami deklarace identity, ověřování deklarací identity nebo nastavit výchozí hodnoty pro sadu výstupní deklarace identit.

## <a name="protocol"></a>Protocol (Protokol)

**Název** atribut **protokol** elementu musí být nastavena na `Proprietary`. **Obslužná rutina** atribut musí obsahovat plně kvalifikovaný název sestavení obslužné rutiny protokolu, který používá Azure AD B2C: `Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Následující příklad ukazuje technický profil transformace deklarací identity:

```XML
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="output-claims"></a>Výstupní deklarace identit.

**OutputClaims** element je povinný. Je vhodné zadat, že aspoň jednu výstupní deklarace identity vrátí technický profil. Následující příklad ukazuje, jak nastavit výchozí hodnoty na výstupní deklarace identit:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="ageGroup" DefaultValue="Undefined" />
  <OutputClaim ClaimTypeReferenceId="ageGroupValueChanged" DefaultValue="false" />
</OutputClaims>
```

## <a name="output-claims-transformations"></a>Výstupní deklarace identity transformace

**OutputClaimsTransformations** element může obsahovat kolekci **OutputClaimsTransformation** prvků, které slouží k úpravě deklarace identity nebo generovat nové značky. Následující volání technický profil **RemoveAlternativeSecurityIdByIdentityProvider** transformace deklarací identity. Tato deklarace identity transformace odebere z kolekce rozpoznat sociální **AlternativeSecurityIds**. Výstupní deklarace identit tohoto technického profilu jsou **identityProvider2**, který je nastaven na `facebook.com`, a **AlternativeSecurityIds**, který obsahuje seznam přidružený k tomuto sociálních identit uživatel po odebrání facebook.com identity.

```XML
<ClaimsTransformations>
  <ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider"
TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="IdentityProvider2"
TransformationClaimType="identityProvider" />
      <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
...
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="identityProvider2" DefaultValue="facebook.com" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="RemoveAlternativeSecurityIdByIdentityProvider" />
    </OutputClaimsTransformations>
    <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

Technický profil transformace deklarací identity umožňuje provedení transformace deklarací identity z krok Orchestrace všechny cesty uživatele. V následujícím příkladu, krok Orchestrace volá jeden z profilů technické odpojit, například **odpojit. Facebook OAUTH**. Tento technický profil volá deklarace identity transformace technický profil **RemoveAlternativeSecurityIdByIdentityProvider**, který vygeneruje nový **AlternativeSecurityIds2** deklarace identity, který obsahuje seznam sociálních identit uživatelů, odebírání Facebooková identita z kolekcí.

```XML
<UserJourney Id="AccountUnLink">
  <OrchestrationSteps>
    ...
    <OrchestrationStep Order="8" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="UnLinkFacebookExchange" TechnicalProfileReferenceId="UnLink-Facebook-OAUTH" />
        <ClaimsExchange Id="UnLinkMicrosoftExchange" TechnicalProfileReferenceId="UnLink-Microsoft-OAUTH" />
        <ClaimsExchange Id="UnLinkGitHubExchange" TechnicalProfileReferenceId="UnLink-GitHub-OAUTH" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ...
  </OrchestrationSteps>
</UserJourney>
```

## <a name="use-a-validation-technical-profile"></a>Použít technický profil ověření

Technický profil transformace deklarací identity je možné ověřit informace. V následujícím příkladu [samopopisné technický profil](self-asserted-technical-profile.md) s názvem **LocalAccountSignUpWithLogonEmail** vyzve uživatele k zadání e-mailu dvakrát a potom volá [technické ověření profil](validation-technical-profile.md) s názvem **ověřit e-mailu** k ověření e-mailů. **Ověřit e-mailu** technický profil volá transformace deklarací identity **AssertEmailAreEqual** k porovnání dvou deklarací **e-mailu** a **emailRepeat** a vyvolají výjimku, pokud nejsou stejné podle zadané porovnávací.

```XML
<ClaimsTransformations>
  <ClaimsTransformation Id="AssertEmailAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
      <InputClaim ClaimTypeReferenceId="emailRepeat" TransformationClaimType="inputClaim2" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    </InputParameters>
  </ClaimsTransformation>
</ClaimsTransformations>
```

Technický profil volání transformace deklarací identity **AssertEmailAreEqual** deklarace identity transformace, která vyhodnotí, zda uživatel zadal e-mailů jsou stejné.

```XML
<TechnicalProfile Id="Validate-Email">
  <DisplayName>Unlink Facebook</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailRepeat" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAreEqual" />
  </OutputClaimsTransformations>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

A místním s prohlašovanou můžete volání technický profil ověření a zobrazit chybovou zprávu, jak je uvedeno v technickém profilu **UserMessageIfClaimsTransformationStringsAreNotEqual** metadat.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>User ID signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    ...
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">The email addresses you provided are not the same</Item>
  </Metadata>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="emailRepeat" />
    ...
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="Validate-Email" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

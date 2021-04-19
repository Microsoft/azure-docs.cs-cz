---
title: Technické profily podmíněného přístupu ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Referenční informace o vlastních zásadách pro technické profily podmíněného přístupu v Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/19/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebdc1c9c92f6e3debf08cb640e46424c4ad9d5ad
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107721884"
---
# <a name="define-a-conditional-access-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu podmíněného přístupu v Azure Active Directory B2C vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Podmíněný přístup Azure Active Directory (Azure AD) je nástroj používaný Azure AD B2C k spojování signálů, rozhodování a prosazování zásad organizace. Automatizace hodnocení rizik pomocí podmínek zásad znamená, že jsou rizikové přihlašování zjištěné a napravované nebo blokované.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protokol

Atribut **Name** elementu **Protocol** musí být nastaven na hodnotu `Proprietary` . Atribut **obslužné rutiny** musí obsahovat plně kvalifikovaný název sestavení obslužné rutiny protokolu, které je používáno Azure AD B2C:

```
Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

Následující příklad ukazuje technický profil podmíněného přístupu:

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
```

## <a name="conditional-access-evaluation"></a>Vyhodnocení podmíněného přístupu

Pro každé přihlášení Azure AD B2C vyhodnotí všechny zásady a před udělením přístupu uživatele zajistí splnění všech požadavků. Možnost blokovat přístup přepíše všechna ostatní nastavení konfigurace. Režim **vyhodnocení** technického profilu podmíněného přístupu vyhodnocuje signály shromážděné Azure AD B2C během přihlašování pomocí místního účtu. Výsledkem technického profilu podmíněného přístupu je sada deklarací identity, které jsou výsledkem vyhodnocení podmíněného přístupu. Zásady Azure AD B2C používají tyto deklarace v dalším kroku orchestrace k provedení akce, jako je například blokování uživatele nebo odvolání uživatele pomocí služby Multi-Factor Authentication. Pro tento režim lze nakonfigurovat následující možnosti.

### <a name="metadata"></a>Metadata

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Typ operace | Yes | Musí být **vyhodnocena**.  |

### <a name="input-claims"></a>Vstupní deklarace identity

Element **InputClaims** obsahuje seznam deklarací pro odeslání do podmíněného přístupu. Název vaší deklarace identity můžete také namapovat na název definovaný v technickém profilu podmíněného přístupu.

| ClaimReferenceId | Vyžadováno | Typ dat | Popis |
| --------- | -------- | ----------- |----------- |
| UserId | Yes | řetězec | Identifikátor uživatele, který se přihlásí. |
| AuthenticationMethodsUsed | Yes |Třída StringCollection | Seznam metod, které uživatel použil k přihlášení Možné hodnoty: `Password` , a `OneTimePasscode` . |
| Federované | Yes |boolean | Označuje, jestli se uživatel přihlásil pomocí federovaného účtu. Hodnota musí být `false` . |
| IsMfaRegistered | Yes |boolean | Označuje, zda již uživatel zaregistroval telefonní číslo pro službu Multi-Factor Authentication. |


Element **InputClaimsTransformations** může obsahovat kolekci prvků **InputClaimsTransformation** , které se používají k úpravě vstupních deklarací identity nebo k vygenerování nových objektů před jejich odesláním do služby podmíněného přístupu.

### <a name="output-claims"></a>Deklarace výstupů

Element **OutputClaims** obsahuje seznam deklarací generovaných ConditionalAccessProtocolProvider. Název vaší deklarace identity můžete také namapovat na název definovaný níže.

| ClaimReferenceId | Vyžadováno | Typ dat | Popis |
| --------- | -------- | ----------- |----------- |
| Výzvy | Yes |Třída StringCollection | Seznam akcí pro nápravu identifikované hrozby. Možné hodnoty: `block` |
| MultiConditionalAccessStatus | Yes | Třída StringCollection |  |

Element **OutputClaimsTransformations** může obsahovat kolekci prvků **OutputClaimsTransformation** , které se používají k úpravě výstupních deklarací identity nebo k vygenerování nových.

### <a name="example-evaluation"></a>Příklad: vyhodnocení

Následující příklad ukazuje technický profil podmíněného přístupu, který se používá k vyhodnocení hrozby přihlášení.

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="IsMfaRegisteredCT" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="UserId" />
    <InputClaim ClaimTypeReferenceId="AuthenticationMethodsUsed" />
    <InputClaim ClaimTypeReferenceId="IsFederated" DefaultValue="false" />
    <InputClaim ClaimTypeReferenceId="IsMfaRegistered" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="Challenges" />
    <OutputClaim ClaimTypeReferenceId="ConditionalAccessStatus" PartnerClaimType="MultiConditionalAccessStatus" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="remediation"></a>Náprava

Režim **oprav** pro technický profil podmíněného přístupu informuje Azure AD B2C, že se zjištěná hrozba pro přihlášení napravuje. Pro opravný režim lze nakonfigurovat následující možnosti.

### <a name="metadata"></a>Metadata

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Typ operace | Yes | Je nutné provést **nápravu**.  |

### <a name="input-claims"></a>Vstupní deklarace identity

Element **InputClaims** obsahuje seznam deklarací pro odeslání do podmíněného přístupu. Název vaší deklarace identity můžete také namapovat na název definovaný v technickém profilu podmíněného přístupu.

| ClaimReferenceId | Vyžadováno | Typ dat | Popis |
| --------- | -------- | ----------- |----------- |
| ChallengesSatisfied | Yes | Třída StringCollection| Seznam uspokojivých výzev k nápravě identifikované hrozby jako návrat z režimu vyhodnocení, s nárokem na výzvy.|


Element **InputClaimsTransformations** může obsahovat kolekci prvků **InputClaimsTransformation** , které se používají k úpravě vstupních deklarací identity nebo k vygenerování nových před voláním služby podmíněného přístupu.

### <a name="output-claims"></a>Deklarace výstupů

Zprostředkovatel protokolu podmíněného přístupu nevrací žádné **OutputClaims**, takže není nutné zadávat deklarace výstupů. Můžete ale zahrnout deklarace identity, které nevrací poskytovatel protokolu podmíněného přístupu, pokud nastavíte `DefaultValue` atribut.

Element **OutputClaimsTransformations** může obsahovat kolekci prvků **OutputClaimsTransformation** , které se používají k úpravě výstupních deklarací identity nebo k vygenerování nových.

### <a name="example-remediation"></a>Příklad: náprava

Následující příklad ukazuje technický profil podmíněného přístupu, který se používá k nápravě hrozby přihlášení.

```xml
<TechnicalProfile Id="ConditionalAccessRemediation">
  <DisplayName>Conditional Access Remediation</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
  <Metadata>
    <Item Key="OperationType">Remediation</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="ChallengesSatisfied" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Další kroky

- Naučte se, jak [Přidat podmíněný přístup ke tokům uživatelů v Azure Active Directory B2C](conditional-access-user-flow.md).

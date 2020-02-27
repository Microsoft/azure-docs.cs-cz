---
title: Příklady logických transformací deklarací identity pro vlastní zásady
titleSuffix: Azure AD B2C
description: Příklady logických transformací deklarací identity pro schéma rozhraní IEF (identity Experience Framework) Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 130fca4d5894316e7684270ff9d6361e9d9f9dd3
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620835"
---
# <a name="boolean-claims-transformations"></a>Logické transformace deklarací identity

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek popisuje příklady použití logických transformací identity schématu rozhraní identity Experience v Azure Active Directory B2C (Azure AD B2C). Další informace najdete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="andclaims"></a>AndClaims

Provede operaci a se dvěma logickými inputClaims a nastaví outputClaim s výsledkem operace.

| Položka  | TransformationClaimType  | Typ dat  | Poznámky: |
|-------| ------------------------ | ---------- | ----- |
| inputClaim | inputClaim1 | Boolean | První deklarace ClaimType pro vyhodnocení. |
| inputClaim | inputClaim2  | Boolean | Druhá deklarace ClaimType pro vyhodnocení. |
|outputClaim | outputClaim | Boolean | ClaimTypes, který bude vytvořen po vyvolání této transformace deklarací (true nebo false). |

Následující transformace deklarací identity ukazuje, jak a dvě Boolean ClaimTypes: `isEmailNotExist`a `isSocialAccount`. Výstupní `presentEmailSelfAsserted` deklarace identity je nastavená na `true`, pokud je hodnota obou vstupních deklarací `true`. V kroku orchestrace můžete použít předběžnou podmínku k přednastavení stránky s vlastním kontrolním účtem, jenom když je e-mail účtu v sociální síti prázdný.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="AndClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isEmailNotExist" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isSocialAccount" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentEmailSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
    - **inputClaim1**: true
    - **inputClaim2**: false
- Deklarace výstupů:
    - **outputClaim**: false


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

Kontroluje, zda logické hodnoty dvou deklarací identity jsou stejné, a vyvolá výjimku, pokud nejsou.

| Položka | TransformationClaimType  | Typ dat  | Poznámky: |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | Boolean | Deklarace ClaimType, která má být uplatněna. |
| InputParameter |valueToCompareTo | Boolean | Hodnota, která má být porovnána (true nebo false). |

Transformace deklarací **AssertBooleanClaimIsEqualToValue** je vždy prováděna z [technického profilu ověření](validation-technical-profile.md) , který je volán pomocí [technického profilu s vlastním uplatněním](self-asserted-technical-profile.md). Metadata technického profilu **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** s vlastním uplatněním řídí chybovou zprávu, kterou poskytuje technický profil uživateli.

![Spuštění AssertStringClaimsAreEqual](./media/boolean-transformations/assert-execution.png)

Následující transformace deklarací identity ukazuje, jak kontrolovat hodnotu logického typu ClaimType s hodnotou `true`. Pokud je hodnota `accountEnabled` ClaimType false, je vyvolána chybová zpráva.

```XML
<ClaimsTransformation Id="AssertAccountEnabledIsTrue" TransformationMethod="AssertBooleanClaimIsEqualToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="accountEnabled" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```


Technický profil ověření `login-NonInteractive` volá transformaci deklarací `AssertAccountEnabledIsTrue`.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Technický profil s vlastním uplatněním volá ověřovací **přihlášení – neinteraktivní** technický profil.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Custom error message if account is disabled.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
    - **inputClaim**: false
    - **valueToCompareTo**: true
- Výsledek: došlo k chybě

## <a name="comparebooleanclaimtovalue"></a>CompareBooleanClaimToValue

Kontroluje, zda je logická hodnota deklarace identity rovna `true` nebo `false`a vrátí výsledek komprese. 

| Položka | TransformationClaimType  | Typ dat  | Poznámky: |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | Boolean | Deklarace ClaimType, která má být uplatněna. |
| InputParameter |valueToCompareTo | Boolean | Hodnota, která má být porovnána (true nebo false). |
| outputClaim | compareResult | Boolean | Deklarace ClaimType, která je vytvořena po vyvolání tohoto ClaimsTransformation. |


Následující transformace deklarací identity ukazuje, jak kontrolovat hodnotu logického typu ClaimType s hodnotou `true`. Pokud je hodnota `IsAgeOver21Years` ClaimType rovna `true`, transformace deklarací identity vrátí `true`, jinak `false`.

```XML
<ClaimsTransformation Id="AssertAccountEnabled" TransformationMethod="CompareBooleanClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="IsAgeOver21Years" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
      <OutputClaim  ClaimTypeReferenceId="accountEnabled" TransformationClaimType="compareResult"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
    - **inputClaim**: false
- Vstupní parametry:
    - **valueToCompareTo**: true
- Deklarace výstupů:
    - **compareResult**: false 



## <a name="notclaims"></a>NotClaims

Provede operaci not pro logickou inputClaim a nastaví outputClaim s výsledkem operace.

| Položka | TransformationClaimType | Typ dat | Poznámky: |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | Boolean | Deklaraci identity, která se má provozovat. |
| outputClaim | outputClaim | Boolean | ClaimTypes, které jsou vytvářeny po vyvolání tohoto ClaimsTransformation (true nebo false). |

Pomocí této transformace deklarace identity proveďte logickou negaci deklarace identity.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="NotClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
    - **inputClaim**: false
- Deklarace výstupů:
    - **outputClaim**: true

## <a name="orclaims"></a>OrClaims

Vypočítá nebo ze dvou logických inputClaims a nastaví outputClaim s výsledkem operace.

| Položka | TransformationClaimType | Typ dat | Poznámky: |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | Boolean | První deklarace ClaimType pro vyhodnocení. |
| inputClaim | inputClaim2 | Boolean | Druhá deklarace ClaimType pro vyhodnocení. |
| outputClaim | outputClaim | Boolean | ClaimTypes, který bude vytvořen po vyvolání této ClaimsTransformation (true nebo false). |

Následující transformace deklarací identity ukazuje, jak `Or` dvou logických ClaimTypes. V kroku orchestrace můžete použít předběžnou podmínku k přednastavení stránky s vlastním oceněním, pokud je hodnota jedné z deklarací `true`.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="OrClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedNotExists" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentTOSSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
    - **inputClaim1**: true
    - **inputClaim2**: false
- Deklarace výstupů:
    - **outputClaim**: true

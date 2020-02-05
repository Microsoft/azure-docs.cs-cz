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
ms.openlocfilehash: f0d6d74271cc4ff0be4a653b389cc70ad5c56ef9
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76983074"
---
# <a name="boolean-claims-transformations"></a>Logické transformace deklarací identity

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek popisuje příklady použití logických transformací identity schématu rozhraní identity Experience v Azure Active Directory B2C (Azure AD B2C). Další informace najdete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="andclaims"></a>AndClaims

Provede operaci a se dvěma logickými inputClaims a nastaví outputClaim s výsledkem operace.

| Položka  | TransformationClaimType  | Typ dat  | Poznámky |
|-------| ------------------------ | ---------- | ----- |
| InputClaim | inputClaim1 | Boolean | První deklarace ClaimType pro vyhodnocení. |
| InputClaim | inputClaim2  | Boolean | Druhá deklarace ClaimType pro vyhodnocení. |
|OutputClaim | OutputClaim | Boolean | ClaimTypes, který bude vytvořen po vyvolání této transformace deklarací (true nebo false). |

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

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **inputClaim1**: true
    - **inputClaim2**: false
- Deklarace výstupů:
    - **outputClaim**: false


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

Kontroluje, zda logické hodnoty dvou deklarací identity jsou stejné, a vyvolá výjimku, pokud nejsou.

| Položka | TransformationClaimType  | Typ dat  | Poznámky |
| ---- | ------------------------ | ---------- | ----- |
| InputClaim | InputClaim | Boolean | Deklarace ClaimType, která má být uplatněna. |
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

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **inputClaim**: false
    - **valueToCompareTo**: true
- Výsledek: došlo k chybě

## <a name="comparebooleanclaimtovalue"></a>CompareBooleanClaimToValue

Kontroluje, zda je logická hodnota deklarace identity rovna `true` nebo `false`a vrátí výsledek komprese. 

| Položka | TransformationClaimType  | Typ dat  | Poznámky |
| ---- | ------------------------ | ---------- | ----- |
| InputClaim | InputClaim | Boolean | Deklarace ClaimType, která má být uplatněna. |
| InputParameter |valueToCompareTo | Boolean | Hodnota, která má být porovnána (true nebo false). |
| OutputClaim | InputClaim | Boolean | Deklarace ClaimType, která je vytvořena po vyvolání tohoto ClaimsTransformation. |


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

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **inputClaim**: false
- Vstupní parametry:
    - **valueToCompareTo**: true
- Deklarace výstupů:
    - **compareResult**: false 



## <a name="notclaims"></a>NotClaims

Provede operaci not pro logickou inputClaim a nastaví outputClaim s výsledkem operace.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | Boolean | Deklaraci identity, která se má provozovat. |
| OutputClaim | OutputClaim | Boolean | ClaimTypes, které jsou vytvářeny po vyvolání tohoto ClaimsTransformation (true nebo false). |

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

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **inputClaim**: false
- Deklarace výstupů:
    - **outputClaim**: true

## <a name="orclaims"></a>OrClaims

Vypočítá nebo ze dvou logických inputClaims a nastaví outputClaim s výsledkem operace.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | Boolean | První deklarace ClaimType pro vyhodnocení. |
| InputClaim | inputClaim2 | Boolean | Druhá deklarace ClaimType pro vyhodnocení. |
| OutputClaim | OutputClaim | Boolean | ClaimTypes, který bude vytvořen po vyvolání této ClaimsTransformation (true nebo false). |

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

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **inputClaim1**: true
    - **inputClaim2**: false
- Deklarace výstupů:
    - **outputClaim**: true

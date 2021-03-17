---
title: Příklady logických transformací deklarací identity pro vlastní zásady
titleSuffix: Azure AD B2C
description: Příklady logických transformací deklarací identity pro schéma rozhraní IEF (identity Experience Framework) Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/06/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7c292f939339add06168c55236f8666651e4aace
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85201272"
---
# <a name="boolean-claims-transformations"></a>Logické transformace deklarací identity

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek popisuje příklady použití logických transformací identity schématu rozhraní identity Experience v Azure Active Directory B2C (Azure AD B2C). Další informace najdete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="andclaims"></a>AndClaims

Provede operaci a se dvěma logickými inputClaims a nastaví outputClaim s výsledkem operace.

| Položka  | TransformationClaimType  | Typ dat  | Poznámky |
|-------| ------------------------ | ---------- | ----- |
| InputClaim | inputClaim1 | boolean | První deklarace ClaimType pro vyhodnocení. |
| InputClaim | inputClaim2  | boolean | Druhá deklarace ClaimType pro vyhodnocení. |
|OutputClaim | outputClaim | boolean | ClaimTypes, který bude vytvořen po vyvolání této transformace deklarací (true nebo false). |

Následující transformace deklarací identity ukazuje, jak a dvě booleovské ClaimTypes: a `isEmailNotExist` `isSocialAccount` . Výstupní deklarace identity `presentEmailSelfAsserted` je nastavena na `true` hodnotu, pokud jsou obě vstupní deklarace identity `true` . V kroku orchestrace můžete použít předběžnou podmínku k přednastavení stránky s vlastním kontrolním účtem, jenom když je e-mail účtu v sociální síti prázdný.

```xml
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

### <a name="example-of-andclaims"></a>Příklad AndClaims

- Vstupní deklarace identity:
    - **inputClaim1**: true
    - **inputClaim2**: false
- Deklarace výstupů:
    - **outputClaim**: false


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

Kontroluje, zda logické hodnoty dvou deklarací identity jsou stejné, a vyvolá výjimku, pokud nejsou.

| Položka | TransformationClaimType  | Typ dat  | Poznámky |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | boolean | Deklarace ClaimType, která má být uplatněna. |
| InputParameter |valueToCompareTo | boolean | Hodnota, která má být porovnána (true nebo false). |

Transformace deklarací **AssertBooleanClaimIsEqualToValue** je vždy prováděna z [technického profilu ověření](validation-technical-profile.md) , který je volán pomocí [technického profilu s vlastním uplatněním](self-asserted-technical-profile.md). Metadata technického profilu **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** s vlastním uplatněním řídí chybovou zprávu, kterou poskytuje technický profil uživateli. Chybové zprávy lze [lokalizovat](localization-string-ids.md#claims-transformations-error-messages).

![Spuštění AssertStringClaimsAreEqual](./media/boolean-transformations/assert-execution.png)

Následující transformace deklarací identity ukazuje, jak ověřit hodnotu logického typu ClaimType s `true` hodnotou. Pokud `accountEnabled` je hodnota ClaimType false, je vyvolána chybová zpráva.

```xml
<ClaimsTransformation Id="AssertAccountEnabledIsTrue" TransformationMethod="AssertBooleanClaimIsEqualToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="accountEnabled" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```


`login-NonInteractive`Technický profil ověření volá `AssertAccountEnabledIsTrue` transformaci deklarací identity.

```xml
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Technický profil s vlastním uplatněním volá ověřovací **přihlášení – neinteraktivní** technický profil.

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Custom error message if account is disabled.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example-of-assertbooleanclaimisequaltovalue"></a>Příklad AssertBooleanClaimIsEqualToValue

- Vstupní deklarace identity:
    - **inputClaim**: false
    - **valueToCompareTo**: true
- Výsledek: došlo k chybě

## <a name="comparebooleanclaimtovalue"></a>CompareBooleanClaimToValue

Kontroluje, zda je logická hodnota deklarace identity rovna hodnotě `true` nebo `false` a vrátí výsledek komprese.

| Položka | TransformationClaimType  | Typ dat  | Poznámky |
| ---- | ------------------------ | ---------- | ----- |
| InputClaim | inputClaim | boolean | Deklarace ClaimType, která má být uplatněna. |
| InputParameter |valueToCompareTo | boolean | Hodnota, která má být porovnána (true nebo false). |
| OutputClaim | compareResult | boolean | Deklarace ClaimType, která je vytvořena po vyvolání tohoto ClaimsTransformation. |

Následující transformace deklarací identity ukazuje, jak ověřit hodnotu logického typu ClaimType s `true` hodnotou. Pokud `IsAgeOver21Years` je hodnota ClaimType rovna `true` , transformace deklarací identity vrátí `true` , jinak `false` .

```xml
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

### <a name="example-of-comparebooleanclaimtovalue"></a>Příklad CompareBooleanClaimToValue

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
| InputClaim | inputClaim | boolean | Deklaraci identity, která se má provozovat. |
| OutputClaim | outputClaim | boolean | ClaimTypes, které jsou vytvářeny po vyvolání tohoto ClaimsTransformation (true nebo false). |

Pomocí této transformace deklarace identity proveďte logickou negaci deklarace identity.

```xml
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="NotClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-notclaims"></a>Příklad NotClaims

- Vstupní deklarace identity:
    - **inputClaim**: false
- Deklarace výstupů:
    - **outputClaim**: true

## <a name="orclaims"></a>OrClaims

Vypočítá nebo ze dvou logických inputClaims a nastaví outputClaim s výsledkem operace.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | boolean | První deklarace ClaimType pro vyhodnocení. |
| InputClaim | inputClaim2 | boolean | Druhá deklarace ClaimType pro vyhodnocení. |
| OutputClaim | outputClaim | boolean | ClaimTypes, který bude vytvořen po vyvolání této ClaimsTransformation (true nebo false). |

Následující transformace deklarací identity ukazuje, jak se `Or` dvěma logickými ClaimTypesy. V kroku orchestrace můžete použít předběžnou podmínku k přednastavení stránky s vlastním oceněním, pokud je hodnota jedné z deklarací `true` .

```xml
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="OrClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedNotExists" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentTOSSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-orclaims"></a>Příklad OrClaims

- Vstupní deklarace identity:
    - **inputClaim1**: true
    - **inputClaim2**: false
- Deklarace výstupů:
    - **outputClaim**: true

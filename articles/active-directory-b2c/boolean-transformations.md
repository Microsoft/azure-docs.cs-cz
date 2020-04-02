---
title: Logické deklarace identity příklady transformace pro vlastní zásady
titleSuffix: Azure AD B2C
description: Logické deklarace identity transformace příklady pro rozhraní Identity Experience Framework (IEF) schéma Služby Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 007d613a1f170a0ee278a838c92ade2fce9c6dec
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529206"
---
# <a name="boolean-claims-transformations"></a>Logická hodnota deklarací identity

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek obsahuje příklady pro použití logických deklarací transformace schématu identity experience framework v Azure Active Directory B2C (Azure AD B2C). Další informace naleznete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="andclaims"></a>AndClaims

Provede and operace dvou logických inputClaims a nastaví outputClaim s výsledkem operace.

| Položka  | TransformationClaimType  | Typ dat  | Poznámky |
|-------| ------------------------ | ---------- | ----- |
| Vstupní deklarace | inputClaim1 | Boolean | První ClaimType vyhodnotit. |
| Vstupní deklarace | inputClaim2  | Boolean | Druhý ClaimType vyhodnotit. |
|Výstupní nárok | outputClaim | Boolean | ClaimTypes, které budou vytvořeny po této deklarace transformace byla vyvolána (true nebo false). |

Následující deklarace transformace ukazuje, jak and dva `isEmailNotExist`logické `isSocialAccount`ClaimTypes: , a . Výstupní deklarace `presentEmailSelfAsserted` je `true` nastavena na, pokud `true`je hodnota obou vstupních deklarací . V kroku orchestrace můžete použít předběžnou podmínku pro přednastavení stránky s vlastním uplatněním, pouze pokud je e-mail sociálního účtu prázdný.

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

- Vstupní deklarace:
    - **inputClaim1**: true
    - **inputClaim2**: false
- Výstupní nároky:
    - **outputClaim**: false


## <a name="assertbooleanclaimisequaltovalue"></a>AssertToleanClaimIsEqualToValue

Zkontroluje, zda jsou logické hodnoty dvou deklarací stejné a vyvolá výjimku, pokud nejsou.

| Položka | TransformationClaimType  | Typ dat  | Poznámky |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | Boolean | ClaimType, které mají být uplatněny. |
| Parametr Input |valueToCompareTo | Boolean | Hodnota porovnat (true nebo false). |

**AssertBooleanClaimIsEqualToValue** nároky transformace je vždy proveden z [ověření technického profilu,](validation-technical-profile.md) který je volán [samostatně uplatněný technický profil](self-asserted-technical-profile.md). Metadata technického profilu, která se zobrazuje v **uživatelskézprávěIfIfClaims, určuje základní hodnotu Jsounotačové** a nesená metadata technického profilu, určuje chybovou zprávu, kterou uživateli zobrazí technický profil. Chybové zprávy lze [lokalizovat](localization-string-ids.md#claims-transformations-error-messages).

![AssertStringClaimsAreEqual provádění](./media/boolean-transformations/assert-execution.png)

Následující transformace deklarací ukazuje, jak zkontrolovat hodnotu logického `true` claimtype s hodnotou. Pokud je hodnota `accountEnabled` ClaimType false, je vyvolána chybová zpráva.

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


Technický `login-NonInteractive` profil ověření `AssertAccountEnabledIsTrue` volá transformaci deklarací identity.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Samoobslužný technický profil volá ověření **login-NonInteractive** technický profil.

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

- Vstupní deklarace:
    - **inputClaim**: false
    - **valueToCompareTo**: true
- Výsledek: Vyvolána chyba

## <a name="comparebooleanclaimtovalue"></a>CompareBooleanClaimToValue

Zkontroluje, zda je logická `true` hodnota `false`deklarace rovná nebo , a vrátí výsledek komprese.

| Položka | TransformationClaimType  | Typ dat  | Poznámky |
| ---- | ------------------------ | ---------- | ----- |
| Vstupní deklarace | inputClaim | Boolean | ClaimType, které mají být uplatněny. |
| Parametr Input |valueToCompareTo | Boolean | Hodnota porovnat (true nebo false). |
| Výstupní nárok | compareVýsledek | Boolean | ClaimType, který je vytvořen po této ClaimsTransformation byla vyvolána. |


Následující transformace deklarací ukazuje, jak zkontrolovat hodnotu logického `true` claimtype s hodnotou. Pokud je hodnota `IsAgeOver21Years` ClaimType rovna `true`, transformace `true`deklarací vrátí , jinak `false`.

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

- Vstupní deklarace:
    - **inputClaim**: false
- Vstupní parametry:
    - **valueToCompareTo**: true
- Výstupní nároky:
    - **compareResult**: false



## <a name="notclaims"></a>Notclaims

Provede operaci Není logické vstupní Deklarace a nastaví výstupDeklarac s výsledkem operace.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | inputClaim | Boolean | Nárok, který má být provozován. |
| Výstupní nárok | outputClaim | Boolean | ClaimTypes, které jsou vytvořeny po této ClaimsTransformation byla vyvolána (true nebo false). |

Pomocí této transformace deklarace můžete provést logické negace deklarace identity.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="NotClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

- Vstupní deklarace:
    - **inputClaim**: false
- Výstupní nároky:
    - **outputClaim**: true outputClaim : true outputClaim : true outputClaim

## <a name="orclaims"></a>OrClaims

Vypočítá Nebo dvou logických vstupů Deklarace a nastaví outputClaim s výsledkem operace.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | inputClaim1 | Boolean | První ClaimType vyhodnotit. |
| Vstupní deklarace | inputClaim2 | Boolean | Druhý ClaimType vyhodnotit. |
| Výstupní nárok | outputClaim | Boolean | ClaimTypes, které budou vytvořeny po této ClaimsTransformation byla vyvolána (true nebo false). |

Následující transformace deklarací ukazuje, jak dva `Or` logické ClaimTypes. V kroku orchestrace můžete použít předběžnou podmínku pro přednastavení stránky s vlastním uplatněním, `true`pokud je hodnota jedné z deklarací .

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
```

### <a name="example"></a>Příklad

- Vstupní deklarace:
    - **inputClaim1**: true
    - **inputClaim2**: false
- Výstupní nároky:
    - **outputClaim**: true outputClaim : true outputClaim : true outputClaim

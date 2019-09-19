---
title: Příklady převodu logických deklarací identity pro schéma rozhraní Azure Active Directory B2C pro prostředí identity | Microsoft Docs
description: Příklady logických transformací deklarací identity pro schéma rozhraní Azure Active Directory B2C prostředí identit.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: da4fc4704ee72210e180ef95fe6a821c8d116fa2
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064564"
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
|OutputClaim | OutputClaim | boolean | ClaimTypes, který bude vytvořen po vyvolání této transformace deklarací (true nebo false). |

Následující transformace deklarací identity ukazuje `isEmailNotExist` `isSocialAccount`, jak a dvě booleovské ClaimTypes: a. Výstupní deklarace identity `presentEmailSelfAsserted` je nastavena na `true` hodnotu, pokud jsou `true`obě vstupní deklarace identity. V kroku orchestrace můžete použít předběžnou podmínku k přednastavení stránky s vlastním kontrolním účtem, jenom když je e-mail účtu v sociální síti prázdný.

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

| Položka | TransformationClaimType  | Typ dat  | Poznámky |
| ---- | ------------------------ | ---------- | ----- |
| InputClaim | InputClaim | boolean | Deklarace ClaimType, která má být uplatněna. |
| InputParameter |valueToCompareTo | boolean | Hodnota, která má být porovnána (true nebo false). |

Transformace deklarací **AssertBooleanClaimIsEqualToValue** je vždy prováděna z [technického profilu ověření](validation-technical-profile.md) , který je volán pomocí [technického profilu s vlastním uplatněním](self-asserted-technical-profile.md). Metadata technického profilu **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** s vlastním uplatněním řídí chybovou zprávu, kterou poskytuje technický profil uživateli.

![Spuštění AssertStringClaimsAreEqual](./media/boolean-transformations/assert-execution.png)

Následující transformace deklarací identity ukazuje, jak ověřit hodnotu logického typu ClaimType s `true` hodnotou. Pokud je hodnota `accountEnabled` ClaimType false, je vyvolána chybová zpráva.

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


`AssertAccountEnabledIsTrue` Technický profil `login-NonInteractive` ověření volá transformaci deklarací identity.
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
- Vyústit Došlo k chybě

## <a name="notclaims"></a>NotClaims

Provede operaci not pro logickou inputClaim a nastaví outputClaim s výsledkem operace.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | boolean | Deklaraci identity, která se má provozovat. |
| OutputClaim | OutputClaim | boolean | ClaimTypes, které jsou vytvářeny po vyvolání tohoto ClaimsTransformation (true nebo false). |

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

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | boolean | První deklarace ClaimType pro vyhodnocení. |
| InputClaim | inputClaim2 | boolean | Druhá deklarace ClaimType pro vyhodnocení. |
| OutputClaim | OutputClaim | boolean | ClaimTypes, který bude vytvořen po vyvolání této ClaimsTransformation (true nebo false). |

Následující transformace deklarací identity ukazuje, jak `Or` se dvěma logickými ClaimTypesy. V kroku orchestrace můžete použít předběžnou podmínku k přednastavení stránky s vlastním oceněním, pokud je `true`hodnota jedné z deklarací.

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


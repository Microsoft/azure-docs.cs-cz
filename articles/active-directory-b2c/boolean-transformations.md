---
title: Datový typ Boolean deklaraci příklady transformaci identita prostředí Framework schéma z Azure Active Directory B2C | Dokumentace Microsoftu
description: Datový typ Boolean deklaraci příklady transformaci identita prostředí Framework schéma z Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1afbf8e8473e12992b7f031ac9835a58e1089b0d
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54853268"
---
# <a name="boolean-claims-transformations"></a>Logická hodnota deklarace identity transformace

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek obsahuje příklady použití transformace logická deklarací schématu architekturu rozhraní identit v Azure Active Directory (Azure AD) B2C. Další informace najdete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="andclaims"></a>AndClaims

Provede operaci a dvě logické inputClaims a nastaví outputClaim s výsledek operace.

| Položka  | TransformationClaimType  | Typ dat  | Poznámky |
|-------| ------------------------ | ---------- | ----- |
| InputClaim | inputClaim1 | Boolean | První typ ClaimType k vyhodnocení. |
| InputClaim | inputClaim2  | Boolean | Druhý typ ClaimType k vyhodnocení. |
|outputClaim | outputClaim | Boolean | Zavolání ClaimTypes, který bude vytvořen poté, co to transformace deklarací identity (PRAVDA nebo NEPRAVDA). |

Následující deklarace identity transformace ukazuje, jak a dvě logické ClaimTypes: `isEmailNotExist`, a `isSocialAccount`. Výstupní deklarací `presentEmailSelfAsserted` je nastavena na `true` Pokud hodnota oba vstupní deklarace identity jsou `true`. V jednom z kroků Orchestrace vám pomůže předběžné podmínky přednastavení stránku s vlastním potvrzením pouze v případě, že účtu na sociální síti, e-mailu je prázdný.

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
- Výstupní deklarace identit:
    - **outputClaim**: false


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

Kontroluje, zda jsou si rovny logické hodnoty dvou deklarací a vyvolá výjimku, pokud tomu tak není.

| Položka | TransformationClaimType  | Typ dat  | Poznámky |
| ---- | ------------------------ | ---------- | ----- |
| InputClaim | InputClaim | Boolean | Typ ClaimType se bude posuzovat. |
| InputParameter |valueToCompareTo | Boolean | Hodnota pro porovnání (true nebo false). |

**AssertBooleanClaimIsEqualToValue** transformaci deklarací identity je vždy spuštěn z [technický profil ověření](validation-technical-profile.md) , který je volán [držitelem s prohlašovanou technický profil](self-asserted-technical-profile.md). **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** technického profilu s vlastním potvrzením určuje chybovou zprávu, která se uživateli zobrazí technický profil.

![AssertStringClaimsAreEqual execution](./media/boolean-transformations/assert-execution.png)

Následující transformace deklarací identity ukazuje, jak zkontrolovat hodnoty s logickou typu deklarace identity `true` hodnotu. Pokud hodnota `accountEnabled` typu deklarace identity má hodnotu false, je vyvolána chybovou zprávu.

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


`login-NonInteractive` Volání technický profil ověření `AssertAccountEnabledIsTrue` transformace deklarací identity.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Volání ověření s vlastním potvrzením technický profil **neinteraktivní přihlášení** technický profil.

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
- Výsledek: Vyvolána chyba

## <a name="notclaims"></a>NotClaims

Provádí operaci není logická inputClaim a nastaví outputClaim s výsledek operace.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | Boolean | Deklarace identity do provozu. |
| outputClaim | outputClaim | Boolean | ClaimTypes vytvořený po zavolání této ClaimsTransformation (PRAVDA nebo NEPRAVDA). |

Tato transformace deklarací identity použijte k provedení Logická negace na deklaraci identity.

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
- Výstupní deklarace identit:
    - **outputClaim**: true

## <a name="orclaims"></a>OrClaims 

Vypočítá nebo dvě logické inputClaims a nastaví outputClaim s výsledek operace.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | Boolean | První typ ClaimType k vyhodnocení. |
| InputClaim | inputClaim2 | Boolean | Druhý typ ClaimType k vyhodnocení. |
| outputClaim | outputClaim | Boolean | ClaimTypes, který bude vytvořen po zavolání této ClaimsTransformation (PRAVDA nebo NEPRAVDA). |

Následující deklarace identity transformace ukazuje, jak `Or` dvě logické ClaimTypes. V kroku Orchestrace, vám pomůže předběžné podmínky přednastavení s vlastním potvrzením stránky, pokud je hodnota jednoho z deklarací identity `true`.

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
- Výstupní deklarace identit:
    - **outputClaim**: true


---
title: Příklady transformace deklarací dat pro vlastní zásady
description: Příklady transformace deklarace identity pro schéma IEF (identity Experience Framework) Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bde2fcad6f84e4a2df5268d1135e88a263b65ee0
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949112"
---
# <a name="date-claims-transformations"></a>Transformace deklarací data

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek popisuje příklady použití transformací data ze schématu rozhraní identity Experience schématu v Azure Active Directory B2C (Azure AD B2C). Další informace najdete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="assertdatetimeisgreaterthan"></a>AssertDateTimeIsGreaterThan

Kontroluje, že jedna deklarace identity data a času (datový typ String) je pozdější než druhá deklarace identity data a času (datový typ String) a vyvolá výjimku.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | leftOperand | string | Typ první deklarace, který by měl být pozdější než druhá deklarace identity. |
| InputClaim | rightOperand | string | Typ druhé deklarace identity, který by měl být dřívější než první deklarace identity. |
| InputParameter | AssertIfEqualTo | Boolean | Určuje, zda má být tento kontrolní výraz splněn, je-li levý operand roven pravému operandu. |
| InputParameter | AssertIfRightOperandIsNotPresent | Boolean | Určuje, zda má být tento kontrolní výraz splněn, pokud chybí pravý operand. |
| InputParameter | TreatAsEqualIfWithinMillseconds | int | Určuje počet milisekund, které mají být povoleny mezi dvěma hodnotami data a času, které mají být považovat za časy rovnající se (například pro účet pro časový posun). |

Transformace deklarací **AssertDateTimeIsGreaterThan** je vždy prováděna z [technického profilu ověření](validation-technical-profile.md) , který je volán pomocí [technického profilu s vlastním uplatněním](self-asserted-technical-profile.md). Metadata technického profilu **DateTimeGreaterThan** s vlastním uplatněním řídí chybovou zprávu, kterou poskytuje technický profil uživateli.

![Spuštění AssertStringClaimsAreEqual](./media/date-transformations/assert-execution.png)

Následující příklad porovnává deklaraci `currentDateTime` s deklarací `approvedDateTime`. Pokud je `currentDateTime` pozdější než `approvedDateTime`, je vyvolána chyba. Transformace považuje hodnoty za shodné, pokud jsou během 5 minut (30000 milisekund) rozdíl.

```XML
<ClaimsTransformation Id="AssertApprovedDateTimeLaterThanCurrentDateTime" TransformationMethod="AssertDateTimeIsGreaterThan">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="approvedDateTime" TransformationClaimType="leftOperand" />
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="rightOperand" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
    <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
    <InputParameter Id="TreatAsEqualIfWithinMillseconds" DataType="int" Value="300000" />
  </InputParameters>
</ClaimsTransformation>
```

Technický profil ověření `login-NonInteractive` volá transformaci deklarací `AssertApprovedDateTimeLaterThanCurrentDateTime`.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Technický profil s vlastním uplatněním volá ověřovací **přihlášení – neinteraktivní** technický profil.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="DateTimeGreaterThan">Custom error message if the provided left operand is greater than the right operand.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **leftOperand**: 2018-10-01T15:00:00.0000000 z
    - **rightOperand**: 2018-10-01T14:00:00.0000000 z
- Výsledek: došlo k chybě

## <a name="convertdatetodatetimeclaim"></a>ConvertDateToDateTimeClaim

Převede hodnotu vlastnosti **Date** ClaimType na **typ DateTime** . Transformace deklarací převede formát času a přidá 12:00:00 k datu.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | date | Deklarace ClaimType, která má být převedena. |
| outputClaim | outputClaim | Datum a čas | Deklarace ClaimType, která je vytvořena po vyvolání tohoto ClaimsTransformation. |

Následující příklad ukazuje převod `dateOfBirth` deklarace identity (datový typ Date) na jiný `dateOfBirthWithTime` deklarací identity (datový typ dateTime).

```XML
  <ClaimsTransformation Id="ConvertToDateTime" TransformationMethod="ConvertDateToDateTimeClaim">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="dateOfBirth" TransformationClaimType="inputClaim" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="dateOfBirthWithTime" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
```

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **inputClaim**: 2019-06-01
- Deklarace výstupů:
    - **outputClaim**: 1559347200 (1. června 2019 12:00:00 am)

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

Získejte aktuální datum a čas UTC a přidejte hodnotu do ClaimType.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| outputClaim | currentDateTime | Datum a čas | Deklarace ClaimType, která je vytvořena po vyvolání tohoto ClaimsTransformation. |

```XML
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad:

* Deklarace výstupů:
    * **currentDateTime**: 1534418820 (16. srpna 2018 11:27:00 am)

## <a name="datetimecomparison"></a>DateTimeComparison

Určete, zda je jedno datum a čas pozdější, dřívější nebo stejné jako jiné. Výsledkem je nová logická logická hodnota ClaimType s hodnotou `true` nebo `false`.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | firstDateTime | Datum a čas | První hodnota dateTime pro porovnání, zda je dřívější nebo pozdější než druhá hodnota dateTime. Hodnota null vyvolá výjimku. |
| InputClaim | secondDateTime | Datum a čas | Druhý typ dateTime pro porovnání, zda je dřívější nebo pozdější než první datum a čas. Hodnota null se považuje za aktuální datetTime. |
| InputParameter | operator | string | Jedna z následujících hodnot: totéž, později než nebo starší než. |
| InputParameter | timeSpanInSeconds | int | Přidejte časové rozpětí do prvního data a času. |
| outputClaim | výsledek | Boolean | Deklarace ClaimType, která je vytvořena po vyvolání tohoto ClaimsTransformation. |

Pomocí této transformace deklarací identity určíte, zda jsou dva ClaimTypes stejné, pozdější nebo dřívější. Můžete například uložit čas poslední přijetí podmínek služby (TOS) uživatelem. Po 3 měsících můžete požádat uživatele, aby znovu přistupují ke službě TOS.
Chcete-li spustit transformaci deklarace identity, musíte nejprve získat aktuální datum a čas a také čas, kdy uživatel akceptuje TOS.

```XML
<ClaimsTransformation Id="CompareLastTOSAcceptedWithCurrentDateTime" TransformationMethod="DateTimeComparison">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="firstDateTime" />
    <InputClaim ClaimTypeReferenceId="extension_LastTOSAccepted" TransformationClaimType="secondDateTime" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="later than" />
    <InputParameter Id="timeSpanInSeconds" DataType="int" Value="7776000" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="result" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **firstDateTime**: 2018-01-01T00:00:00.100000 z
    - **secondDateTime**: 2018-04-01T00:00:00.100000 z
- Vstupní parametry:
    - **operátor**: později než
    - **timeSpanInSeconds**: 7776000 (90 dní)
- Deklarace výstupů:
    - **výsledek**: pravda

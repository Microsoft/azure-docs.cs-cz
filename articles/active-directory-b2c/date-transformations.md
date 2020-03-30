---
title: Datum nároky transformace příklady pro vlastní zásady
description: Datum deklarace transformace příklady pro rozhraní Identity Experience Framework (IEF) schéma Služby Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c02ac9392d6f3f95deef38ff86250e96dfb76d96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476684"
---
# <a name="date-claims-transformations"></a>Datum nároky transformace

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek obsahuje příklady pro použití data deklarace transformace schématu identity experience framework v Azure Active Directory B2C (Azure AD B2C). Další informace naleznete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="assertdatetimeisgreaterthan"></a>AssertDateTimeisGreaterThan

Zkontroluje, že jedna deklarace data a času (datový typ řetězce) je pozdější než druhá deklarace data a času (datový typ řetězce) a vyvolá výjimku.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | vlevoOperand | řetězec | Typ první pohledávky, který by měl být pozdější než druhý nárok. |
| Vstupní deklarace | vpravoOperand | řetězec | Typ druhé pohledávky, který by měl být dřívější než první nárok. |
| Parametr Input | AssertIfEqualTo | Boolean | Určuje, zda má tento kontrolní výraz projít, pokud je levý operand rovna pravému operandu. |
| Parametr Input | AssertIfRightOperandIsNotPresent | Boolean | Určuje, zda má tento kontrolní výraz projít, pokud chybí pravý operand. |
| Parametr Input | TreatAsEqualIfVrámcimillsekundách | int | Určuje počet milisekund, které mají být mezi dvěma časy kalendářních dat zvažovány, aby se časy považovaly za stejné (například pro zkosení hodin). |

**AssertDateTimeIsGreaterThan** nároky transformace je vždy proveden z [ověření technického profilu,](validation-technical-profile.md) který je volán [samostatně uplatněný technický profil](self-asserted-technical-profile.md). **DateTimeGreaterThan** self-tvrdil, metadata technického profilu řídí chybovou zprávu, která představuje technický profil pro uživatele. Chybové zprávy lze [lokalizovat](localization-string-ids.md#claims-transformations-error-messages).

![AssertStringClaimsAreEqual provádění](./media/date-transformations/assert-execution.png)

Následující příklad porovnává `currentDateTime` deklaraci `approvedDateTime` s deklarací. Je vyvolána chyba, `currentDateTime` pokud `approvedDateTime`je pozdější než . Transformace zachází s hodnotami jako stejné, pokud jsou v rámci 5 minut (30000 milisekund) rozdíl.

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

Technický `login-NonInteractive` profil ověření `AssertApprovedDateTimeLaterThanCurrentDateTime` volá transformaci deklarací identity.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Samoobslužný technický profil volá ověření **login-NonInteractive** technický profil.

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

### <a name="example"></a>Příklad

- Vstupní deklarace:
    - **leftOperand**: 2020-03-01T15:00:00.0000000Z
    - **rightOperand**: 2020-03-01T14:00:00.0000000Z
- Výsledek: Vyvolána chyba

## <a name="convertdatetodatetimeclaim"></a>ConvertDateToDateTimeClaim

Převede **typ** claimtype data na typ claimtype **datetime.** Transformace deklarací převede formát času a přidá 12:00:00 k datu.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | inputClaim | date | ClaimType, které mají být převedeny. |
| Výstupní nárok | outputClaim | data a času. | ClaimType, který je vytvořen po této ClaimsTransformation byla vyvolána. |

Následující příklad ukazuje převod deklarace `dateOfBirth` pohledávky (datový typ `dateOfBirthWithTime` data) na jinou deklaraci (datový typ dateTime).

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

### <a name="example"></a>Příklad

- Vstupní deklarace:
    - **inputClaim**: 2020-15-03
- Výstupní nároky:
    - **outputClaim**: 2020-15-03T00:00:00.0000000Z

## <a name="convertdatetimetodateclaim"></a>ConvertDateTimeToDateClaim

Převede typ claimtype **datetime** na typ deklarace **data.** Transformace deklarací identity odebere formát času z data.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | inputClaim | data a času. | ClaimType, které mají být převedeny. |
| Výstupní nárok | outputClaim | date | ClaimType, který je vytvořen po této ClaimsTransformation byla vyvolána. |

Následující příklad ukazuje převod deklarace `systemDateTime` pohledávky (datový typ `systemDate` dateTime) na jinou deklaraci (datový typ data).

```XML
<ClaimsTransformation Id="ConvertToDate" TransformationMethod="ConvertDateTimeToDateClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDate" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

- Vstupní deklarace:
  - **inputClaim**: 2020-15-03T11:34:22.0000000Z
- Výstupní nároky:
  - **outputClaim**: 2020-15-03

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

Získejte aktuální datum a čas utc a přidejte hodnotu claimtype.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Výstupní nárok | currentDateTime | data a času. | ClaimType, který je vytvořen po této ClaimsTransformation byla vyvolána. |

```XML
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

* Výstupní nároky:
    * **currentDateTime**: 2020-15-03T11:40:35.0000000Z

## <a name="datetimecomparison"></a>DatumPorovnání času

Určete, zda jeden dateTime je později, dříve nebo rovno jinému. Výsledkem je nová logická hodnota ClaimType `true` s `false`hodnotou nebo .

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | firstDateTime | data a času. | První dateTime porovnat, zda je starší nebo novější než druhý dateTime. Hodnota Null vyvolá výjimku. |
| Vstupní deklarace | secondDateTime | data a času. | Druhý dateTime porovnat, zda je starší nebo novější než první dateTime. Hodnota Null je považována za aktuální datetTime. |
| Parametr Input |  – operátor | řetězec | Jedna z následujících hodnot: stejné, pozdější nebo dřívější než. |
| Parametr Input | timeSpanInSeconds | int | Přidejte časový klíč k prvnímu datu. |
| Výstupní nárok | result | Boolean | ClaimType, který je vytvořen po této ClaimsTransformation byla vyvolána. |

Pomocí této transformace deklarací určit, pokud dva ClaimTypes jsou stejné, novější nebo starší než ostatní. Můžete například uložit čas, kdy uživatel přijal vaše smluvní podmínky (TOS). Po 3 měsících můžete požádat uživatele o přístup k TOS znovu.
Chcete-li spustit transformaci deklarace, musíte nejprve získat aktuální dateTime a také poslední čas, kdy uživatel přijme TOS.

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

### <a name="example"></a>Příklad

- Vstupní deklarace:
    - **firstDateTime**: 2020-01-01T00:00:00.100000Z
    - **secondDateTime**: 2020-04-01T00:00:00.100000Z
- Vstupní parametry:
    - **operátor**: později než
    - **timeSpanInSeconds**: 7776000 (90 dní)
- Výstupní nároky:
    - **výsledek**: true

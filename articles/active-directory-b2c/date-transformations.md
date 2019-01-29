---
title: Datum deklaraci příklady transformaci identita prostředí Framework schéma z Azure Active Directory B2C | Dokumentace Microsoftu
description: Datum deklaraci příklady transformaci identita prostředí Framework schéma z Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 35c5731d260fb63dece3d2b8ae4f4a4522fb91b8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55153400"
---
# <a name="date-claims-transformations"></a>Datum deklarace identity transformace

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek obsahuje příklady použití transformace deklarací identity data schématu architekturu rozhraní identit v Azure Active Directory (Azure AD) B2C. Další informace najdete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="assertdatetimeisgreaterthan"></a>AssertDateTimeIsGreaterThan 

Kontroluje, že jeden datum a čas deklarace identity (datový typ string) je pozdější než druhý datum a čas deklarací (datový typ string) a vyvolá výjimku.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | leftOperand | řetězec | Typ první deklaraci identity, které by měly být pozdější než druhý deklarace identity. |
| InputClaim | rightOperand | řetězec | Druhý typ deklarace identity, který by měl být dřívější než první deklaraci identity. |
| InputParameter | AssertIfEqualTo | Boolean | Určuje, zda by měla předat Tento kontrolní výraz, pokud levý operand je rovna pravého operandu. |
| InputParameter | AssertIfRightOperandIsNotPresent | Boolean | Určuje, zda by měla předat Tento kontrolní výraz, pokud chybí pravého operandu. |
| InputParameter | TreatAsEqualIfWithinMillseconds | int | Určuje počet milisekund, aby mezi těmito dvěma data a času vzít v úvahu dobu je rovno (například účet pro posun hodin). |

**AssertDateTimeIsGreaterThan** transformaci deklarací identity je vždy spuštěn z [technický profil ověření](validation-technical-profile.md) , který je volán [držitelem s prohlašovanou technický profil](self-asserted-technical-profile.md). **DateTimeGreaterThan** technického profilu s vlastním potvrzením určuje chybovou zprávu, která se uživateli zobrazí technický profil.

![AssertStringClaimsAreEqual execution](./media/date-transformations/assert-execution.png)

Následující příklad porovnává `currentDateTime` deklarace identity s `approvedDateTime` deklarací identity. Pokud je vyvolána chyba `currentDateTime` je pozdější než `approvedDateTime`. Transformace považuje za hodnoty rovny, pokud jsou v rámci rozdíl (30000 milisekund) 5 minut.

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

`login-NonInteractive` Volání technický profil ověření `AssertApprovedDateTimeLaterThanCurrentDateTime` transformace deklarací identity.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Volání ověření s vlastním potvrzením technický profil **neinteraktivní přihlášení** technický profil.

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
    - **leftOperand**: 2018-10-01T15:00:00.0000000Z
    - **rightOperand**: 2018-10-01T14:00:00.0000000Z
- Výsledek: Vyvolána chyba


## <a name="convertdatetodatetimeclaim"></a>ConvertDateToDateTimeClaim

Převede **datum** typu deklarace identity k **data a času** typu deklarace identity. Transformace deklarací identity převede formát času a přičte 12:00:00 AM k datu.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | date | Typ ClaimType má být převeden. |
| outputClaim | outputClaim | Datum a čas | Typ ClaimType, který je vytvořen po zavolání této ClaimsTransformation. |

Následující příklad ukazuje převod deklarace `dateOfBirth` (datový typ datum) do jiného deklarací `dateOfBirthWithTime` (datový typ dateTime).

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
- Výstupní deklarace identit:
    - **outputClaim**: 1559347200 (1 dne. května 2019 12:00:00 AM)

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

Získejte aktuální datum a čas UTC a přidá hodnotu typu deklarace identity.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| outputClaim | currentDateTime | Datum a čas | Typ ClaimType, který je vytvořen po zavolání této ClaimsTransformation. |

```XML
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad:

* Výstupní deklarace identit:
    * **currentDateTime**: 1534418820 (16 dne. května 2018 11:27:00 dop.)

## <a name="datetimecomparison"></a>DateTimeComparison

Zjistit, zda jeden data a času je později, dřívější nebo roven jinému. Výsledkem je nová logická hodnota boolean typu deklarace identity s hodnotou `true` nebo `false`.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | firstDateTime | Datum a čas | První datum a čas pro porovnání, ať už jde o dřívější nebo pozdější než druhý data a času. Hodnota Null, dojde k výjimce. |
| InputClaim | secondDateTime | Datum a čas | Druhý datum a čas pro porovnání, ať už jde o dřívější nebo pozdější než první datum a čas. Aktuální datetTime je považován za hodnotu Null. |
| InputParameter | – Operátor | řetězec | Jeden z následujících hodnot: stejné, novější než nebo dřívější. |
| InputParameter | timeSpanInSeconds | int | Přidáte časový interval pro první datum a čas. |
| outputClaim | výsledek | Boolean | Typ ClaimType, který je vytvořen po zavolání této ClaimsTransformation. |

Použijte tato deklarace identity transformace, která určí, zda dva ClaimTypes jsou stejné, novější nebo starší než mezi sebou. Například může ukládat posledního uživatel přijal vaše podmínky služby (TOS). Po 3 měsících můžete požádat uživatele o přístup TOS znovu.
Ke spuštění transformace deklarací identity, musíte nejprve získat aktuální datum a čas a také poslední čas uživatel přijme podmínky telekomunikací.

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
    - **firstDateTime**: 2018-01-01T00:00:00.100000Z
    - **secondDateTime**: 2018-04-01T00:00:00.100000Z
- Vstupní parametry:
    - **operátor**: později než
    - **timeSpanInSeconds**: 7776000 (90 dnů)
- Výstupní deklarace identit: 
    - **výsledek**: true


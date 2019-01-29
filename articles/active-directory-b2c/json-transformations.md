---
title: JSON deklaraci příklady transformaci identita prostředí Framework schéma z Azure Active Directory B2C | Dokumentace Microsoftu
description: JSON deklaraci příklady transformaci identita prostředí Framework schéma z Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 67cda814925c3e5b974e4580594724c890851f64
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55176197"
---
# <a name="json-claims-transformations"></a>JSON deklarace identity transformace

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek obsahuje příklady použití transformace deklarací JSON schématu architekturu rozhraní identit v Azure Active Directory (Azure AD) B2C. Další informace najdete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="getclaimfromjson"></a>GetClaimFromJson

Získejte Zadaný prvek z dat JSON.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | řetězec | ClaimTypes, které jsou používány transformace deklarací identity k získání položky. |
| InputParameter | claimToExtract | řetězec | Název elementu JSON, který chcete extrahovat. |
| outputClaim | extractedClaim | řetězec | Zavolání typu deklarace identity, který je vytvořen po to transformace deklarací identity v zadaná hodnota elementu _claimToExtract_ vstupního parametru. |

V následujícím příkladu se extrahují transformace deklarací identity `emailAddress` element z JSON data: `{"emailAddress": "someone@example.com", "displayName": "Someone"}`

```XML
<ClaimsTransformation Id="GetEmailClaimFromJson" TransformationMethod="GetClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="customUserData" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="emailAddress" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="extractedEmail" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **inputJson**: {"email": "someone@example.com", "displayName": "Uživatel"}
- Vstupní parametr:
    - **claimToExtract**: emailAddress
- Výstupní deklarace identit: 
    - **extractedClaim**: someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

Získání seznamu sad zadané elementy z dat Json.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | jsonSourceClaim | řetězec | ClaimTypes, které používají deklarace identity transformace získat deklarace identity. |
| InputParameter | errorOnMissingClaims | Boolean | Určuje, jestli se má vyvolat chybu, pokud chybí jedna z deklarací. |
| InputParameter | includeEmptyClaims | řetězec | Určete, zda obsahují prázdné deklarace. |
| InputParameter | jsonSourceKeyName | řetězec | Název klíče – element |
| InputParameter | jsonSourceValueName | řetězec | Hodnota názvu elementu |
| outputClaim | Kolekce | řetězec, int, logická hodnota a datum a čas |Seznam deklarací identity k extrakci. Název deklarace by měl být roven zadanému v _jsonSourceClaim_ vstupní deklaraci identity. |

V následujícím příkladu transformace deklarací identity extrahuje následující deklarace: e-mailu (řetězec), displayName (řetězec), membershipNum (int), aktivní (logická hodnota) a datum narození (datetime) z dat JSON.

```JSON
[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
```

```XML
<ClaimsTransformation Id="GetClaimsFromJson" TransformationMethod="GetClaimsFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="jsonSourceClaim" TransformationClaimType="jsonSource" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="errorOnMissingClaims" DataType="boolean" Value="false" />
    <InputParameter Id="includeEmptyClaims" DataType="boolean" Value="false" />
    <InputParameter Id="jsonSourceKeyName" DataType="string" Value="key" />
    <InputParameter Id="jsonSourceValueName" DataType="string" Value="value" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="membershipNum" />
    <OutputClaim ClaimTypeReferenceId="active" />
    <OutputClaim ClaimTypeReferenceId="birthdate" />
  </OutputClaims>
</ClaimsTransformation>
```    

- Vstupní deklarace identity:
    - **jsonSourceClaim**: [{"klíče": "email", "value": "someone@example.com"}, {"klíče": "displayName", "value": "Uživatel"}, {"klíče": "membershipNum", "value": 6353399}, {"klíče": "aktivní", "value": true}, {"klíče": "datum narození", "value": "1980-09-23T00:0 [[[0:00Z"}]
- Vstupní parametry:
    - **errorOnMissingClaims**: false
    - **includeEmptyClaims**: false
    - **jsonSourceKeyName**: key
    - **jsonSourceValueName**: hodnota
- Výstupní deklarace identit:
    - **e-mailu**: "someone@example.com"
    - **displayName**: "Uživatel"
    - **membershipNum**: 6353399
    - **aktivní**: true
    - **Datum narození**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Získá Zadaný prvek číselné (dlouhé) z dat JSON.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | řetězec | ClaimTypes, které používají deklarace identity transformace získat deklarace identity. |
| InputParameter | claimToExtract | řetězec | Název elementu JSON, který chcete extrahovat. |
| outputClaim | extractedClaim | Long | Typ ClaimType, který je vytvořen po zavolání této ClaimsTransformation, název elementu zadali v _claimToExtract_ vstupní parametry. |

V následujícím příkladu, extrahuje transformace deklarací identity `id` element z dat JSON.

```JSON
{
    "emailAddress": "someone@example.com", 
    "displayName": "Someone", 
    "id" : 6353399
}
```

```XML
<ClaimsTransformation Id="GetIdFromResponse" TransformationMethod="GetNumericClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="exampleInputClaim" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="id" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="membershipId" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **inputJson**: {"email": "someone@example.com", "displayName": "Uživatel", "id": 6353399}
- Vstupní parametry
    - **claimToExtract**: id
- Výstupní deklarace identit: 
    - **extractedClaim**: 6353399

## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Získá první prvek z pole data JSON.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJsonClaim | řetězec | ClaimTypes, které jsou používány transformace deklarací identity k získání položky z pole JSON. |
| outputClaim | extractedClaim | řetězec | Typ ClaimType, který je vytvořen po zavolání této ClaimsTransformation, první prvek v poli JSON. |

V následujícím příkladu transformace deklarací identity extrahuje první prvek (e-mailovou adresu) z pole JSON `["someone@example.com", "Someone", 6353399]`.

```XML
<ClaimsTransformation Id="GetEmailFromJson" TransformationMethod="GetSingleValueFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userData" TransformationClaimType="inputJsonClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad:

- Vstupní deklarace identity:
    - **inputJsonClaim**: ["someone@example.com", "Uživatel", 6353399]
- Výstupní deklarace identit: 
    - **extractedClaim**: someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

Převede XML data do formátu JSON.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | xml | řetězec | ClaimTypes, které používají deklarace identity transformace převést data ze souboru XML do formátu JSON. |
| outputClaim | json | řetězec | Typ ClaimType, který je vytvořen po zavolání této ClaimsTransformation, data ve formátu JSON. |

```XML
<ClaimsTransformation Id="ConvertXmlToJson" TransformationMethod="XmlStringToJsonString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="intpuXML" TransformationClaimType="xml" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="outputJson" TransformationClaimType="json" />
  </OutputClaims>
</ClaimsTransformation>
```

Transformace deklarací identity v následujícím příkladu se převede následující data XML do formátu JSON.

#### <a name="example"></a>Příklad:
Vstupní deklaraci identity:

```XML
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Výstupní deklarací:

```JSON
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```


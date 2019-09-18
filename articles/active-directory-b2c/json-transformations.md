---
title: Příklady transformací deklarací JSON pro schéma architektury prostředí identity Azure Active Directory B2C | Microsoft Docs
description: Příklady transformací deklarací JSON pro schéma rozhraní Azure Active Directory B2C prostředí identity
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ff70b2f54304c83f70ff578e1947d752aafb34a7
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064167"
---
# <a name="json-claims-transformations"></a>Transformace deklarací JSON

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek popisuje příklady použití transformací deklarací JSON schématu rozhraní identity Experience schématu v Azure Active Directory B2C (Azure AD B2C). Další informace najdete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="getclaimfromjson"></a>GetClaimFromJson

Získá zadaný element z dat JSON.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | řetězec | ClaimTypes, které jsou používány transformací deklarací k získání položky. |
| InputParameter | claimToExtract | řetězec | název elementu JSON, který se má extrahovat. |
| OutputClaim | extractedClaim | řetězec | Deklarace ClaimType, která je vytvořena po vyvolání této transformace deklarací, hodnota prvku zadaná ve vstupním parametru _claimToExtract_ . |

V následujícím příkladu transformace deklarací extrakci `emailAddress` prvek z dat JSON:`{"emailAddress": "someone@example.com", "displayName": "Someone"}`

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

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
  - **inputJson**: {"EmailAddress": "someone@example.com", "DisplayName": "Někdo"}
- Vstupní parametr:
    - **claimToExtract**: EmailAddress
- Deklarace výstupů:
  - **extractedClaim**:someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

Získá seznam zadaných elementů z dat JSON.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | jsonSourceClaim | řetězec | ClaimTypes, které jsou používány transformací deklarací k získání deklarací identity. |
| InputParameter | errorOnMissingClaims | boolean | Určuje, zda má být vyvolána chyba, pokud chybí jedna z deklarací. |
| InputParameter | includeEmptyClaims | řetězec | Určete, jestli se mají zahrnout prázdné deklarace identity. |
| InputParameter | jsonSourceKeyName | řetězec | Název klíče elementu |
| InputParameter | jsonSourceValueName | řetězec | Název hodnoty prvku |
| OutputClaim | Collection | String, int, Boolean a DateTime |Seznam deklarací, které se mají extrahovat Název deklarace identity by měl být roven hodnotě zadané ve vstupní deklaraci _jsonSourceClaim_ . |

V následujícím příkladu transformace deklarací extrahuje následující deklarace: e-mail (String), DisplayName (String), membershipNum (int), Active (Boolean) a DatumNarození (DateTime) z dat JSON.

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
  - **jsonSourceClaim**: [{"klíče": "email", "value": "someone@example.com"}, {"klíče": "displayName", "value": "Uživatel"}, {"klíče": "membershipNum", "value": 6353399}, {"klíče": "aktivní", "value": true}, {"klíče": "datum narození", "value": "1980-09-23T00:0 0:00Z"}]
- Vstupní parametry:
    - **errorOnMissingClaims**: false
    - **includeEmptyClaims**: false
    - **jsonSourceKeyName**: klíč
    - **jsonSourceValueName**: hodnota
- Deklarace výstupů:
  - **e-mail**: "someone@example.com"
  - **displayName**: Uživatelé
  - **membershipNum**: 6353399
  - **aktivní**: true
  - **Datum narození**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Získá zadaný numerický (dlouhý) prvek z dat JSON.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | řetězec | ClaimTypes, který se používá při transformaci deklarací k získání deklarace identity. |
| InputParameter | claimToExtract | řetězec | Název elementu JSON, který se má extrahovat. |
| OutputClaim | extractedClaim | long | Deklarace ClaimType, která je vytvořena po vyvolání tohoto ClaimsTransformationu, hodnota elementu zadaná ve vstupních parametrech _claimToExtract_ . |

V následujícím příkladu transformace deklarací extrahuje `id` prvek z dat JSON.

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

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
  - **inputJson**: {"EmailAddress": "someone@example.com", "DisplayName": "Někdo", "ID": 6353399}
- Vstupní parametry
    - **claimToExtract**: ID
- Deklarace výstupů:
    - **extractedClaim**: 6353399

## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Získá první prvek z datového pole JSON.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJsonClaim | řetězec | ClaimTypes, který se používá při transformaci deklarací k získání položky z pole JSON. |
| OutputClaim | extractedClaim | řetězec | Deklarace ClaimType, která je vytvořena po vyvolání tohoto ClaimsTransformation, první prvek v poli JSON. |

V následujícím příkladu transformace deklarací extrahuje první prvek (e-mailová adresa) z pole `["someone@example.com", "Someone", 6353399]`JSON.

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

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
  - **inputJsonClaim**: ["someone@example.com", "někoho", 6353399]
- Deklarace výstupů:
  - **extractedClaim**:someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

Převede data XML do formátu JSON.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | xml | řetězec | ClaimTypes, které jsou používány transformací deklarací k převodu dat z formátu XML na formát JSON. |
| OutputClaim | json | řetězec | Deklarace ClaimType, která je vytvořena po vyvolání tohoto ClaimsTransformationu, data ve formátu JSON. |

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

V následujícím příkladu transformace deklarací převede následující data XML do formátu JSON.

#### <a name="example"></a>Příklad
Vstupní deklarace identity:

```XML
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Výstupní deklarace identity:

```JSON
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```


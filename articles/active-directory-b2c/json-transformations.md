---
title: JSON nároky transformace příklady pro vlastní zásady
titleSuffix: Azure AD B2C
description: JSON nároky příklady transformace pro rozhraní Identity Experience Framework (IEF) schéma Služby Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ad8fcf578ae1c89856a9d7929af0aec813cb4082
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187589"
---
# <a name="json-claims-transformations"></a>JSON nároky transformace

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek obsahuje příklady pro použití JSON deklarace identity schématu rozhraní identity rozhraní ve službě Azure Active Directory B2C (Azure AD B2C). Další informace naleznete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="generatejson"></a>GenerovatJson

Ke generování řetězce JSON použijte hodnoty deklarací nebo konstanty. Řetězec cesty následující tečka zápisu se používá k označení, kam vložit data do řetězce JSON. Po rozdělení podle tets jsou všechna celá čísla interpretována jako index pole JSON a necelá čísla jsou interpretována jako index objektu JSON.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | Libovolný řetězec následující tečka zápisu | řetězec | JsonPath json, kde bude vložena hodnota deklarace. |
| Parametr Input | Libovolný řetězec následující tečka zápisu | řetězec | JsonPath json, kde bude vložena hodnota konstantní řetězec do. |
| Výstupní nárok | outputClaim | řetězec | Vygenerovaný řetězec JSON. |

Následující příklad generuje řetězec JSON na základě deklarace hodnoty "e-mail" a "otp" stejně jako konstantní řetězce.

```XML
<ClaimsTransformation Id="GenerateRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="template_id" DataType="string" Value="d-4c56ffb40fa648b1aa6822283df94f60"/>
    <InputParameter Id="from.email" DataType="string" Value="service@contoso.com"/>
    <InputParameter Id="personalizations.0.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="requestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

Následující deklarace identity transformace výstupy deklarace řetězce JSON, který bude tělo požadavku odeslaného SendGrid (zprostředkovatel e-mailu třetí strany). Struktura objektu JSON je definována ID v dot notaci InputParameters a TransformationClaimTypes InputClaims. Čísla v dot notaci implikují pole. Hodnoty pocházejí z inputclaims 'hodnoty a InputParameters '"Hodnota" vlastnosti.

- Vstupní pohledávky :
  - **e-mail**, transformace nárok **personalizace.0.to.0.email**: "someone@example.com
  - **otp**, transformace tvrzení typu **personalizations.0.dynamic_template_data.otp** "346349"
- Vstupní parametr:
  - **template_id**: "d-4c56ffb40fa648b1aa6822283df94f60"
  - **from.email**:service@contoso.com"
  - **personalizations.0.subject** "Ověřovací kód e-mailu účtu Společnosti Contoso"
- Výstupní nárok:
  - **requestBody**: Hodnota JSON

```JSON
{
  "personalizations": [
    {
      "to": [
        {
          "email": "someone@example.com"
        }
      ],
      "dynamic_template_data": {
        "otp": "346349",
        "verify-email" : "someone@example.com"
      },
      "subject": "Contoso account email verification code"
    }
  ],
  "template_id": "d-989077fbba9746e89f3f6411f596fb96",
  "from": {
    "email": "service@contoso.com"
  }
}
```

## <a name="getclaimfromjson"></a>GetClaimFromJson

Získejte zadaný prvek z dat JSON.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | inputJson | řetězec | ClaimTypes, které jsou používány transformace deklarací získat položku. |
| Parametr Input | claimToExtract | řetězec | název prvku JSON, který má být extrahován. |
| Výstupní nárok | extrahovaný nárok | řetězec | ClaimType, který je vytvořen po této deklarace transformace byla vyvolána, hodnota prvku zadaná ve vstupním parametru _claimToExtract._ |

V následujícím příkladu transformace deklarací `emailAddress` extrahuje prvek z dat JSON:`{"emailAddress": "someone@example.com", "displayName": "Someone"}`

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

- Vstupní deklarace:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Někdo"}
- Vstupní parametr:
    - **claimToExtract**: emailAddress
- Výstupní nároky:
  - **extrahovaný nárok**:someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

Získejte seznam zadaných prvků z dat Json.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | jsonSourceClaim | řetězec | ClaimTypes, které jsou používány transformace deklarací získat deklarace identity. |
| Parametr Input | errorOnMissingClaims | Boolean | Určuje, zda má být chyba vyvolána, pokud chybí jeden z deklarací identity. |
| Parametr Input | includeEmptyClaims | řetězec | Určete, zda mají být zahrnuty prázdné deklarace identity. |
| Parametr Input | jsonSourceKeyName | řetězec | Název klíče prvku |
| Parametr Input | jsonSourceValueName | řetězec | Název hodnoty prvku |
| Výstupní nárok | Kolekce | řetězec, int, logická data a datetime |Seznam nároků na výpis. Název deklarace by měl být roven názvu zadanému ve vstupní deklaraci _jsonSourceClaim._ |

V následujícím příkladu deklarace transformace extrahuje následující deklarace identity: e-mail (řetězec), displayName (řetězec), membershipNum (int), aktivní (boolean) a datum narození (datetime) z dat JSON.

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

- Vstupní deklarace:
  - **jsonSourceClaim**: [{"key":"email","value":"},someone@example.com{"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00:00
- Vstupní parametry:
    - **errorOnMissingClaims**: false
    - **includeEmptyClaims**: false
    - **jsonSourceKeyName**: klíč
    - **jsonSourceValueName**: hodnota
- Výstupní nároky:
  - **e-mail**: "someone@example.com"
  - **displayName**: "Někdo"
  - **členstvíNum**: 6353399
  - **aktivní**: true
  - **datum narození**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Získá zadaný číselný (dlouhý) prvek z dat JSON.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | inputJson | řetězec | ClaimTypes, které jsou používány transformace deklarací získat deklarace identity. |
| Parametr Input | claimToExtract | řetězec | Název prvku JSON extrahovat. |
| Výstupní nárok | extrahovaný nárok | long | ClaimType, který je vytvořen po této ClaimsTransformation byl vyvolán, hodnota prvku zadaná v _claimToExtract_ vstupní parametry. |

V následujícím příkladu deklarace transformace `id` extrahuje prvek z dat JSON.

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

- Vstupní deklarace:
  - **inputJson**: {"emailAddress": " ",someone@example.com"displayName": "Někdo", "id" : 6353399}
- Vstupní parametry
    - **claimToExtract**: id
- Výstupní nároky:
    - **extrahovaný nárok**: 6353399

## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Získá první prvek z datového pole JSON.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | inputSonClaim | řetězec | ClaimTypes, které jsou používány transformace deklarací získat položku z pole JSON. |
| Výstupní nárok | extrahovaný nárok | řetězec | ClaimType, který je vytvořen po této ClaimsTransformation byl vyvolán, první prvek v poli JSON. |

V následujícím příkladu deklarace identity transformace extrahuje první prvek `["someone@example.com", "Someone", 6353399]`(e-mailovou adresu) z pole JSON .

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

- Vstupní deklarace:
  - **inputJsonClaim**:someone@example.com[" ", "Někdo", 6353399]
- Výstupní nároky:
  - **extrahovaný nárok**:someone@example.com

## <a name="xmlstringtojsonstring"></a>Řetězec XmlStringToJsonString

Převede data XML do formátu JSON.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | xml | řetězec | ClaimTypes, které jsou používány transformace deklarací převést data z XML do formátu JSON. |
| Výstupní nárok | json | řetězec | ClaimType, který je vytvořen po této ClaimsTransformation byla vyvolána, data ve formátu JSON. |

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
Vstupní deklarace:

```XML
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Výstupní nárok:

```JSON
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```

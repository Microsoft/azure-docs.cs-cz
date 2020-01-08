---
title: Příklady transformací deklarací JSON pro vlastní zásady
titleSuffix: Azure AD B2C
description: Příklady transformací deklarací JSON pro schéma IEF (identity Experience Framework) Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 56c46b8f2804e37544c94ec2d6ced7e8879b1ffa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75367123"
---
# <a name="json-claims-transformations"></a>Transformace deklarací JSON

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek popisuje příklady použití transformací deklarací JSON schématu rozhraní identity Experience schématu v Azure Active Directory B2C (Azure AD B2C). Další informace najdete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="generatejson"></a>GenerateJson

K vygenerování řetězce JSON použijte buď hodnoty deklarace identity, nebo konstanty. Řetězec cesty následující po zápisu tečky slouží k označení místa vložení dat do řetězce JSON. Po rozdělení tečkami jsou všechna celá čísla interpretována jako index pole JSON a jiná než celá čísla jsou interpretována jako index objektu JSON.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Libovolný řetězec za tečkami | string | JsonPath kódu JSON, do kterého bude vložena hodnota deklarace. |
| InputParameter | Libovolný řetězec za tečkami | string | JsonPath kódu JSON, do kterého bude vložena hodnota konstanty řetězce. |
| OutputClaim | outputClaim | string | Generovaný řetězec JSON. |

Následující příklad generuje řetězec JSON na základě hodnoty deklarace "e-mail" a "jednorázové heslo" a také konstantních řetězců.

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

### <a name="example"></a>Příklad:

Následující transformace deklarací identity vypíše deklaraci identity řetězce JSON, která bude textem žádosti odeslanou SendGrid (poskytovatel e-mailu třetí strany). Struktura objektu JSON je definována ID v desítkovém zápisu vstupními parametry a TransformationClaimTypesem InputClaims. Čísla v zápisu teček implikují pole. Hodnoty pocházejí z hodnot InputClaims a vstupní hodnoty vlastností value.

- Vstupní deklarace identity:
  - **e-mail**, transformace typu deklarace transformace **. 0. do. 0. e-mail**: "someone@example.com"
  - **jednorázové heslo**a přizpůsobení typu deklarace transformace **. 0. dynamic_template_data. jednorázové heslo** "346349"
- Vstupní parametr:
  - **template_id**: "d-4c56ffb40fa648b1aa6822283df94f60"
  - **z. e-mail**: "service@contoso.com"
  - **přizpůsobení. 0. Subject** "kód pro ověření e-mailu účtu contoso"
- Výstupní deklarace identity:
  - **částmi**: hodnota JSON

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

Získá zadaný element z dat JSON.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | ClaimTypes, které jsou používány transformací deklarací k získání položky. |
| InputParameter | claimToExtract | string | název elementu JSON, který se má extrahovat. |
| OutputClaim | extractedClaim | string | Deklarace ClaimType, která je vytvořena po vyvolání této transformace deklarací, hodnota prvku zadaná ve vstupním parametru _claimToExtract_ . |

V následujícím příkladu transformace deklarací extrahuje prvek `emailAddress` z dat JSON: `{"emailAddress": "someone@example.com", "displayName": "Someone"}`

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
  - **inputJson**: {"EmailAddress": "someone@example.com"; "DisplayName": "někdo"}
- Vstupní parametr:
    - **claimToExtract**: EmailAddress
- Deklarace výstupů:
  - **extractedClaim**: someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

Získá seznam zadaných elementů z dat JSON.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | jsonSourceClaim | string | ClaimTypes, které jsou používány transformací deklarací k získání deklarací identity. |
| InputParameter | errorOnMissingClaims | Boolean | Určuje, zda má být vyvolána chyba, pokud chybí jedna z deklarací. |
| InputParameter | includeEmptyClaims | string | Určete, jestli se mají zahrnout prázdné deklarace identity. |
| InputParameter | jsonSourceKeyName | string | Název klíče elementu |
| InputParameter | jsonSourceValueName | string | Název hodnoty prvku |
| OutputClaim | Kolekce | String, int, Boolean a DateTime |Seznam deklarací, které se mají extrahovat Název deklarace identity by měl být roven hodnotě zadané ve vstupní deklaraci _jsonSourceClaim_ . |

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
  - **Zobrazovaný**název: "někdo"
  - **membershipNum**: 6353399
  - **aktivní**: true
  - **DatumNarození**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Získá zadaný numerický (dlouhý) prvek z dat JSON.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | ClaimTypes, který se používá při transformaci deklarací k získání deklarace identity. |
| InputParameter | claimToExtract | string | Název elementu JSON, který se má extrahovat. |
| OutputClaim | extractedClaim | Long | Deklarace ClaimType, která je vytvořena po vyvolání tohoto ClaimsTransformationu, hodnota elementu zadaná ve vstupních parametrech _claimToExtract_ . |

V následujícím příkladu transformace deklarací extrahuje prvek `id` z dat JSON.

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
  - **inputJson**: {"EmailAddress": "someone@example.com"; "DisplayName": "někdo", "ID": 6353399}
- Vstupní parametry
    - **claimToExtract**: ID
- Deklarace výstupů:
    - **extractedClaim**: 6353399

## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Získá první prvek z datového pole JSON.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJsonClaim | string | ClaimTypes, který se používá při transformaci deklarací k získání položky z pole JSON. |
| OutputClaim | extractedClaim | string | Deklarace ClaimType, která je vytvořena po vyvolání tohoto ClaimsTransformation, první prvek v poli JSON. |

V následujícím příkladu transformace deklarací extrahuje první prvek (e-mailová adresa) z pole JSON `["someone@example.com", "Someone", 6353399]`.

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
  - **inputJsonClaim**: ["someone@example.com"; "někoho", 6353399]
- Deklarace výstupů:
  - **extractedClaim**: someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

Převede data XML do formátu JSON.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | xml | string | ClaimTypes, které jsou používány transformací deklarací k převodu dat z formátu XML na formát JSON. |
| OutputClaim | json | string | Deklarace ClaimType, která je vytvořena po vyvolání tohoto ClaimsTransformationu, data ve formátu JSON. |

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

#### <a name="example"></a>Příklad:
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

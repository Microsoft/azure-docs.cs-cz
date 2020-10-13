---
title: Příklady transformací deklarací JSON pro vlastní zásady
titleSuffix: Azure AD B2C
description: Příklady transformací deklarací JSON pro schéma IEF (identity Experience Framework) Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 676b6abb28abf58287bfc9036ca907ae6a1ee192
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961285"
---
# <a name="json-claims-transformations"></a>Transformace deklarací JSON

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek popisuje příklady použití transformací deklarací JSON schématu rozhraní identity Experience schématu v Azure Active Directory B2C (Azure AD B2C). Další informace najdete v tématu [ClaimsTransformations](claimstransformations.md).

## <a name="generatejson"></a>GenerateJson

K vygenerování řetězce JSON použijte buď hodnoty deklarace identity, nebo konstanty. Řetězec cesty následující po zápisu tečky slouží k označení místa vložení dat do řetězce JSON. Po rozdělení tečkami jsou všechna celá čísla interpretována jako index pole JSON a jiná než celá čísla jsou interpretována jako index objektu JSON.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Libovolný řetězec za tečkami | řetězec | JsonPath kódu JSON, do kterého bude vložena hodnota deklarace. |
| InputParameter | Libovolný řetězec za tečkami | řetězec | JsonPath kódu JSON, do kterého bude vložena hodnota konstanty řetězce. |
| OutputClaim | outputClaim | řetězec | Generovaný řetězec JSON. |

### <a name="example-1"></a>Příklad 1

Následující příklad generuje řetězec JSON na základě hodnoty deklarace "e-mail" a "jednorázové heslo" a také konstantních řetězců.

```xml
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

Následující transformace deklarací identity vypíše deklaraci identity řetězce JSON, která bude textem žádosti odeslanou SendGrid (poskytovatel e-mailu třetí strany). Struktura objektu JSON je definována ID v desítkovém zápisu vstupními parametry a TransformationClaimTypesem InputClaims. Čísla v zápisu teček implikují pole. Hodnoty pocházejí z hodnot InputClaims a vstupní hodnoty vlastností value.

- Vstupní deklarace identity:
  - **e-mail**a přizpůsobení typu deklarace transformace **. 0. na. 0. e-mail**: someone@example.com
  - **jednorázové heslo**, typ deklarace transformace **personalizations.0.dynamic_template_data. jednorázové heslo** "346349"
- Vstupní parametr:
  - **template_id**: "d-4c56ffb40fa648b1aa6822283df94f60"
  - **z. e-mail**: " service@contoso.com "
  - **přizpůsobení. 0. Subject** "kód pro ověření e-mailu účtu contoso"
- Výstupní deklarace identity:
  - **částmi**: hodnota JSON

```json
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

### <a name="example-2"></a>Příklad 2

Následující příklad generuje řetězec JSON na základě hodnot deklarace identity a také konstantních řetězců.

```xml
<ClaimsTransformation Id="GenerateRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="customerEntity.email" />
    <InputClaim ClaimTypeReferenceId="objectId" TransformationClaimType="customerEntity.userObjectId" />
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="customerEntity.firstName" />
    <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="customerEntity.lastName" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="customerEntity.role.name" DataType="string" Value="Administrator"/>
    <InputParameter Id="customerEntity.role.id" DataType="long" Value="1"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="requestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

Následující transformace deklarací identity vypíše deklaraci identity řetězce JSON, která bude textem žádosti odeslanou REST API. Struktura objektu JSON je definována ID v desítkovém zápisu vstupními parametry a TransformationClaimTypesem InputClaims. Čísla v zápisu teček implikují pole. Hodnoty pocházejí z hodnot InputClaims a vstupní hodnoty vlastností value.

- Vstupní deklarace identity:
  - **e-mail**, typ deklarace transformace  **customerEntity. email**: " john.s@contoso.com "
  - **objectID**, transformovat typ deklarace identity **customerEntity. userObjectId** "01234567-89AB-CDEF-0123-456789ABCDEF"
  - **objectID**, transformovat typ deklarace identity **customerEntity. FirstName** "Jan"
  - **objectID**, transformovat typ deklarace identity **customerEntity. LastName** "Smith"
- Vstupní parametr:
  - **customerEntity.role.Name**: "správce"
  - **customerEntity.role.ID** 1
- Výstupní deklarace identity:
  - **částmi**: hodnota JSON

```json
{
   "customerEntity":{
      "email":"john.s@contoso.com",
      "userObjectId":"01234567-89ab-cdef-0123-456789abcdef",
      "firstName":"John",
      "lastName":"Smith",
      "role":{
         "name":"Administrator",
         "id": 1
      }
   }
}
```

## <a name="getclaimfromjson"></a>GetClaimFromJson

Získá zadaný element z dat JSON.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | řetězec | ClaimTypes, které jsou používány transformací deklarací k získání položky. |
| InputParameter | claimToExtract | řetězec | název elementu JSON, který se má extrahovat. |
| OutputClaim | extractedClaim | řetězec | Deklarace ClaimType, která je vytvořena po vyvolání této transformace deklarací, hodnota prvku zadaná ve vstupním parametru _claimToExtract_ . |

V následujícím příkladu transformace deklarací extrakci `emailAddress` prvek z dat JSON: `{"emailAddress": "someone@example.com", "displayName": "Someone"}`

```xml
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
  - **inputJson**: {"EmailAddress": " someone@example.com ", "DisplayName": "někdo"}
- Vstupní parametr:
    - **claimToExtract**: EmailAddress
- Deklarace výstupů:
  - **extractedClaim**: someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

Získá seznam zadaných elementů z dat JSON.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | jsonSourceClaim | řetězec | ClaimTypes, které jsou používány transformací deklarací k získání deklarací identity. |
| InputParameter | errorOnMissingClaims | boolean | Určuje, zda má být vyvolána chyba, pokud chybí jedna z deklarací. |
| InputParameter | includeEmptyClaims | řetězec | Určete, jestli se mají zahrnout prázdné deklarace identity. |
| InputParameter | jsonSourceKeyName | řetězec | Název klíče elementu |
| InputParameter | jsonSourceValueName | řetězec | Název hodnoty prvku |
| OutputClaim | Shromažďování | String, int, Boolean a DateTime |Seznam deklarací, které se mají extrahovat Název deklarace identity by měl být roven hodnotě zadané ve vstupní deklaraci _jsonSourceClaim_ . |

V následujícím příkladu transformace deklarací extrahuje následující deklarace: e-mail (String), DisplayName (String), membershipNum (int), Active (Boolean) a DatumNarození (DateTime) z dat JSON.

```json
[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
```

```xml
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
  - **jsonSourceClaim**: [{"Key": "e-mail"; "value": " someone@example.com "}; {"Key": "DisplayName", "value": "někdo"}, {"Key": "membershipNum"; "value": 6353399}; {"Key": "Active", "value": true}, {"Key": "DatumNarození", "value": "1980-09-23T00:00:00Z"}]
- Vstupní parametry:
    - **errorOnMissingClaims**: false
    - **includeEmptyClaims**: false
    - **jsonSourceKeyName**: klíč
    - **jsonSourceValueName**: hodnota
- Deklarace výstupů:
  - **e-mail**: " someone@example.com "
  - **Zobrazovaný**název: "někdo"
  - **membershipNum**: 6353399
  - **aktivní**: true
  - **DatumNarození**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Získá zadaný numerický (dlouhý) prvek z dat JSON.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | řetězec | ClaimTypes, který se používá při transformaci deklarací k získání deklarace identity. |
| InputParameter | claimToExtract | řetězec | Název elementu JSON, který se má extrahovat. |
| OutputClaim | extractedClaim | long | Deklarace ClaimType, která je vytvořena po vyvolání tohoto ClaimsTransformationu, hodnota elementu zadaná ve vstupních parametrech _claimToExtract_ . |

V následujícím příkladu transformace deklarací extrahuje `id` prvek z dat JSON.

```json
{
    "emailAddress": "someone@example.com",
    "displayName": "Someone",
    "id" : 6353399
}
```

```xml
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
  - **inputJson**: {"EmailAddress": " someone@example.com ", "DisplayName": "někdo", "id": 6353399}
- Vstupní parametry
    - **claimToExtract**: ID
- Deklarace výstupů:
    - **extractedClaim**: 6353399

## <a name="getsingleitemfromjson"></a>GetSingleItemFromJson

Získá první prvek z dat JSON.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | řetězec | ClaimTypes, který se používá při transformaci deklarací k získání položky z dat JSON. |
| OutputClaim | key | řetězec | První klíč elementu ve formátu JSON. |
| OutputClaim | hodnota | řetězec | Hodnota prvního prvku ve formátu JSON. |

V následujícím příkladu transformace deklarací extrahuje první prvek (daný název) z dat JSON.

```xml
<ClaimsTransformation Id="GetGivenNameFromResponse" TransformationMethod="GetSingleItemFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="json" TransformationClaimType="inputJson" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="givenNameKey" TransformationClaimType="key" />
    <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
  - **inputJson**: {"křestní jméno": "Emilty", "LastName": "Smith"}
- Deklarace výstupů:
  - **klíč**: křestní jméno
  - **hodnota**: Emilty


## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Získá první prvek z datového pole JSON.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJsonClaim | řetězec | ClaimTypes, který se používá při transformaci deklarací k získání položky z pole JSON. |
| OutputClaim | extractedClaim | řetězec | Deklarace ClaimType, která je vytvořena po vyvolání tohoto ClaimsTransformation, první prvek v poli JSON. |

V následujícím příkladu transformace deklarací extrahuje první prvek (e-mailová adresa) z pole JSON  `["someone@example.com", "Someone", 6353399]` .

```xml
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
  - **inputJsonClaim**: [" someone@example.com ", "někoho", 6353399]
- Deklarace výstupů:
  - **extractedClaim**: someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

Převede data XML do formátu JSON.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | xml | řetězec | ClaimTypes, které jsou používány transformací deklarací k převodu dat z formátu XML na formát JSON. |
| OutputClaim | json | řetězec | Deklarace ClaimType, která je vytvořena po vyvolání tohoto ClaimsTransformationu, data ve formátu JSON. |

```xml
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

```xml
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Výstupní deklarace identity:

```json
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```



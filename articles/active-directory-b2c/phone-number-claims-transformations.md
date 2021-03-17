---
title: Telefonní číslo – transformace deklarací ve vlastních zásadách
titleSuffix: Azure AD B2C
description: 'Odkaz na vlastní zásady pro telefonní číslo: transformace deklarací identity v Azure AD B2C.'
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e175a81efc1ab0950c1fda314efb206ff97a2b7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85385378"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>Definovat transformace deklarací telefonního čísla v Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek poskytuje referenční informace a příklady použití, které využívají k transformaci v rámci schématu rozhraní identity Experience v Azure Active Directory B2C (Azure AD B2C) na telefonním čísle. Další informace o transformacích deklarací obecně naleznete v tématu [ClaimsTransformations](claimstransformations.md).

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertphonenumberclaimtostring"></a>ConvertPhoneNumberClaimToString

Převede `phoneNumber` datový typ na `string` datový typ.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | phoneNumber |  Deklarace ClaimType pro převod na řetězec. |
| OutputClaim | phoneNumberString | řetězec | Deklarace ClaimType, která je vytvořena po vyvolání této transformace deklarací. |

V tomto příkladu je deklarace identity cellPhoneNumber s typem hodnoty `phoneNumber` převedena na deklaraci cellPhone s hodnotou typu `string` .

```xml
<ClaimsTransformation Id="PhoneNumberToString" TransformationMethod="ConvertPhoneNumberClaimToString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="cellPhoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="cellPhone" TransformationClaimType="phoneNumberString" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Příklad

- Vstupní deklarace identity:
  - **phoneNumber**: + 11234567890 (PhoneNumber)
- Deklarace výstupů:
  - **phoneNumberString**: + 11234567890 (řetězec)


## <a name="convertstringtophonenumberclaim"></a>ConvertStringToPhoneNumberClaim

Tato transformace deklarace identity ověřuje formát telefonního čísla. Pokud je v platném formátu, změňte ho na standardní formát, který používá Azure AD B2C. Pokud zadané telefonní číslo nemá platný formát, vrátí se chybová zpráva.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumberString | řetězec |  Deklarace řetězce pro telefonní číslo Telefonní číslo musí být v mezinárodním formátu a musí se zadat úvodní znak "+" a země/oblasti. Pokud je zadaná vstupní deklarace identity `country` , telefonní číslo je v místním formátu (bez kódu země nebo oblasti). |
| InputClaim | country | řetězec | Volitelné Deklarace řetězce pro kód země nebo oblasti telefonního čísla ve formátu ISO3166 (kód země/oblasti o dvou písmenech ISO-3166). |
| OutputClaim | outputClaim | phoneNumber | Výsledek této transformace deklarací identity. |

Transformace deklarací **ConvertStringToPhoneNumberClaim** je vždy prováděna z [technického profilu ověření](validation-technical-profile.md) , který je volán [vlastním technickým profilem](self-asserted-technical-profile.md) nebo [ovládacím prvkem zobrazení](display-controls.md). Metadata technického profilu **UserMessageIfClaimsTransformationInvalidPhoneNumber** s vlastním uplatněním řídí chybovou zprávu, která se zobrazí uživateli.

![Diagram cesty spuštění chybové zprávy](./media/phone-authentication/assert-execution.png)

Tuto transformaci deklarací identity můžete použít k zajištění, že poskytnutá deklarace řetězce je platné telefonní číslo. V takovém případě je vyvolána chybová zpráva. Následující příklad zkontroluje, zda je **phoneString** ClaimType skutečně platné telefonní číslo, a potom vrátí telefonní číslo ve standardním Azure AD B2C formátu. V opačném případě je vyvolána chybová zpráva.

```xml
<ClaimsTransformation Id="ConvertStringToPhoneNumber" TransformationMethod="ConvertStringToPhoneNumberClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneString" TransformationClaimType="phoneNumberString" />
    <InputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="country" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

Technický profil s vlastním uplatněním, který volá technický profil ověření, který obsahuje tuto transformaci deklarací, může definovat chybovou zprávu.

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationInvalidPhoneNumber">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Příklad 1

- Vstupní deklarace identity:
  - **phoneNumberString**: 033 456-7890
  - **země**: DK
- Deklarace výstupů:
  - **outputClaim**: + 450334567890

### <a name="example-2"></a>Příklad 2

- Vstupní deklarace identity:
  - **phoneNumberString**: + 1 (123) 456-7890
- Deklarace výstupů:
  - **outputClaim**: + 11234567890


## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>GetNationalNumberAndCountryCodeFromPhoneNumberString

Tím se extrahuje kód země nebo oblasti a národní číslo ze vstupní deklarace identity a volitelně vyvolá výjimku, pokud zadané telefonní číslo není platné.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | řetězec | Deklarace řetězce telefonního čísla. Telefonní číslo musí být v mezinárodním formátu a musí se zadat úvodní znak "+" a země/oblasti. |
| InputParameter | throwExceptionOnFailure | boolean | Volitelné Parametr, který označuje, zda je vyvolána výjimka, pokud telefonní číslo není platné. Výchozí hodnota je false. |
| InputParameter | countryCodeType | řetězec | Volitelné Parametr označující typ kódu země/oblasti ve výstupní deklaraci identity. Dostupné hodnoty jsou **CallingCode** (mezinárodní volající kód pro zemi nebo oblast, například + 1) nebo **ISO3166** (kód země/oblasti o dvou písmenech ISO-3166). |
| OutputClaim | nationalNumber | řetězec | Deklarace řetězce pro národní číslo telefonního čísla. |
| OutputClaim | countryCode | řetězec | Deklarace řetězce pro kód země nebo oblasti telefonního čísla. |


Pokud se transformace deklarací **GetNationalNumberAndCountryCodeFromPhoneNumberString** spustí z [technického profilu ověření](validation-technical-profile.md) , který je volán pomocí [technického profilu s vlastním uplatněním](self-asserted-technical-profile.md) nebo pomocí [akce ovládacího prvku zobrazení](display-controls.md#display-control-actions), pak **UserMessageIfPhoneNumberParseFailure** metadata technického profilu s vlastním uplatněním řídí chybovou zprávu, která se zobrazí uživateli.

![Diagram cesty spuštění chybové zprávy](./media/phone-authentication/assert-execution.png)

Pomocí této transformace deklarací identity můžete rozdělit celé telefonní číslo do kódu země nebo oblasti a do národního čísla. Pokud telefonní číslo není platné, můžete zvolit, aby se chybová zpráva vyvolala.

Následující příklad se pokusí rozdělit telefonní číslo na národní číslo a kód země/oblasti. Pokud je telefonní číslo platné, telefonní číslo bude přepsáno národním číslem. Pokud telefonní číslo není platné, výjimka nebude vyvolána a telefonní číslo má nadále původní hodnotu.

```xml
<ClaimsTransformation Id="GetNationalNumberAndCountryCodeFromPhoneNumberString" TransformationMethod="GetNationalNumberAndCountryCodeFromPhoneNumberString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="throwExceptionOnFailure" DataType="boolean" Value="false" />
    <InputParameter Id="countryCodeType" DataType="string" Value="ISO3166" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="nationalNumber" TransformationClaimType="nationalNumber" />
    <OutputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="countryCode" />
  </OutputClaims>
</ClaimsTransformation>
```

Technický profil s vlastním uplatněním, který volá technický profil ověření, který obsahuje tuto transformaci deklarací, může definovat chybovou zprávu.

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfPhoneNumberParseFailure">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Příklad 1

- Vstupní deklarace identity:
  - **phoneNumber**: + 49 (123) 456-7890
- Vstupní parametry:
  - **throwExceptionOnFailure**: false
  - **countryCodeType**: ISO3166
- Deklarace výstupů:
  - **nationalNumber**: 1234567890
  - **countryCode**: de

### <a name="example-2"></a>Příklad 2

- Vstupní deklarace identity:
  - **phoneNumber**: + 49 (123) 456-7890
- Vstupní parametry
  - **throwExceptionOnFailure**: false
  - **countryCodeType**: CallingCode
- Deklarace výstupů:
  - **nationalNumber**: 1234567890
  - **countryCode**: + 49

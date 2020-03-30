---
title: Telefonní číslo nároky transformace ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Vlastní odkaz na zásady pro transformace deklarací telefonních čísel v Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bd26b2b475e293a1fda1b007289ba7c3eef35136
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183923"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>Definování transformací deklarací telefonního čísla v Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek obsahuje odkazy a příklady pro použití telefonníčíslo nároky transformace schématu identity experience framework v Azure Active Directory B2C (Azure AD B2C). Další informace o transformacích deklarací obecně naleznete v tématu [ClaimsTransformations](claimstransformations.md).

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertphonenumberclaimtostring"></a>PřevéstPhoneNumberClaimToString

Převede `phoneNumber` datový typ `string` na datový typ.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | Phonenumber | Phonenumber |  ClaimType převést na řetězec. |
| Výstupní nárok | phoneNumberString | řetězec | ClaimType, který je vytvořen po této deklarace transformace byla vyvolána. |

V tomto příkladu je deklarace cellPhoneNumber s typem hodnoty převedena na `string`deklaraci mobilního `phoneNumber` telefonu s typem hodnoty .

```XML
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

- Vstupní deklarace:
  - **telefonní číslo**: +11234567890 (phoneNumber)
- Výstupní nároky:
  - **phoneNumberString**: +11234567890 (řetězec)


## <a name="convertstringtophonenumberclaim"></a>ConvertStringToPhoneNumberClaim

Tato transformace deklarace aplikace ověří formát telefonního čísla. Pokud je v platném formátu, změňte jej na standardní formát používaný Azure AD B2C. Pokud uvedené telefonní číslo není v platném formátu, je vrácena chybová zpráva.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | phoneNumberString | řetězec |  Deklarace řetězce pro telefonní číslo. Telefonní číslo musí být v mezinárodním formátu, kompletní s předním "+" a kódem země. Pokud je `country` zadána vstupní deklarace, telefonní číslo je v místním formátu (bez kódu země). |
| Vstupní deklarace | country | řetězec | [Nepovinné] Deklarace řetězce pro kód země telefonního čísla ve formátu ISO3166 (dvoupísmenný kód země ISO-3166). |
| Výstupní nárok | outputClaim | Phonenumber | Výsledek tohoto nároky transformace. |

**ConvertStringToPhoneNumberClaim** nároky transformace je vždy proveden z [ověření technického profilu,](validation-technical-profile.md) který je volán [self-tvrdil technický profil](self-asserted-technical-profile.md) nebo ovládací prvek [zobrazení](display-controls.md). Metadata technického profilu, která se zobrazuje, řídí metadata uživatelského účtu **IfIfClaimsTransformationInvalidPhoneNumber,** která je sama uplatněna, a řídí chybovou zprávu, která je uživateli prezentována.

![Diagram cesty spuštění chybové zprávy](./media/phone-authentication/assert-execution.png)

Tuto transformaci deklarací identity můžete použít k zajištění, že zadaný řetězec deklarace identity je platné telefonní číslo. Pokud tomu tak není, je vyvolána chybová zpráva. Následující příklad zkontroluje, zda **phoneString** ClaimType je skutečně platné telefonní číslo a potom vrátí telefonní číslo ve standardním formátu Azure AD B2C. V opačném případě je vyvolána chybová zpráva.

```XML
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

Vlastní uplatněný technický profil, který volá technický profil ověření, který obsahuje tuto transformaci deklarací identity, může definovat chybovou zprávu.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationInvalidPhoneNumber">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Příklad 1

- Vstupní deklarace:
  - **phoneNumberString**: 033 456-7890
  - **země**: DK
- Výstupní nároky:
  - **outputClaim**: +450334567890

### <a name="example-2"></a>Příklad 2

- Vstupní deklarace:
  - **phoneNumberString**: +1 (123) 456-7890
- Výstupní nároky:
  - **outputClaim**: +11234567890


## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>GetNationalNumberAndCountryCodeFromPhoneNumberString

Tím se z vstupní deklarace vyextrahuje kód země a národní číslo a volitelně vyvolá výjimku, pokud zadané telefonní číslo není platné.

| Položka | TransformationClaimType | Typ dat | Poznámky |
| ---- | ----------------------- | --------- | ----- |
| Vstupní deklarace | Phonenumber | řetězec | Deklarace řetězce telefonního čísla. Telefonní číslo musí být v mezinárodním formátu, kompletní s předním "+" a kódem země. |
| Parametr Input | throwExceptionOnFailure | Boolean | [Nepovinné] Parametr označující, zda je vyvolána výjimka, když telefonní číslo není platné. Výchozí hodnota je false. |
| Parametr Input | zeměKódTyp | řetězec | [Nepovinné] Parametr označující typ kódu země ve výstupní deklaraci. Dostupné hodnoty jsou **CallingCode** (mezinárodní volací kód pro zemi, například +1) nebo **ISO3166** (dvoupísmenný kód země ISO-3166). |
| Výstupní nárok | národníČíslo | řetězec | Deklarace řetězce pro národní číslo telefonního čísla. |
| Výstupní nárok | kód země | řetězec | Deklarace řetězce pro kód země telefonního čísla. |


Pokud **GetNationalNumberAndCountryCodeFromPhoneNumberString** transformace nároky je proveden z [ověření technický profil,](validation-technical-profile.md) který je volán [self-tvrdil technický profil](self-asserted-technical-profile.md) nebo akce [ovládacího prvku zobrazení](display-controls.md#display-control-actions), pak **UserMessageIfPhoneParaseFailure** self-tvrdil, technický profil metadata řídí chybovou zprávu, která je předložena uživateli.

![Diagram cesty spuštění chybové zprávy](./media/phone-authentication/assert-execution.png)

Pomocí této transformace deklarací můžete rozdělit celé telefonní číslo na kód země a národní číslo. Pokud poskytnuté telefonní číslo není platné, můžete vyvolat chybovou zprávu.

Následující příklad se pokusí rozdělit telefonní číslo na národní číslo a kód země. Pokud je telefonní číslo platné, bude telefonní číslo přepsáno národním číslem. Pokud telefonní číslo není platné, výjimka nebude vyvolána a telefonní číslo má stále původní hodnotu.

```XML
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

Vlastní uplatněný technický profil, který volá technický profil ověření, který obsahuje tuto transformaci deklarací identity, může definovat chybovou zprávu.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfPhoneNumberParseFailure">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Příklad 1

- Vstupní deklarace:
  - **telefonní číslo**: +49 (123) 456-7890
- Vstupní parametry:
  - **throwExceptionOnFailure**: false
  - **zeměTyp kódu**: ISO3166
- Výstupní nároky:
  - **národníČíslo**: 1234567890
  - **zeměKód**: DE

### <a name="example-2"></a>Příklad 2

- Vstupní deklarace:
  - **telefonní číslo**: +49 (123) 456-7890
- Vstupní parametry
  - **throwExceptionOnFailure**: false
  - **countryCodeType**: CallingCode
- Výstupní nároky:
  - **národníČíslo**: 1234567890
  - **zeměKód**: +49

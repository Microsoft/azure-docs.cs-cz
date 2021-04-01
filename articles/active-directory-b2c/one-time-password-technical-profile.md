---
title: Povolit ověřování JEDNORÁZOVým heslem
titleSuffix: Azure AD B2C
description: Přečtěte si, jak nastavit scénář jednorázového hesla pomocí Azure AD B2C vlastních zásad.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/19/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 12b9639342e2e35b9229aa15bb9cfb4695427606
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97881187"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definování technického profilu s jednorázovým heslem v Azure AD B2C vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) poskytuje podporu pro správu generování a ověřování jednorázového hesla. Použijte technický profil k vygenerování kódu a pak ověřte, že kód je pozdější.

Technický profil pro jednorázové heslo může také při ověřování kódu vrátit chybovou zprávu. Navrhněte integraci s jednorázovým heslem pomocí **ověřovacího technického profilu**. Technický profil ověření volá technický profil pro jednorázové heslo pro ověření kódu. Technický profil ověření ověřuje uživatelem poskytnutá data před pokračováním cesty uživatele. S technickým profilem ověření se na stránce s vlastním kontrolním jménem zobrazí chybová zpráva.

## <a name="protocol"></a>Protokol

Atribut **Name** elementu **Protocol** musí být nastaven na hodnotu `Proprietary` . Atribut **obslužné rutiny** musí obsahovat plně kvalifikovaný název sestavení obslužné rutiny protokolu, které je používáno Azure AD B2C:

```xml
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

Následující příklad ukazuje technický profil pro jednorázové heslo:

```xml
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>Generování kódu

Prvním režimem tohoto technického profilu je vygenerování kódu. Níže jsou uvedeny možnosti, které lze nakonfigurovat pro tento režim. V rámci relace jsou sledovány kódy vygenerované a pokusy. 

### <a name="input-claims"></a>Vstupní deklarace identity

Element **InputClaims** obsahuje seznam deklarací identity potřebných k odeslání do poskytovatele protokolu jednorázového hesla. Název vaší deklarace identity můžete také namapovat na název definovaný níže.

| ClaimReferenceId | Povinné | Popis |
| --------- | -------- | ----------- |
| identifikátor | Yes | Identifikátor k identifikaci uživatele, který potřebuje později ověřit kód. Obvykle se používá jako identifikátor cílového umístění, do kterého se kód doručuje, například e-mailová adresa nebo telefonní číslo. |

Element **InputClaimsTransformations** může obsahovat kolekci prvků **InputClaimsTransformation** , které se používají k úpravě vstupních deklarací identity nebo k vygenerování nových před odesláním do poskytovatele protokolu jednorázového hesla.

### <a name="output-claims"></a>Deklarace výstupů

Element **OutputClaims** obsahuje seznam deklarací generovaných poskytovatelem protokolu jednorázového hesla. Název vaší deklarace identity můžete také namapovat na název definovaný níže.

| ClaimReferenceId | Povinné | Popis |
| --------- | -------- | ----------- |
| otpGenerated | Yes | Generovaný kód, jehož relace je spravovaná pomocí Azure AD B2C. |

Element **OutputClaimsTransformations** může obsahovat kolekci prvků **OutputClaimsTransformation** , které se používají k úpravě výstupních deklarací identity nebo k vygenerování nových.

### <a name="metadata"></a>Metadata

Následující nastavení lze použít ke konfiguraci režimu generování kódu:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| CodeExpirationInSeconds | No | Doba v sekundách, po kterou bude vypršení platnosti kódu. Minimum: `60` ; Maximum: `1200` ; Výchozí: `600` . Pokaždé, když je poskytnut kód (stejný kód s použitím `ReuseSameCode` nebo nový kód), je prodloužení platnosti kódu prodlouženo. Tato doba se používá také k nastavení časového limitu opakování (po dosažení maximálního počtu pokusů je uživatel zablokován z pokusu o získání nových kódů až do vypršení platnosti této doby). |
| CodeLength | No | Délka kódu. Výchozí hodnota je `6`. |
| CharacterSet | No | Znaková sada pro kód formátovaný pro použití v regulárním výrazu. Například, `a-z0-9A-Z`. Výchozí hodnota je `0-9`. Znaková sada musí obsahovat minimálně 10 různých znaků v zadané sadě. |
| NumRetryAttempts | No | Počet pokusů o ověření před kódem, který je považován za neplatný. Výchozí hodnota je `5`. |
| NumCodeGenerationAttempts | No | Počet pokusů o generování kódu na identifikátor. Výchozí hodnota je 10, pokud není zadána. |
| Operace | Yes | Operace, která má být provedena. Možná hodnota: `GenerateCode` . |
| ReuseSameCode | No | Zda by měl být uveden stejný kód namísto generování nového kódu, pokud daný kód nevypršel a je stále platný. Výchozí hodnota je `false`.  |



### <a name="example"></a>Příklad

Následující příklad `TechnicalProfile` slouží k vygenerování kódu:

```xml
<TechnicalProfile Id="GenerateCode">
  <DisplayName>Generate Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">GenerateCode</Item>
    <Item Key="CodeExpirationInSeconds">600</Item>
    <Item Key="CodeLength">6</Item>
    <Item Key="CharacterSet">0-9</Item>
    <Item Key="NumRetryAttempts">5</Item>
    <Item Key="NumCodeGenerationAttempts">15</Item>
    <Item Key="ReuseSameCode">false</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpGenerated" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Ověřit kód

Druhým režimem tohoto technického profilu je ověření kódu. Níže jsou uvedeny možnosti, které lze nakonfigurovat pro tento režim.

### <a name="input-claims"></a>Vstupní deklarace identity

Element **InputClaims** obsahuje seznam deklarací identity potřebných k odeslání do poskytovatele protokolu jednorázového hesla. Název vaší deklarace identity můžete také namapovat na název definovaný níže.

| ClaimReferenceId | Povinné | Popis |
| --------- | -------- | ----------- |
| identifikátor | Yes | Identifikátor k identifikaci uživatele, který dříve vygeneroval kód. Obvykle se používá jako identifikátor cílového umístění, do kterého se kód doručuje, například e-mailová adresa nebo telefonní číslo. |
| otpToVerify | Yes | Ověřovací kód poskytnutý uživatelem |

Element **InputClaimsTransformations** může obsahovat kolekci prvků **InputClaimsTransformation** , které se používají k úpravě vstupních deklarací identity nebo k vygenerování nových před odesláním do poskytovatele protokolu jednorázového hesla.

### <a name="output-claims"></a>Deklarace výstupů

Během ověřování kódu tohoto poskytovatele protokolu nejsou k dispozici žádné deklarace výstupu.

Element **OutputClaimsTransformations** může obsahovat kolekci prvků **OutputClaimsTransformation** , které se používají k úpravě výstupních deklarací identity nebo k vygenerování nových.

### <a name="metadata"></a>Metadata

V režimu ověření kódu lze použít následující nastavení:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Operace | Yes | Operace, která má být provedena. Možná hodnota: `VerifyCode` . |


### <a name="ui-elements"></a>Prvky uživatelského rozhraní

Následující metadata lze použít ke konfiguraci chybových zpráv zobrazených při selhání ověřování kódu. Metadata by měla být nakonfigurovaná v technickém profilu s [vlastním kontrolním](self-asserted-technical-profile.md) výrazem. Chybové zprávy lze [lokalizovat](localization-string-ids.md#one-time-password-error-messages).

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| UserMessageIfSessionDoesNotExist | No | Zpráva, která se zobrazí uživateli, pokud vypršela platnost relace ověření kódu Buď je tento kód neplatný, nebo kód nebyl nikdy vygenerován pro daný identifikátor. |
| UserMessageIfMaxRetryAttempted | No | Zpráva, která se zobrazí uživateli, pokud překročila maximální povolený počet pokusů o ověření. |
| UserMessageIfMaxNumberOfCodeGenerated | No | Zpráva, která se zobrazí uživateli, pokud generování kódu překročilo maximální povolený počet pokusů. |
| UserMessageIfInvalidCode | No | Zpráva, která se zobrazí uživateli, pokud jim byl zadán neplatný kód. |
| UserMessageIfVerificationFailedRetryAllowed | No | Zpráva, která se zobrazí uživateli, pokud jim byl poskytnut neplatný kód a uživatel je oprávněn poskytnout správný kód.  |
|UserMessageIfSessionConflict|No| Zpráva, která se zobrazí uživateli, pokud nelze kód ověřit|

### <a name="example"></a>Příklad

Následující příklad `TechnicalProfile` slouží k ověření kódu:

```xml
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
    <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Další kroky

V následujícím článku najdete příklad použití technického profilu s jednorázovým heslem s ověřováním pomocí vlastního e-mailu:

- Vlastní ověření e-mailu v Azure Active Directory B2C ([Mailjet](custom-email-mailjet.md), [SendGrid](custom-email-sendgrid.md))


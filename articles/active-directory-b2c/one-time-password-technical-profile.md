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
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 701fb64dd85526bc79cab48bf36d4583da71ca76
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184022"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definování technického profilu s jednorázovým heslem v Azure AD B2C vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) poskytuje podporu pro správu generování a ověřování jednorázového hesla. Použijte technický profil k vygenerování kódu a pak ověřte, že kód je pozdější.

Technický profil pro jednorázové heslo může také při ověřování kódu vrátit chybovou zprávu. Navrhněte integraci s jednorázovým heslem pomocí **ověřovacího technického profilu**. Technický profil ověření volá technický profil pro jednorázové heslo pro ověření kódu. Technický profil ověření ověřuje uživatelem poskytnutá data před pokračováním cesty uživatele. S technickým profilem ověření se na stránce s vlastním kontrolním jménem zobrazí chybová zpráva.

## <a name="protocol"></a>Protocol (Protokol)

Atribut **Name** elementu **Protocol** musí být nastaven na `Proprietary`. Atribut **obslužné rutiny** musí obsahovat plně kvalifikovaný název sestavení obslužné rutiny protokolu, které je používáno Azure AD B2C:

```XML
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

Následující příklad ukazuje technický profil pro jednorázové heslo:

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>Generování kódu

Prvním režimem tohoto technického profilu je vygenerování kódu. Níže jsou uvedeny možnosti, které lze nakonfigurovat pro tento režim.

### <a name="input-claims"></a>Vstupní deklarace identity

Element **InputClaims** obsahuje seznam deklarací identity potřebných k odeslání do poskytovatele protokolu jednorázového hesla. Název vaší deklarace identity můžete také namapovat na název definovaný níže.

| ClaimReferenceId | Požaduje se | Popis |
| --------- | -------- | ----------- |
| identifikátor | Ano | Identifikátor k identifikaci uživatele, který potřebuje později ověřit kód. Obvykle se používá jako identifikátor cílového umístění, do kterého se kód doručuje, například e-mailová adresa nebo telefonní číslo. |

Element **InputClaimsTransformations** může obsahovat kolekci prvků **InputClaimsTransformation** , které se používají k úpravě vstupních deklarací identity nebo k vygenerování nových před odesláním do poskytovatele protokolu jednorázového hesla.

### <a name="output-claims"></a>Deklarace výstupů

Element **OutputClaims** obsahuje seznam deklarací generovaných poskytovatelem protokolu jednorázového hesla. Název vaší deklarace identity můžete také namapovat na název definovaný níže.

| ClaimReferenceId | Požaduje se | Popis |
| --------- | -------- | ----------- |
| otpGenerated | Ano | Generovaný kód, jehož relace je spravovaná pomocí Azure AD B2C. |

Element **OutputClaimsTransformations** může obsahovat kolekci prvků **OutputClaimsTransformation** , které se používají k úpravě výstupních deklarací identity nebo k vygenerování nových.

### <a name="metadata"></a>Metadata

Následující nastavení lze použít ke konfiguraci generování a údržby kódu:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| CodeExpirationInSeconds | Ne | Doba v sekundách, po kterou bude vypršení platnosti kódu. Minimum: `60`; Maximum: `1200`; Výchozí: `600`. |
| CodeLength | Ne | Délka kódu. Výchozí hodnota je `6`. |
| CharacterSet | Ne | Znaková sada pro kód formátovaný pro použití v regulárním výrazu. například `a-z0-9A-Z`. Výchozí hodnota je `0-9`. Znaková sada musí obsahovat minimálně 10 různých znaků v zadané sadě. |
| NumRetryAttempts | Ne | Počet pokusů o ověření před kódem, který je považován za neplatný. Výchozí hodnota je `5`. |
| Operace | Ano | Operace, která má být provedena. Možné hodnoty: `GenerateCode`nebo `VerifyCode`. |
| ReuseSameCode | Ne | Bez ohledu na to, zda by měl být uveden duplicitní kód namísto generování nového kódu, pokud uplynula platnost daného kódu a je stále platný. Výchozí hodnota je `false`. |

### <a name="returning-error-message"></a>Vracení chybové zprávy

Pro režim generování kódu se nevrátila žádná chybová zpráva.

### <a name="example"></a>Příklad

Následující příklad `TechnicalProfile` se používá pro generování kódu:

```XML
<TechnicalProfile Id="GenerateCode">
    <DisplayName>Generate Code</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">GenerateCode</Item>
        <Item Key="CodeExpirationInSeconds">600</Item>
        <Item Key="CodeLength">6</Item>
        <Item Key="CharacterSet">0-9</Item>
        <Item Key="NumRetryAttempts">5</Item>
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

| ClaimReferenceId | Požaduje se | Popis |
| --------- | -------- | ----------- |
| identifikátor | Ano | Identifikátor k identifikaci uživatele, který dříve vygeneroval kód. Obvykle se používá jako identifikátor cílového umístění, do kterého se kód doručuje, například e-mailová adresa nebo telefonní číslo. |
| otpToVerify | Ano | Ověřovací kód poskytnutý uživatelem |

Element **InputClaimsTransformations** může obsahovat kolekci prvků **InputClaimsTransformation** , které se používají k úpravě vstupních deklarací identity nebo k vygenerování nových před odesláním do poskytovatele protokolu jednorázového hesla.

### <a name="output-claims"></a>Deklarace výstupů

Během ověřování kódu tohoto poskytovatele protokolu nejsou k dispozici žádné deklarace výstupu.

Element **OutputClaimsTransformations** může obsahovat kolekci prvků **OutputClaimsTransformation** , které se používají k úpravě výstupních deklarací identity nebo k vygenerování nových.

### <a name="metadata"></a>Metadata

Následující nastavení lze použít ke konfiguraci chybové zprávy, která se zobrazí při selhání ověření kódu:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| UserMessageIfSessionDoesNotExist | Ne | Zpráva, která se zobrazí uživateli, pokud vypršela platnost relace ověření kódu Buď je tento kód neplatný, nebo kód nebyl nikdy vygenerován pro daný identifikátor. |
| UserMessageIfMaxRetryAttempted | Ne | Zpráva, která se zobrazí uživateli, pokud překročila maximální povolený počet pokusů o ověření. |
| UserMessageIfInvalidCode | Ne | Zpráva, která se zobrazí uživateli, pokud jim byl zadán neplatný kód. |

### <a name="returning-error-message"></a>Vracení chybové zprávy

Jak je popsáno v části [metadata](#metadata), můžete přizpůsobit chybovou zprávu pro uživatele pro různé chybové případy. Tyto zprávy můžete dále lokalizovat pomocí prefixu národního prostředí, například:

```XML
<Item Key="en.UserMessageIfInvalidCode">Wrong code has been entered.</Item>
```

### <a name="example"></a>Příklad

Následující příklad `TechnicalProfile` slouží k ověření kódu:

```XML
<TechnicalProfile Id="VerifyCode">
    <DisplayName>Verify Code</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">VerifyCode</Item>
        <Item Key="UserMessageIfInvalidCode">Wrong code has been entered.</Item>
        <Item Key="UserMessageIfSessionDoesNotExist">Code has expired.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You've tried too many times.</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
    </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Další kroky

V následujícím článku najdete příklad použití profilu jednorázového hesla technial s vlastním ověřením e-mailu:

- [Ověření vlastního e-mailu v Azure Active Directory B2C](custom-email.md)


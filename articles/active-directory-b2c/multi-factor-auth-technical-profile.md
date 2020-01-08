---
title: Technické profily Azure MFA ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Referenční informace o vlastních zásadách pro Azure Multi-Factor Authentication (MFA) Technical Profiles v Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a8aaea6b2afb4d89e6e667edba0eeba2f4ddcca8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480213"
---
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definování technického profilu Azure MFA ve službě Azure AD B2C vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) poskytuje podporu pro ověření telefonního čísla pomocí Azure Multi-Factor Authentication (MFA). Tento technický profil použijte k vygenerování a odeslání kódu na telefonní číslo a pak ověřte kód.

Technický profil Azure MFA může také vrátit chybovou zprávu. Integraci s Azure MFA můžete navrhovat pomocí **technického profilu ověření**. Technický profil ověření volá službu Azure MFA. Technický profil ověření ověřuje uživatelem poskytnutá data před pokračováním cesty uživatele. S technickým profilem ověření se zobrazí chybová zpráva na stránce s vlastním kontrolním jménem.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocol (Protokol)

Atribut **Name** elementu **Protocol** musí být nastaven na `Proprietary`. Atribut **obslužné rutiny** musí obsahovat plně kvalifikovaný název sestavení obslužné rutiny protokolu, které je používáno Azure AD B2C:

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

Následující příklad ukazuje technický profil Azure MFA:

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>Odeslat SMS

Prvním režimem tohoto technického profilu je vygenerování kódu a jeho odeslání. Pro tento režim lze nakonfigurovat následující možnosti.

### <a name="input-claims"></a>Vstupní deklarace identity

Element **InputClaims** obsahuje seznam deklarací pro odeslání do Azure MFA. Název vaší deklarace identity můžete také namapovat na název definovaný v technickém profilu MFA.

| ClaimReferenceId | Požaduje se | Popis |
| --------- | -------- | ----------- |
| userPrincipalName (Hlavní název uživatele) | Ano | Identifikátor uživatele, který vlastní telefonní číslo. |
| phoneNumber | Ano | Telefonní číslo, do kterého se má poslat SMS kód |
| Společnosti | Ne |Název společnosti v serveru SMS. Pokud není zadaný, použije se název vaší aplikace. |
| locale | Ne | Národní prostředí serveru SMS. Pokud není zadaný, použije se národní prostředí prohlížeče uživatele. |

Element **InputClaimsTransformations** může obsahovat kolekci prvků **InputClaimsTransformation** , které se používají k úpravě vstupních deklarací identity nebo k vygenerování nových dat před odesláním do služby Azure MFA.

### <a name="output-claims"></a>Deklarace výstupů

Zprostředkovatel protokolu Azure MFA nevrací žádné **OutputClaims**, takže není potřeba zadávat deklarace výstupů. Můžete ale zahrnout deklarace identity, které nevrací poskytovatel identity Azure MFA, pokud nastavíte atribut `DefaultValue`.

Element **OutputClaimsTransformations** může obsahovat kolekci prvků **OutputClaimsTransformation** , které se používají k úpravě výstupních deklarací identity nebo k vygenerování nových.

### <a name="metadata"></a>Metadata

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Operace | Ano | Musí být **OneWaySMS**.  |
| UserMessageIfInvalidFormat | Ne | Vlastní chybová zpráva, pokud zadané telefonní číslo není platné telefonní číslo |
| UserMessageIfCouldntSendSms | Ne | Vlastní chybová zpráva, pokud telefonní číslo nepřijímá zprávu SMS |
| UserMessageIfServerError | Ne | Vlastní chybová zpráva, pokud došlo k vnitřní chybě serveru |

### <a name="return-an-error-message"></a>Vrátí chybovou zprávu.

Jak je popsáno v části [metadata](#metadata), můžete upravit chybovou zprávu zobrazenou uživateli pro různé chybové případy. Tyto zprávy můžete dále lokalizovat pomocí předpony národního prostředí. Příklad:

```XML
<Item Key="en.UserMessageIfInvalidFormat">Invalid phone number.</Item>
```

### <a name="example-send-an-sms"></a>Příklad: odeslání serveru SMS

Následující příklad ukazuje technický profil Azure MFA, který slouží k odeslání kódu prostřednictvím serveru SMS.

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">OneWaySMS</Item>
    </Metadata>
    <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="CombinePhoneAndCountryCode" />
        <InputClaimsTransformation ReferenceId="ConvertStringToPhoneNumber" />
    </InputClaimsTransformations>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="userPrincipalName" />
        <InputClaim ClaimTypeReferenceId="fullPhoneNumber" PartnerClaimType="phoneNumber" />
    </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Ověřit kód

Druhým režimem tohoto technického profilu je ověření kódu. Pro tento režim lze nakonfigurovat následující možnosti.

### <a name="input-claims"></a>Vstupní deklarace identity

Element **InputClaims** obsahuje seznam deklarací pro odeslání do Azure MFA. Název vaší deklarace identity můžete také namapovat na název definovaný v technickém profilu MFA.

| ClaimReferenceId | Požaduje se | Popis |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| Ano | Stejné telefonní číslo jako dříve použité k odeslání kódu. Používá se také k vyhledání relace ověřování pro telefon. |
| verificationCode  | Ano | Ověřovací kód poskytnutý uživatelem, který se má ověřit |

Element **InputClaimsTransformations** může obsahovat kolekci prvků **InputClaimsTransformation** , které se používají k úpravě vstupních deklarací identity nebo k vygenerování nových před voláním služby Azure MFA.

### <a name="output-claims"></a>Deklarace výstupů

Zprostředkovatel protokolu Azure MFA nevrací žádné **OutputClaims**, takže není potřeba zadávat deklarace výstupů. Můžete ale zahrnout deklarace identity, které nevrací poskytovatel identity Azure MFA, pokud nastavíte atribut `DefaultValue`.

Element **OutputClaimsTransformations** může obsahovat kolekci prvků **OutputClaimsTransformation** , které se používají k úpravě výstupních deklarací identity nebo k vygenerování nových.

## <a name="metadata"></a>Metadata

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Operace | Ano | Je nutné **ověřit** |
| UserMessageIfInvalidFormat | Ne | Vlastní chybová zpráva, pokud zadané telefonní číslo není platné telefonní číslo |
| UserMessageIfWrongCodeEntered | Ne | Vlastní chybová zpráva, pokud je kód zadaný pro ověření nesprávný |
| UserMessageIfMaxAllowedCodeRetryReached | Ne | Vlastní chybová zpráva v případě, že se uživatel pokusil o ověřovací kód příliš mnohokrát |
| UserMessageIfThrottled | Ne | Vlastní chybová zpráva, pokud je uživatel omezený |
| UserMessageIfServerError | Ne | Vlastní chybová zpráva, pokud došlo k vnitřní chybě serveru |

### <a name="return-an-error-message"></a>Vrátí chybovou zprávu.

Jak je popsáno v části [metadata](#metadata), můžete upravit chybovou zprávu zobrazenou uživateli pro různé chybové případy. Tyto zprávy můžete dále lokalizovat pomocí předpony národního prostředí. Příklad:

```XML
<Item Key="en.UserMessageIfWrongCodeEntered">Wrong code has been entered.</Item>
```

### <a name="example-verify-a-code"></a>Příklad: ověření kódu

Následující příklad ukazuje technický profil Azure MFA, který slouží k ověření kódu.

```XML
<TechnicalProfile Id="AzureMfa-VerifySms">
    <DisplayName>Verify Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">Verify</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="phoneNumber" PartnerClaimType="phoneNumber" />
        <InputClaim ClaimTypeReferenceId="verificationCode" />
    </InputClaims>
</TechnicalProfile>
```

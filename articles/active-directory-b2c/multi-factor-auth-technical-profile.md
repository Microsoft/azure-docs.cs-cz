---
title: Technické profily Azure AD MFA ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Referenční informace o vlastních zásadách pro Azure AD Multi-Factor Authentication (MFA) Technical Profiles v Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e81ac35555e6653cecb602e5af2f19aa3e2f05e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94840589"
---
# <a name="define-an-azure-ad-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definování technického profilu Azure AD MFA v Azure AD B2C vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) nabízí podporu ověření telefonního čísla pomocí vícefaktorového ověřování (MFA) Azure AD. Pokud chcete vygenerovat kód, odeslat ho na telefonní číslo a pak tento kód ověřit, použijte tento technický profil. Technický profil Azure AD MFA může také vracet chybovou zprávu.  Technický profil ověření ověřuje uživatelem poskytnutá data před pokračováním cesty uživatele. S technickým profilem ověření se zobrazí chybová zpráva na stránce s vlastním kontrolním jménem.

Tento technický profil:

- Neposkytuje rozhraní pro interakci s uživatelem. Místo toho je uživatelské rozhraní voláno z technického profilu s [vlastním uplatněním](self-asserted-technical-profile.md) nebo [ovládacího prvku zobrazení](display-controls.md) jako [technického profilu ověření](validation-technical-profile.md).
- Pomocí služby Azure AD MFA vygeneruje a pošle kód na telefonní číslo a potom ověří kód.  
- Ověří telefonní číslo prostřednictvím textových zpráv.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protokol

Atribut **Name** elementu **Protocol** musí být nastaven na hodnotu `Proprietary` . Atribut **obslužné rutiny** musí obsahovat plně kvalifikovaný název sestavení obslužné rutiny protokolu, které je používáno Azure AD B2C:

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

Následující příklad ukazuje technický profil Azure AD MFA:

```xml
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>Odeslat SMS

Prvním režimem tohoto technického profilu je vygenerování kódu a jeho odeslání. Pro tento režim lze nakonfigurovat následující možnosti.

### <a name="input-claims"></a>Vstupní deklarace identity

Element **InputClaims** obsahuje seznam deklarací pro odeslání do Azure AD MFA. Název vaší deklarace identity můžete také namapovat na název definovaný v technickém profilu MFA.

| ClaimReferenceId | Povinné | Popis |
| --------- | -------- | ----------- |
| userPrincipalName (Hlavní název uživatele) | Yes | Identifikátor uživatele, který vlastní telefonní číslo. |
| phoneNumber | Yes | Telefonní číslo, do kterého se má poslat SMS kód |
| Společnosti | No |Název společnosti v serveru SMS. Pokud není zadaný, použije se název vaší aplikace. |
| locale | No | Národní prostředí serveru SMS. Pokud není zadaný, použije se národní prostředí prohlížeče uživatele. |

Element **InputClaimsTransformations** může obsahovat kolekci prvků **InputClaimsTransformation** , které se používají k úpravě vstupních deklarací identity nebo k vygenerování nových dat před odesláním do služby Azure AD MFA.

### <a name="output-claims"></a>Deklarace výstupů

Zprostředkovatel protokolu Azure AD MFA nevrací žádné **OutputClaims**, takže není nutné zadávat deklarace výstupů. Můžete ale zahrnout deklarace identity, které nevrací poskytovatel identity MFA Azure AD, pokud nastavíte `DefaultValue` atribut.

Element **OutputClaimsTransformations** může obsahovat kolekci prvků **OutputClaimsTransformation** , které se používají k úpravě výstupních deklarací identity nebo k vygenerování nových.

### <a name="metadata"></a>Metadata

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Operace | Yes | Musí být **OneWaySMS**.  |

#### <a name="ui-elements"></a>Prvky uživatelského rozhraní

Následující metadata lze použít ke konfiguraci chybových zpráv zobrazených při odesílání selhání serveru SMS. Metadata by měla být nakonfigurovaná v technickém profilu s [vlastním kontrolním](self-asserted-technical-profile.md) výrazem. Chybové zprávy lze [lokalizovat](localization-string-ids.md#azure-ad-mfa-error-messages).

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| UserMessageIfCouldntSendSms | No | Chybová zpráva uživatele, pokud telefonní číslo nepřijímá zprávu SMS. |
| UserMessageIfInvalidFormat | No | Chybová zpráva uživatele v případě, že zadané telefonní číslo není platné telefonní číslo. |
| UserMessageIfServerError | No | Chybová zpráva uživatele v případě, že došlo k vnitřní chybě serveru |
| UserMessageIfThrottled| No | Chybová zpráva uživatele, pokud byl požadavek omezen.|

### <a name="example-send-an-sms"></a>Příklad: odeslání serveru SMS

Následující příklad ukazuje technický profil Azure AD MFA, který se používá k odeslání kódu přes SMS.

```xml
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

Element **InputClaims** obsahuje seznam deklarací pro odeslání do Azure AD MFA. Název vaší deklarace identity můžete také namapovat na název definovaný v technickém profilu MFA.

| ClaimReferenceId | Povinné | Popis |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| Yes | Stejné telefonní číslo jako dříve použité k odeslání kódu. Používá se také k vyhledání relace ověřování pro telefon. |
| verificationCode  | Yes | Ověřovací kód poskytnutý uživatelem, který se má ověřit |

Element **InputClaimsTransformations** může obsahovat kolekci prvků **InputClaimsTransformation** , které se používají k úpravě vstupních deklarací identity nebo k vygenerování nových před VOLÁNÍM služby Azure AD MFA.

### <a name="output-claims"></a>Deklarace výstupů

Zprostředkovatel protokolu Azure AD MFA nevrací žádné **OutputClaims**, takže není nutné zadávat deklarace výstupů. Můžete ale zahrnout deklarace identity, které nevrací poskytovatel identity MFA Azure AD, pokud nastavíte `DefaultValue` atribut.

Element **OutputClaimsTransformations** může obsahovat kolekci prvků **OutputClaimsTransformation** , které se používají k úpravě výstupních deklarací identity nebo k vygenerování nových.

### <a name="metadata"></a>Metadata

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Operace | Yes | Je nutné **ověřit** |

#### <a name="ui-elements"></a>Prvky uživatelského rozhraní

Následující metadata lze použít ke konfiguraci chybových zpráv zobrazených při selhání ověřování kódu. Metadata by měla být nakonfigurovaná v technickém profilu s [vlastním kontrolním](self-asserted-technical-profile.md) výrazem. Chybové zprávy lze [lokalizovat](localization-string-ids.md#azure-ad-mfa-error-messages).

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| UserMessageIfMaxAllowedCodeRetryReached| No | Chybová zpráva uživatele v případě, že se uživatel pokusil o ověřovací kód příliš mnohokrát. |
| UserMessageIfServerError | No | Chybová zpráva uživatele v případě, že došlo k vnitřní chybě serveru |
| UserMessageIfThrottled| No | Chybová zpráva uživatele, pokud je požadavek omezen.|
| UserMessageIfWrongCodeEntered| No| Chybová zpráva uživatele, je-li kód pro ověření zadán nesprávně.|

### <a name="example-verify-a-code"></a>Příklad: ověření kódu

Následující příklad ukazuje technický profil Azure AD MFA, který slouží k ověření kódu.

```xml
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

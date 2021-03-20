---
title: Technické profily Azure AD SSPR ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Reference k vlastním zásadám pro technické profily Azure AD SSPR v Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3e6fcf956639d827a8654c5ee80e7cab8cadf930
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85383593"
---
# <a name="define-an-azure-ad-sspr-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definování technického profilu Azure AD SSPR v Azure AD B2C vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) poskytuje podporu pro ověření e-mailové adresy pro Samoobslužné resetování hesla (SSPR). Pomocí technického profilu Azure AD SSPR vygenerujte a odešlete kód na e-mailovou adresu a pak ověřte kód. Technický profil Azure AD SSPR může také vrátit chybovou zprávu. Technický profil ověření ověřuje uživatelem poskytnutá data před pokračováním cesty uživatele. S technickým profilem ověření se zobrazí chybová zpráva na stránce s vlastním kontrolním jménem.

Tento technický profil:

- Neposkytuje rozhraní pro interakci s uživatelem. Místo toho je uživatelské rozhraní voláno z technického profilu s [vlastním uplatněním](self-asserted-technical-profile.md) nebo [ovládacího prvku zobrazení](display-controls.md) jako [technického profilu ověření](validation-technical-profile.md).
- Pomocí služby Azure AD SSPR vygeneruje a pošle kód na e-mailovou adresu a potom ověří kód.  
- Ověří e-mailovou adresu pomocí ověřovacího kódu.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protokol

Atribut **Name** elementu **Protocol** musí být nastaven na hodnotu `Proprietary` . Atribut **obslužné rutiny** musí obsahovat plně kvalifikovaný název sestavení obslužné rutiny protokolu, které je používáno Azure AD B2C:

```
Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

Následující příklad ukazuje technický profil Azure AD SSPR:

```XML
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-email"></a>Odeslání e-mailu

Prvním režimem tohoto technického profilu je vygenerování kódu a jeho odeslání. Pro tento režim lze nakonfigurovat následující možnosti.

### <a name="input-claims"></a>Vstupní deklarace identity

Element **InputClaims** obsahuje seznam deklarací, které se mají odeslat do Azure AD SSPR. Název vaší deklarace identity můžete také namapovat na název definovaný v SSPR Technical Profile.

| ClaimReferenceId | Povinné | Popis |
| --------- | -------- | ----------- |
| emailAddress | Yes | Identifikátor uživatele, který vlastní e-mailovou adresu. `PartnerClaimType`Vlastnost vstupní deklarace identity musí být nastavená na `emailAddress` . |


Element **InputClaimsTransformations** může obsahovat kolekci prvků **InputClaimsTransformation** , které se používají k úpravě vstupních deklarací identity nebo k vygenerování nových dat před odesláním do služby Azure AD SSPR.

### <a name="output-claims"></a>Deklarace výstupů

Poskytovatel protokolu Azure AD SSPR nevrací žádné **OutputClaims**, takže není nutné zadávat deklarace výstupů. Můžete ale zahrnout deklarace identity, které nevrací poskytovatel protokolu Azure AD SSPR, pokud nastavíte `DefaultValue` atribut.

Element **OutputClaimsTransformations** může obsahovat kolekci prvků **OutputClaimsTransformation** , které se používají k úpravě výstupních deklarací identity nebo k vygenerování nových.

### <a name="metadata"></a>Metadata

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Operace | Yes | Musí být **SendCode**.  |

#### <a name="ui-elements"></a>Prvky uživatelského rozhraní

Následující metadata lze použít ke konfiguraci chybových zpráv zobrazených při odesílání selhání serveru SMS. Metadata by měla být nakonfigurovaná v technickém profilu s [vlastním kontrolním](self-asserted-technical-profile.md) výrazem. Chybové zprávy lze [lokalizovat](localization-string-ids.md#azure-ad-sspr).

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| UserMessageIfInternalError | No | Chybová zpráva uživatele v případě, že došlo k vnitřní chybě serveru |
| UserMessageIfThrottled| No | Chybová zpráva uživatele, pokud byl požadavek omezen.|


### <a name="example-send-an-email"></a>Příklad: odeslání e-mailu

Následující příklad ukazuje technický profil Azure AD SSPR, který se používá k posílání kódu prostřednictvím e-mailu.

```XML
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">SendCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Ověřit kód

Druhým režimem tohoto technického profilu je ověření kódu. Pro tento režim lze nakonfigurovat následující možnosti.

### <a name="input-claims"></a>Vstupní deklarace identity

Element **InputClaims** obsahuje seznam deklarací, které se mají odeslat do Azure AD SSPR. Název vaší deklarace identity můžete také namapovat na název definovaný v SSPR Technical Profile.

| ClaimReferenceId | Povinné | Popis |
| --------- | -------- | ----------- | ----------- |
| emailAddress| Yes | Stejná e-mailová adresa, jakou jste použili k odeslání kódu. Používá se také k vyhledání relace ověřování e-mailem. `PartnerClaimType`Vlastnost vstupní deklarace identity musí být nastavená na `emailAddress` .|
| verificationCode  | Yes | Ověřovací kód poskytnutý uživatelem, který se má ověřit `PartnerClaimType`Vlastnost vstupní deklarace identity musí být nastavená na `verificationCode` . |

Element **InputClaimsTransformations** může obsahovat kolekci prvků **InputClaimsTransformation** , které se používají k úpravě vstupních deklarací identity nebo k vygenerování nových před VOLÁNÍM služby Azure AD SSPR.

### <a name="output-claims"></a>Deklarace výstupů

Poskytovatel protokolu Azure AD SSPR nevrací žádné **OutputClaims**, takže není nutné zadávat deklarace výstupů. Můžete ale zahrnout deklarace identity, které nevrací poskytovatel protokolu Azure AD SSPR, pokud nastavíte `DefaultValue` atribut.

Element **OutputClaimsTransformations** může obsahovat kolekci prvků **OutputClaimsTransformation** , které se používají k úpravě výstupních deklarací identity nebo k vygenerování nových.

### <a name="metadata"></a>Metadata

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Operace | Yes | Musí být **VerifyCode** |

#### <a name="ui-elements"></a>Prvky uživatelského rozhraní

Následující metadata lze použít ke konfiguraci chybových zpráv zobrazených při selhání ověřování kódu. Metadata by měla být nakonfigurovaná v technickém profilu s [vlastním kontrolním](self-asserted-technical-profile.md) výrazem. Chybové zprávy lze [lokalizovat](localization-string-ids.md#azure-ad-sspr).

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
|UserMessageIfChallengeExpired | Zpráva, která se zobrazí uživateli, pokud vypršela platnost relace ověření kódu Buď platnost kódu vypršela, nebo kód nebyl nikdy vygenerován pro daný identifikátor.|
|UserMessageIfInternalError | Chybová zpráva uživatele v případě, že došlo k vnitřní chybě serveru|
|UserMessageIfThrottled | Chybová zpráva uživatele, pokud byl požadavek omezen.|
|UserMessageIfVerificationFailedNoRetry | Zpráva, která se zobrazí uživateli, pokud jim byl zadán neplatný kód a uživatel není povolen k zadání správného kódu.|
|UserMessageIfVerificationFailedRetryAllowed | Zpráva, která se zobrazí uživateli, pokud zadala neplatný kód a uživatel je oprávněn poskytnout správný kód.|

### <a name="example-verify-a-code"></a>Příklad: ověření kódu

Následující příklad ukazuje technický profil služby Azure AD SSPR, který slouží k ověření kódu.

```XML
<TechnicalProfile Id="AadSspr-VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="verificationCode" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```
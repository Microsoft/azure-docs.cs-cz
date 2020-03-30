---
title: Technické profily Azure MFA ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Odkaz na vlastní zásady pro technické profily Azure Multi-Factor Authentication (MFA) v Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c9ed0e329b498112feafaf21c34e85ea436cbb77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332808"
---
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definování technického profilu Azure MFA ve vlastních zásadách Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) poskytuje podporu pro ověření telefonního čísla pomocí Azure Multi-Factor Authentication (MFA). Tento technický profil slouží ke generování a odeslání kódu na telefonní číslo a následnéověření kódu. Technický profil Azure MFA může také vrátit chybovou zprávu.  Technický profil ověření ověří data zajišťovaná uživatelem před pokračováním cesty uživatele. S technickým profilem ověření se na stránce s vlastním uplatněním zobrazí chybová zpráva.

Tento technický profil:

- Neposkytuje rozhraní pro interakci s uživatelem. Místo toho je uživatelské rozhraní voláno z [vlastního technického](self-asserted-technical-profile.md) profilu nebo [ovládacího prvku zobrazení](display-controls.md) jako [technického profilu ověření](validation-technical-profile.md).
- Používá službu Azure MFA ke generování a odeslání kódu na telefonní číslo a potom kód ověří.  
- Ověří telefonní číslo prostřednictvím textových zpráv.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocol (Protokol)

**Atribut Name** elementu **Protocol** musí být `Proprietary`nastaven na . Atribut **obslužné rutiny** musí obsahovat plně kvalifikovaný název sestavení obslužné rutiny protokolu, který používá Azure AD B2C:

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

Prvním režimem tohoto technického profilu je vygenerovat kód a odeslat jej. Pro tento režim lze nakonfigurovat následující možnosti.

### <a name="input-claims"></a>Vstupní deklarace

**InputClaims** element obsahuje seznam deklarací identity k odeslání do Azure MFA. Název deklarace aplikace můžete také namapovat na název definovaný v technickém profilu vícefaktorové analýzy.

| ClaimReferenceId | Požaduje se | Popis |
| --------- | -------- | ----------- |
| userPrincipalName (Hlavní název uživatele) | Ano | Identifikátor uživatele, který vlastní telefonní číslo. |
| Phonenumber | Ano | Telefonní číslo, na které chcete odeslat SMS kód. |
| Companyname | Ne |Název společnosti v SMS. Pokud není k dispozici, název aplikace se používá. |
| locale | Ne | Národní prostředí SMS. Pokud není k dispozici, použije se národní prostředí prohlížeče uživatele. |

**InputClaimsTransformations** Element může obsahovat kolekci **InputClaimsTransformation** prvky, které se používají k úpravě vstupní deklarace identity nebo generovat nové před odesláním do služby Azure MFA.

### <a name="output-claims"></a>Výstupní pohledávky

Zprostředkovatel protokolu Azure MFA nevrací žádné **OutputClaims**, proto není nutné zadávat výstupní deklarace identity. Můžete však zahrnout deklarace identity, které nejsou vráceny zprostředkovatelem identity `DefaultValue` Azure MFA tak dlouho, dokud nastavíte atribut.

**OutputClaimsTransformations** Element může obsahovat kolekci **OutputClaimsTransformation** prvky, které se používají k úpravě výstupní deklarace nebo generovat nové.

### <a name="metadata"></a>Metadata

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Operace | Ano | Musí být **OneWaySMS**.  |

#### <a name="ui-elements"></a>Prvky uživatelského rozhraní

Následující metadata lze použít ke konfiguraci chybových zpráv zobrazených při odesílání selhání SMS. Metadata by měla být konfigurována v [samoobslužném technickém](self-asserted-technical-profile.md) profilu. Chybové zprávy lze [lokalizovat](localization-string-ids.md#azure-mfa-error-messages).

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Uživatelská zprávaIfCouldntSendSms | Ne | Chybová zpráva uživatele, pokud poskytnuté telefonní číslo nepřijímá sms. |
| UživatelMessageIfInvalidFormat | Ne | Chybová zpráva uživatele, pokud poskytnuté telefonní číslo není platné telefonní číslo. |
| Chyba UserMessageIfServer | Ne | Chybová zpráva uživatele, pokud server zjistil vnitřní chybu. |
| UserMessageIfThrottled| Ne | Chybová zpráva uživatele, pokud byl požadavek omezen.|

### <a name="example-send-an-sms"></a>Příklad: odeslání SMS

Následující příklad ukazuje technický profil Azure MFA, který se používá k odeslání kódu prostřednictvím SMS.

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

### <a name="input-claims"></a>Vstupní deklarace

**InputClaims** element obsahuje seznam deklarací identity k odeslání do Azure MFA. Název deklarace aplikace můžete také namapovat na název definovaný v technickém profilu vícefaktorové analýzy.

| ClaimReferenceId | Požaduje se | Popis |
| --------- | -------- | ----------- | ----------- |
| Phonenumber| Ano | Stejné telefonní číslo, které se dříve používalo k odeslání kódu. Používá se také k vyhledání relace ověření telefonu. |
| ověřovací kód  | Ano | Ověřovací kód poskytnutý uživatelem, který má být ověřen |

**InputClaimsTransformations** Element může obsahovat kolekci **InputClaimsTransformation** prvky, které se používají k úpravě vstupní deklarace identity nebo generovat nové před voláním služby Azure MFA.

### <a name="output-claims"></a>Výstupní pohledávky

Zprostředkovatel protokolu Azure MFA nevrací žádné **OutputClaims**, proto není nutné zadávat výstupní deklarace identity. Můžete však zahrnout deklarace identity, které nejsou vráceny zprostředkovatelem identity `DefaultValue` Azure MFA tak dlouho, dokud nastavíte atribut.

**OutputClaimsTransformations** Element může obsahovat kolekci **OutputClaimsTransformation** prvky, které se používají k úpravě výstupní deklarace nebo generovat nové.

### <a name="metadata"></a>Metadata

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Operace | Ano | Musí být **ověřit** |

#### <a name="ui-elements"></a>Prvky uživatelského rozhraní

Následující metadata lze použít ke konfiguraci chybových zpráv zobrazených při selhání ověření kódu. Metadata by měla být konfigurována v [samoobslužném technickém](self-asserted-technical-profile.md) profilu. Chybové zprávy lze [lokalizovat](localization-string-ids.md#azure-mfa-error-messages).

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| UserMessageIfMaxAllowedCodeRetryReached| Ne | Chybová zpráva uživatele, pokud se uživatel pokusil o ověřovací kód příliš mnohokrát. |
| Chyba UserMessageIfServer | Ne | Chybová zpráva uživatele, pokud server zjistil vnitřní chybu. |
| UserMessageIfThrottled| Ne | Chybová zpráva uživatele, pokud je požadavek omezen.|
| Zadaný userMessageIfWrongCode| Ne| Chybová zpráva uživatele, pokud je kód zadaný k ověření chybný.|

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

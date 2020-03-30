---
title: Povolení ověření jednorázového hesla (OTP)
titleSuffix: Azure AD B2C
description: Zjistěte, jak nastavit scénář jednorázovéheslo (OTP) pomocí vlastních zásad Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bd5fed45332c73c633db1137bdc23aea66fd3403
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332780"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definování jednorázového technického profilu hesla ve vlastních zásadách Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) poskytuje podporu pro správu generování a ověřování jednorázové heslo. Pomocí technického profilu vygenerujte kód a tento kód ověřte později.

Technický profil jednorázového hesla může také vrátit chybovou zprávu během ověřování kódu. Navrhněte integraci s jednorázovým heslem pomocí **technického profilu ověření**. Technický profil ověření volá jednorázový technický profil hesla k ověření kódu. Technický profil ověření ověří data zajišťovaná uživatelem před pokračováním cesty uživatele. S technickým profilem ověření se na stránce s vlastním uplatněním zobrazí chybová zpráva.

## <a name="protocol"></a>Protocol (Protokol)

**Atribut Name** elementu **Protocol** musí být `Proprietary`nastaven na . Atribut **obslužné rutiny** musí obsahovat plně kvalifikovaný název sestavení obslužné rutiny protokolu, který používá Azure AD B2C:

```XML
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

Následující příklad ukazuje jednorázový technický profil hesla:

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>Generování kódu

Prvním režimem tohoto technického profilu je generování kódu. Níže jsou uvedeny možnosti, které lze nakonfigurovat pro tento režim.

### <a name="input-claims"></a>Vstupní deklarace

Element **InputClaims** obsahuje seznam deklarací, které jsou nutné k odeslání poskytovateli protokolu s jednorázovým heslem. Název deklarace aplikace můžete také namapovat na název definovaný níže.

| ClaimReferenceId | Požaduje se | Popis |
| --------- | -------- | ----------- |
| identifikátor | Ano | Identifikátor k identifikaci uživatele, který potřebuje ověřit kód později. Běžně se používá jako identifikátor cíle, kam je kód doručován, například e-mailovou adresu nebo telefonní číslo. |

**InputClaimsTransformations** Element může obsahovat kolekci **InputClaimsTransformation** prvky, které se používají k úpravě vstupní deklarace identity nebo generovat nové před odesláním na zprostředkovatele protokolu jednorázové heslo.

### <a name="output-claims"></a>Výstupní pohledávky

**OutputClaims** Element obsahuje seznam deklarací generovaných poskytovatelem protokolu jednorázové heslo. Název deklarace aplikace můžete také namapovat na název definovaný níže.

| ClaimReferenceId | Požaduje se | Popis |
| --------- | -------- | ----------- |
| otpGenerated | Ano | Generovaný kód, jehož relace je spravována Azure AD B2C. |

**OutputClaimsTransformations** Element může obsahovat kolekci **OutputClaimsTransformation** prvky, které se používají k úpravě výstupní deklarace nebo generovat nové.

### <a name="metadata"></a>Metadata

Ke konfiguraci režimu generování kódu lze použít následující nastavení:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| CodeexpirationInseconds | Ne | Čas v sekundách do vypršení platnosti kódu. Minimum: `60`; Maximální `1200`počet: ; Výchozí `600`nastavení: . |
| CodeLength | Ne | Délka kódu. Výchozí hodnota je `6`. |
| Znaková sada | Ne | Znaková sada pro kód formátovaná pro použití v regulárním výrazu. Například, `a-z0-9A-Z`. Výchozí hodnota je `0-9`. Znaková sada musí obsahovat minimálně 10 různých znaků v zadané sadě. |
| Pokusy numretry | Ne | Počet pokusů o ověření před kód je považován za neplatný. Výchozí hodnota je `5`. |
| Operace | Ano | Operace, která má být provedena. Možná hodnota: `GenerateCode`. |
| Opětovné použitíSameCode | Ne | Zda duplicitní kód by měl být uveden spíše než generování nového kódu, pokud daný kód nevypršela a je stále platný. Výchozí hodnota je `false`. |

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

### <a name="input-claims"></a>Vstupní deklarace

Element **InputClaims** obsahuje seznam deklarací, které jsou nutné k odeslání poskytovateli protokolu s jednorázovým heslem. Název deklarace aplikace můžete také namapovat na název definovaný níže.

| ClaimReferenceId | Požaduje se | Popis |
| --------- | -------- | ----------- |
| identifikátor | Ano | Identifikátor k identifikaci uživatele, který dříve vygeneroval kód. Běžně se používá jako identifikátor cíle, kam je kód doručován, například e-mailovou adresu nebo telefonní číslo. |
| otpChcete-ověřit | Ano | Ověřovací kód poskytnutý uživatelem. |

**InputClaimsTransformations** Element může obsahovat kolekci **InputClaimsTransformation** prvky, které se používají k úpravě vstupní deklarace identity nebo generovat nové před odesláním na zprostředkovatele protokolu jednorázové heslo.

### <a name="output-claims"></a>Výstupní pohledávky

Neexistují žádné výstupní deklarace identity poskytované během ověřování kódu tohoto poskytovatele protokolu.

**OutputClaimsTransformations** Element může obsahovat kolekci **OutputClaimsTransformation** prvky, které se používají k úpravě výstupní deklarace nebo generovat nové.

### <a name="metadata"></a>Metadata

Pro režim ověření kódu lze použít následující nastavení:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Operace | Ano | Operace, která má být provedena. Možná hodnota: `VerifyCode`. |


### <a name="ui-elements"></a>Prvky uživatelského rozhraní

Následující metadata lze použít ke konfiguraci chybových zpráv zobrazených při selhání ověření kódu. Metadata by měla být konfigurována v [samoobslužném technickém](self-asserted-technical-profile.md) profilu. Chybové zprávy lze [lokalizovat](localization-string-ids.md#one-time-password-error-messages).

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Uživatel MessageIfSessionDoesNotExist | Ne | Zpráva, která se má uživateli zobrazit, pokud vypršela platnost relace ověření kódu. Je buď kód vypršela nebo kód nebyl nikdy vygenerován pro daný identifikátor. |
| UserMessageIfMaxRetryAttempted | Ne | Zpráva, která se zobrazí uživateli, pokud překročil maximální povolený pokus o ověření. |
| UserMessageIfInvalidCode | Ne | Zpráva, která se zobrazí uživateli, pokud zadali neplatný kód. |
|Konflikt userMessageIfSessionConflict|Ne| Zpráva, která se má uživateli zobrazit, pokud kód nelze ověřit.|

### <a name="example"></a>Příklad

Následující příklad `TechnicalProfile` se používá k ověření kódu:

```XML
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

V následujícím článku najdete například použití jednorázového technického profilu hesla s vlastním ověřením e-mailu:

- [Vlastní ověření e-mailu ve službě Azure Active Directory B2C](custom-email.md)


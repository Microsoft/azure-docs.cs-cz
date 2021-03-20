---
title: Definování technického profilu pro telefonní faktor v vlastní zásadě
titleSuffix: Azure AD B2C
description: Definujte technický profil pro telefonní faktor ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 322e4b78fbfb38f1822fb7a7cdcdbfcc0738b303
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91950393"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu pro telefonní faktor v Azure Active Directory B2C vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) poskytuje podporu pro registraci a ověření telefonních čísel. Tento technický profil:

- Poskytuje uživatelské rozhraní pro interakci s uživatelem k ověření nebo zaregistrování telefonního čísla.
- Podporuje telefonní hovory a textové zprávy, aby bylo možné ověřit telefonní číslo.
- Podporuje několik telefonních čísel. Uživatel může vybrat jednu ze telefonních čísel, která se má ověřit.  
- Vrátí deklaraci identity, která označuje, jestli uživatel zadal nové telefonní číslo. Pomocí této deklarace identity se můžete rozhodnout, jestli se má telefonní číslo zachovat na Azure AD B2C profil uživatele.  
- Pomocí [definice obsahu](contentdefinitions.md) ovládá vzhled a chování.

## <a name="protocol"></a>Protokol

Atribut **Name** elementu **Protocol** musí být nastaven na hodnotu `Proprietary` . Atribut **obslužné rutiny** musí obsahovat plně kvalifikovaný název sestavení obslužné rutiny protokolu, které Azure AD B2C používá pro telefonní faktor: `Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

Následující příklad ukazuje technický profil telefonního faktoru pro zápis a ověření:

```xml
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims-transformations"></a>Transformace vstupních deklarací identity

Element InputClaimsTransformations může obsahovat kolekci vstupních transformací deklarací identity, které se používají k úpravě vstupních deklarací, nebo generování nových. Následující vstupní transformace deklarací generuje `UserId` deklaraci identity, která se používá později ve vstupní kolekci deklarací.

```xml
<InputClaimsTransformations>
  <InputClaimsTransformation ReferenceId="CreateUserIdForMFA" />
</InputClaimsTransformations>
```

## <a name="input-claims"></a>Vstupní deklarace identity

Element InputClaims musí obsahovat následující deklarace identity. Název vaší deklarace identity můžete také namapovat na název definovaný v technickém profilu pro telefonní faktor. 

|  Datový typ| Povinné | Description |
| --------- | -------- | ----------- | 
| řetězec| Yes | Jedinečný identifikátor pro uživatele. Název deklarace identity nebo PartnerClaimType musí být nastaven na hodnotu `UserId` . Tato deklarace identity by neměla obsahovat osobní údaje, které by se mohly identifikovat.|
| řetězec| Yes | Seznam typů deklarací. Každá deklarace identity obsahuje jedno telefonní číslo. Pokud jakákoli vstupní deklarace identity neobsahuje telefonní číslo, bude uživatel vyzván k registraci a ověření nového telefonního čísla. Ověřené telefonní číslo se vrátí jako výstupní deklarace. Pokud jedna ze vstupních deklarací identity obsahuje telefonní číslo, zobrazí se uživateli výzva k jeho ověření. Pokud více vstupních deklarací obsahuje telefonní číslo, zobrazí se uživateli výzva k výběru a ověření jednoho z telefonních čísel. |

Následující příklad ukazuje použití více telefonních čísel. Další informace najdete v tématu [Vzorová zásada](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa).

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

## <a name="output-claims"></a>Deklarace výstupů

Element OutputClaims obsahuje seznam deklarací vrácených technickým profilem telefonního faktoru.

|  Datový typ| Povinné | Popis |
|  -------- | ----------- |----------- |
| boolean | Yes | Určuje, zda uživatel zadal nové telefonní číslo. Název deklarace identity nebo PartnerClaimType musí být nastaven na. `newPhoneNumberEntered`|
| řetězec| Yes | Ověřené telefonní číslo. Název deklarace identity nebo PartnerClaimType musí být nastaven na hodnotu `Verified.OfficePhone` .|

Element OutputClaimsTransformations může obsahovat kolekci prvků OutputClaimsTransformation, které se používají k úpravě výstupních deklarací, nebo k vygenerování nových.

## <a name="cryptographic-keys"></a>Kryptografické klíče

Element **CryptographicKeys** se nepoužívá.


## <a name="metadata"></a>Metadata

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | Yes | Identifikátor [definice obsahu](contentdefinitions.md) přidruženého k tomuto technickému profilu. |
| ManualPhoneNumberEntryAllowed| No | Určuje, jestli uživatel smí ručně zadat telefonní číslo. Možné hodnoty: `true` , nebo `false` (výchozí).|
| nastavení. authenticationMode | No | Metoda pro ověření telefonního čísla. Možné hodnoty: `sms` , `phone` , nebo `mixed` (výchozí).|
| nastavení. Autodial| No| Určete, zda má technický profil automaticky vytočit nebo automaticky odeslat zprávu SMS. Možné hodnoty: `true` , nebo `false` (výchozí). Automatické vytáčení vyžaduje `setting.authenticationMode` , aby byla metadata nastavena na `sms` , nebo `phone` . Vstupní kolekce deklarací musí obsahovat jedno telefonní číslo. |

### <a name="ui-elements"></a>Prvky uživatelského rozhraní

Prvky uživatelského rozhraní stránky pro ověření telefonního faktoru lze [lokalizovat](localization-string-ids.md#phone-factor-authentication-page-user-interface-elements).

## <a name="next-steps"></a>Další kroky

- Prohlédněte si [sociální a místní účty pomocí MFA](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) Starter Pack.

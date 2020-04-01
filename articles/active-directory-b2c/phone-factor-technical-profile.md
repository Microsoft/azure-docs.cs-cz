---
title: Definování technického profilu faktoru telefonu ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Ve vlastní chodnícím počítači Azure Active Directory B2C definujte technický profil faktoru telefonu.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e2b30e8f6bcbe7c0e739455f4942712f68ff8404
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437452"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu faktoru telefonu ve vlastních zásadách služby Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) poskytuje podporu pro registraci a ověřování telefonních čísel. Tento technický profil:

- Poskytuje uživatelské rozhraní pro interakci s uživatelem k ověření nebo registraci telefonního čísla.
- Podporuje telefonní hovory a textové zprávy k ověření telefonního čísla.
- Podporuje více telefonních čísel. Uživatel může vybrat jedno z telefonních čísel, které má být ověřováno.  
- Vrátí deklaraci označující, zda uživatel zadali nové telefonní číslo. Toto tvrzení můžete použít k rozhodnutí, zda má být telefonní číslo trvalé do uživatelského profilu Azure AD B2C.  
- Používá [definici obsahu](contentdefinitions.md) k ovládání vzhledu a chování.

## <a name="protocol"></a>Protocol (Protokol)

**Atribut Name** elementu **Protocol** musí být `Proprietary`nastaven na . Atribut **obslužné rutiny** musí obsahovat plně kvalifikovaný název sestavení obslužné rutiny protokolu, který používá Azure AD B2C pro faktor telefonu:`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

Následující příklad ukazuje technický profil faktoru telefonu pro registraci a ověření:

```XML
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims-transformations"></a>Vstupní deklarace identity transformace

InputClaimsTransformations Element může obsahovat kolekci vstupní deklarace transformace, které se používají k úpravě vstupní deklarace nebo generovat nové. Následující vstupní deklarace transformace `UserId` generuje deklarace, která se používá později v kolekci vstupních deklarací.

```xml
<InputClaimsTransformations>
  <InputClaimsTransformation ReferenceId="CreateUserIdForMFA" />
</InputClaimsTransformations>
```

## <a name="input-claims"></a>Vstupní deklarace

InputClaims Element musí obsahovat následující deklarace identity. Název nároku můžete také namapovat na název definovaný v technickém profilu faktoru telefonu. 

|  Datový typ| Požaduje se | Popis |
| --------- | -------- | ----------- | 
| řetězec| Ano | Jedinečný identifikátor uživatele. Název deklarace nebo PartnerClaimType musí `UserId`být nastavena na . Toto tvrzení by nemělo obsahovat osobní identifikovatelné informace.|
| řetězec| Ano | Seznam typů deklarací. Každá deklarace obsahuje jedno telefonní číslo. Pokud některý ze vstupních deklarací neobsahuje telefonní číslo, bude uživatel požádán o registraci a ověření nového telefonního čísla. Ověřené telefonní číslo je vráceno jako výstupní deklarace. Pokud jeden ze vstupních deklarací obsahuje telefonní číslo, je uživatel požádán o jeho ověření. Pokud více vstupních deklarací obsahuje telefonní číslo, je uživatel vyzván k výběru a ověření jednoho z telefonních čísel. |

Následující příklad ukazuje použití více telefonních čísel. Další informace naleznete v [tématu ukázkové zásady](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa).

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

## <a name="output-claims"></a>Výstupní pohledávky

OutputClaims Prvek obsahuje seznam deklarací vrácených technický profil faktor u telefonu.

|  Datový typ| Požaduje se | Popis |
|  -------- | ----------- |----------- |
| Boolean | Ano | Označuje, zda byl uživatel zadán nové telefonní číslo. Název deklarace nebo PartnerClaimType musí být nastaven na`newPhoneNumberEntered`|
| řetězec| Ano | Ověřené telefonní číslo. Název deklarace nebo PartnerClaimType musí `Verified.OfficePhone`být nastavena na .|

OutputClaimsTransformations Element může obsahovat kolekci OutputClaimsTransformation prvky, které se používají k úpravě výstupní deklarace nebo generovat nové.

## <a name="cryptographic-keys"></a>Kryptografické klíče

Prvek **CryptographicKeys** se nepoužívá.


## <a name="metadata"></a>Metadata

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | Ano | Identifikátor definice [obsahu](contentdefinitions.md) přidružené k tomuto technickému profilu. |
| ManualPhoneNumberEntryAllowed| Ne | Určete, zda má uživatel povoleno ručně zadat telefonní číslo. Možné hodnoty: `true` `false` , nebo (výchozí).|
| setting.authenticationMode | Ne | Metoda ověření telefonního čísla. Možné `sms`hodnoty: `phone`, `mixed` , nebo (výchozí).|
| nastavení.automatické vytáčení| Ne| Určete, zda má technický profil automaticky vytočit nebo automaticky odeslat SMS. Možné hodnoty: `true` `false` , nebo (výchozí). Automatické vytáčení vyžaduje, `setting.authenticationMode` aby metadata byla nastavena na `sms`. nebo `phone`. Vstupní deklarace kolekce musí mít jedno telefonní číslo. |

### <a name="ui-elements"></a>Prvky uživatelského rozhraní

Prvky uživatelského rozhraní stránky ověřování faktoru telefonu lze [lokalizovat](localization-string-ids.md#azure-mfa-error-messages).

## <a name="next-steps"></a>Další kroky

- Zkontrolujte [sociální a místní účty se startovacím balíčkem Vícefaktorové](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) osnovy.

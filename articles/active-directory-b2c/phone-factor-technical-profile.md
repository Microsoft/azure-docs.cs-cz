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
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3a0511a19477f3d76baf9c453316c5348cc31397
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332656"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu faktoru telefonu ve vlastních zásadách služby Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) poskytuje podporu pro registraci a ověřování telefonních čísel. Tento technický profil:

- Poskytuje uživatelské rozhraní pro interakci s uživatelem.
- Používá definici obsahu k řízení vzhledu a chování.
- Podporuje telefonní hovory i textové zprávy pro ověření telefonního čísla.
- Podporuje více telefonních čísel. Uživatel může vybrat jedno z telefonních čísel, které má být ověřováno.  
- Pokud je k dispozici telefonní číslo, uživatelské rozhraní faktoru telefonu požádá uživatele o ověření telefonního čísla. Pokud není k dispozici, požádá uživatele o registraci nového telefonního čísla.
- Vrátí deklaraci označující, zda uživatel zadali nové telefonní číslo. Toto tvrzení můžete použít k rozhodnutí, zda má být telefonní číslo trvalé do uživatelského profilu Azure AD.  

## <a name="protocol"></a>Protocol (Protokol)

**Atribut Name** elementu **Protocol** musí být `Proprietary`nastaven na . Atribut **obslužné rutiny** musí obsahovat plně kvalifikovaný název sestavení obslužné rutiny protokolu, který používá Azure AD B2C pro faktor telefonu:`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

Následující příklad ukazuje technický profil faktoru telefonu pro registraci a ověření:

```XML
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Vstupní deklarace

InputClaims Element musí obsahovat následující deklarace identity. Název nároku můžete také namapovat na název definovaný v technickém profilu faktoru telefonu. 

```XML
<InputClaims>
  <!--A unique identifier of the user. The partner claim type must be set to `UserId`. -->
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <!--A claim that contains the phone number. If the claim is empty, Azure AD B2C asks the user to enroll a new phone number. Otherwise, it asks the user to verify the phone number. -->
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
</InputClaims>
```

Následující příklad ukazuje použití více telefonních čísel. Další informace naleznete v [tématu ukázkové zásady](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa).

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

InputClaimsTransformations Element může obsahovat kolekci InputClaimsTransformation prvky, které se používají k úpravě vstupní deklarace nebo generovat nové před jejich předložením na stránku faktor telefonu.

## <a name="output-claims"></a>Výstupní pohledávky

OutputClaims Prvek obsahuje seznam deklarací vrácených technický profil faktor u telefonu.

```xml
<OutputClaims>
  <!-- The verified phone number. The partner claim type must be set to `Verified.OfficePhone`. -->
  <OutputClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="Verified.OfficePhone" />
  <!-- Indicates whether the new phone number has been entered by the user. The partner claim type must be set to `newPhoneNumberEntered`. -->
  <OutputClaim ClaimTypeReferenceId="newPhoneNumberEntered" PartnerClaimType="newPhoneNumberEntered" />
</OutputClaims>
```

OutputClaimsTransformations Element může obsahovat kolekci OutputClaimsTransformation prvky, které se používají k úpravě výstupní deklarace nebo generovat nové.

## <a name="cryptographic-keys"></a>Kryptografické klíče

Prvek **CryptographicKeys** se nepoužívá.


## <a name="metadata"></a>Metadata

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | Ano | Identifikátor definice [obsahu](contentdefinitions.md) přidružené k tomuto technickému profilu. |
| ManualPhoneNumberEntryAllowed| Ne | Určete, zda má uživatel povoleno ručně zadat telefonní číslo. Možné `true` hodnoty: `false` nebo (výchozí).|

### <a name="ui-elements"></a>Prvky uživatelského rozhraní

Prvky uživatelského rozhraní stránky ověřování faktoru telefonu lze [lokalizovat](localization-string-ids.md#azure-mfa-error-messages).

## <a name="next-steps"></a>Další kroky

- Zkontrolujte [sociální a místní účty se startovacím balíčkem Vícefaktorové](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) osnovy.


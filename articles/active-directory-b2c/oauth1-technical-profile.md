---
title: Definování technického profilu OAuth1 ve vlastních zásadách
titleSuffix: Azure AD B2C
description: V Azure Active Directory B2C ve vlastních zásadách definujte technický profil OAuth 1,0.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7a2cbca8c02dcbfc0e59d31040d1fca7a790cd35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96936654"
---
# <a name="define-an-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu OAuth1 v Azure Active Directory B2C vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) poskytuje podporu pro poskytovatele identity [protokolu OAuth 1,0](https://tools.ietf.org/html/rfc5849) . Tento článek popisuje konkrétní technické profily pro interakci se zprostředkovatelem deklarací, který podporuje tento standardizovaný protokol. S technickým profilem OAuth1 můžete federovat s poskytovatelem identity založeném na OAuth1, jako je třeba Twitter. Federování se zprostředkovatelem identity umožňuje uživatelům přihlašovat se pomocí jejich stávajících sociálních nebo podnikových identit.

## <a name="protocol"></a>Protokol

Atribut **Name** elementu **Protocol** musí být nastaven na hodnotu `OAuth1` . Například protokol pro technický profil **Twitter-OAUTH1** je `OAuth1` .

```xml
<TechnicalProfile Id="Twitter-OAUTH1">
  <DisplayName>Twitter</DisplayName>
  <Protocol Name="OAuth1" />
  ...
```

## <a name="input-claims"></a>Vstupní deklarace identity

Prvky **InputClaims** a **InputClaimsTransformations**  jsou prázdné nebo chybí.

## <a name="output-claims"></a>Deklarace výstupů

Element **OutputClaims** obsahuje seznam deklarací identity vrácených zprostředkovatelem identity OAuth1. Možná budete muset namapovat název deklarace identity definované v zásadě na název definovaný v poskytovateli identity. Můžete také zahrnout deklarace identity, které nevrací poskytovatel identity, pokud nastavíte atribut **DefaultValue** .

Element **OutputClaimsTransformations** může obsahovat kolekci prvků **OutputClaimsTransformation** , které se používají k úpravě výstupních deklarací identity nebo k vygenerování nových.

Následující příklad ukazuje deklarace identity vrácené zprostředkovatelem identity Twitteru:

- Deklarace **user_id** , která je namapovaná na deklaraci identity **issuerUserId**
- Deklarace **screen_name** , která je namapovaná na deklaraci **DisplayName** .
- Deklarace **e-mailu** bez mapování názvů

Technický profil také vrací deklarace identity, které nejsou vráceny zprostředkovatelem identity:

- Deklarace identity **identityProvider** , která obsahuje název poskytovatele identity.
- Deklarace identity **authenticationSource** s výchozí hodnotou `socialIdpAuthentication` .

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| client_id | Yes | Identifikátor aplikace zprostředkovatele identity |
| ProviderName | No | Název zprostředkovatele identity |
| request_token_endpoint | Yes | Adresa URL koncového bodu tokenu žádosti na základě RFC 5849. |
| authorization_endpoint | Yes | Adresa URL koncového bodu autorizace na základě RFC 5849. |
| access_token_endpoint | Yes | Adresa URL koncového bodu tokenu na základě RFC 5849. |
| ClaimsEndpoint | No | Adresa URL koncového bodu informací o uživateli |
| ClaimsResponseFormat | No | Formát odpovědi na deklarace identity.|

## <a name="cryptographic-keys"></a>Kryptografické klíče

Element **CryptographicKeys** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| client_secret | Yes | Tajný kód klienta aplikace zprostředkovatele identity.   |

## <a name="redirect-uri"></a>Identifikátor URI pro přesměrování

Když nakonfigurujete identifikátor URI pro přesměrování poskytovatele identity, zadejte `https://{tenant-name}.b2clogin.com/{tenant-name}.onmicrosoft.com/{policy-id}/oauth1/authresp` . Ujistěte `{tenant-name}` se, že jste nahradili názvem vašeho tenanta (například contosob2c) a `{policy-id}` identifikátorem zásady (například b2c_1a_policy). Identifikátor URI pro přesměrování musí být malými písmeny. Přidejte adresu URL pro přesměrování pro všechny zásady, které používají přihlášení zprostředkovatele identity.

Příklady:

- [Přidání Twitteru jako poskytovatele identity OAuth1 pomocí vlastních zásad](identity-provider-twitter.md)

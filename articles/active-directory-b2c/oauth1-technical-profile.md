---
title: Definování technického profilu OAuth1 ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Ve vlastní chodnící služby Azure Active Directory B2C definujte technický profil OAuth 1.0.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7f734844859d44e66bddbc2ddd999659e52f9668
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184073"
---
# <a name="define-an-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu OAuth1 ve vlastních zásadách služby Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) poskytuje podporu pro poskytovatele identity [protokolu OAuth 1.0.](https://tools.ietf.org/html/rfc5849) Tento článek popisuje specifika technického profilu pro interakci s poskytovatelem deklarací identity, který podporuje tento standardizovaný protokol. S technickým profilem OAuth1 můžete federate s poskytovatelem identity založeným na OAuth1, jako je Například Twitter. Federace s poskytovatelem identity umožňuje uživatelům přihlásit se pomocí svých stávajících sociálních nebo podnikových identit.

## <a name="protocol"></a>Protocol (Protokol)

**Atribut Name** elementu **Protocol** musí být `OAuth1`nastaven na . Například protokol pro technický profil **Twitter-OAUTH1** je `OAuth1`.

```XML
<TechnicalProfile Id="Twitter-OAUTH1">
  <DisplayName>Twitter</DisplayName>
  <Protocol Name="OAuth1" />
  ...
```

## <a name="input-claims"></a>Vstupní deklarace

**InputClaims** a **InputClaimsTransformations prvky** jsou prázdné nebo chybí.

## <a name="output-claims"></a>Výstupní pohledávky

**OutputClaims** Element obsahuje seznam deklarací vrácených zprostředkovatelem identity OAuth1. Možná budete muset namapovat název deklarace identity definované v zásadách na název definovaný v poskytovateli identity. Můžete také zahrnout deklarace identity, které nejsou vráceny zprostředkovatelem identity tak dlouho, dokud nastavíte **DefaultValue** atribut.

**OutputClaimsTransformations** Element může obsahovat kolekci **OutputClaimsTransformation** prvky, které se používají k úpravě výstupní deklarace nebo generovat nové.

Následující příklad ukazuje deklarace identity vrácené poskytovatelem identity Twitteru:

- Deklarace **user_id,** která je mapována na deklaraci **issuerUserId.**
- Deklarace **screen_name,** která je mapována na deklaraci **displayName.**
- Deklarace **e-mailu** bez mapování názvu.

Technický profil také vrátí deklarace identity, které nejsou vráceny zprostředkovatelem identity:

- **IdentityProvider** deklarace, která obsahuje název zprostředkovatele identity.
- Deklarace **authenticationSource** s výchozí `socialIdpAuthentication`hodnotou aplikace .

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

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| client_id | Ano | Identifikátor aplikace zprostředkovatele identity. |
| Providername | Ne | Název zprostředkovatele identity. |
| request_token_endpoint | Ano | Adresa URL koncového bodu tokenu požadavku podle rfc 5849. |
| authorization_endpoint | Ano | Adresa URL koncového bodu autorizace podle rfc 5849. |
| access_token_endpoint | Ano | Adresa URL koncového bodu tokenu podle rfc 5849. |
| Konec identity | Ne | Adresa URL koncového bodu informací o uživateli. |
| ClaimsResponseFormat | Ne | Formát odezvy deklarací identity.|

## <a name="cryptographic-keys"></a>Kryptografické klíče

Prvek **CryptographicKeys** obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| client_secret | Ano | Tajný klíč klienta aplikace zprostředkovatele identity.   |

## <a name="redirect-uri"></a>Identifikátor URI pro přesměrování

Při konfiguraci adresy URL přesměrování poskytovatele identity zadejte . `https://login.microsoftonline.com/te/tenant/policyId/oauth1/authresp` Ujistěte se, že nahradit **tenanta** s názvem klienta (například contosob2c.onmicrosoft.com) a **policyId** s identifikátorem zásad (například b2c_1a_policy). Identifikátor URI přesměrování musí být ve všech složených písmenech. Přidejte adresu URL přesměrování pro všechny zásady, které používají přihlášení zprostředkovatele identity.

Pokud používáte **doménu b2clogin.com** místo **login.microsoftonline.com** Ujistěte se, že místo login.microsoftonline.com používáte b2clogin.com.

Příklady:

- [Přidání Twitteru jako zprostředkovatele identity OAuth1 pomocí vlastních zásad](identity-provider-twitter-custom.md)














---
title: Definování technického profilu OAuth2 ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Ve vlastní chodnící služby Azure Active Directory B2C definujte technický profil OAuth2.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 86ec7a5745a58546faf6f0ff15d6dc5f452baa88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184039"
---
# <a name="define-an-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu OAuth2 ve vlastních zásadách služby Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) poskytuje podporu pro poskytovatele identity protokolu OAuth2. OAuth2 je primární protokol pro autorizaci a delegované ověřování. Další informace naleznete v [rfc 6749 OAuth 2.0 Autorizační rámec](https://tools.ietf.org/html/rfc6749). S technickým profilem OAuth2 můžete federate s poskytovatelem identity založeným na OAuth2, jako je Například Facebook. Federace s poskytovatelem identity umožňuje uživatelům přihlásit se pomocí svých stávajících sociálních nebo podnikových identit.

## <a name="protocol"></a>Protocol (Protokol)

**Atribut Name** elementu **Protocol** musí být `OAuth2`nastaven na . Například protokol pro technický profil **Facebook-OAUTH** je `OAuth2`:

```XML
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
  ...
```

## <a name="input-claims"></a>Vstupní deklarace

**InputClaims** a **InputClaimsTransformations prvky nejsou vyžadovány.** Ale můžete chtít odeslat další parametry zprostředkovateli identity. Následující příklad přidá **domain_hint** parametr řetězce dotazu `contoso.com` domain_hint s hodnotou žádosti o autorizaci.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Výstupní pohledávky

**OutputClaims** Element obsahuje seznam deklarací vrácených zprostředkovatelem identity OAuth2. Možná budete muset namapovat název deklarace identity definované v zásadách na název definovaný v poskytovateli identity. Můžete také zahrnout deklarace identity, které nejsou vráceny zprostředkovatelem identity tak dlouho, dokud nastavíte `DefaultValue` atribut.

**OutputClaimsTransformations** Element může obsahovat kolekci **OutputClaimsTransformation** prvky, které se používají k úpravě výstupní deklarace nebo generovat nové.

Následující příklad ukazuje deklarace identity vrácené poskytovatelem identity na Facebooku:

- Deklarace **first_name** je mapována na deklaraci **givenName.**
- Nárok **last_name** je mapován na nárok na **příjmení.**
- Deklarace **displayName** bez mapování názvů.
- Deklarace **e-mailu** bez mapování názvu.

Technický profil také vrátí deklarace identity, které nejsou vráceny zprostředkovatelem identity:

- **IdentityProvider** deklarace, která obsahuje název zprostředkovatele identity.
- Deklarace **authenticationSource** s výchozí hodnotou **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="facebook.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| client_id | Ano | Identifikátor aplikace zprostředkovatele identity. |
| IdTokenAudience | Ne | Publikum id_token. Pokud je zadán, Azure AD B2C zkontroluje, zda je token v deklaraci vrácené zprostředkovatelem identity a rovná se zadaný. |
| authorization_endpoint | Ano | Adresa URL koncového bodu autorizace podle rfc 6749. |
| Koncový bod tokenu AccessToken | Ano | Adresa URL koncového bodu tokenu podle RFC 6749. |
| Konec identity | Ano | Adresa URL koncového bodu informací o uživateli podle rfc 6749. |
| Formát AccessTokenResponseFormat | Ne | Formát volání koncového bodu tokenu přístupu. Facebook například vyžaduje metodu HTTP GET, ale odpověď tokenu přístupu je ve formátu JSON. |
| Parametry AdditionalRequestQueryParameters | Ne | Další parametry dotazu požadavku. Můžete například odeslat další parametry poskytovateli identity. Pomocí oddělovače čárek můžete zahrnout více parametrů. |
| ClaimsEndpointNameTokenName | Ne | Název parametru řetězce dotazu tokenu přístupu. Koncové body deklarací identity zprostředkovatelů identitpodporují požadavek HTTP GET. V tomto případě je token nosiče odeslán pomocí parametru řetězce dotazu namísto hlavičky autorizace. |
| Název_identity_koncebodu | Ne | Název parametru řetězce dotazu formátu. Můžete například nastavit název `format` jako v tomto koncovém bodu deklarací linkedinu `https://api.linkedin.com/v1/people/~?format=json`. |
| Konec aplikace ClaimsEndpointFormat | Ne | Hodnota parametru řetězce dotazu formátu. Můžete například nastavit hodnotu `json` jako v tomto `https://api.linkedin.com/v1/people/~?format=json`koncovém bodu deklarací linkedinu . |
| Providername | Ne | Název zprostředkovatele identity. |
| response_mode | Ne | Metoda, která zprostředkovatel identity používá k odeslání výsledku zpět do Azure AD B2C. Možné `query`hodnoty: `form_post` , (výchozí) nebo `fragment`. |
| scope | Ne | Rozsah požadavku, který je definován podle specifikace zprostředkovatele identity OAuth2. Například `openid` `profile`, `email`a . |
| HttpBinding | Ne | Očekávaná vazba HTTP pro koncový bod přístupového tokenu a tokenu deklarací identity. Možné `GET` hodnoty: `POST`nebo .  |
| ResponseErrorCodeParamName | Ne | Název parametru, který obsahuje chybovou zprávu vrácenou přes HTTP 200 (Ok). |
| ExtraParamsInAccessTokenEndpointResponse | Ne | Obsahuje další parametry, které mohou být vráceny v odpovědi z **AccessTokenEndpoint** některými zprostředkovateli identity. Například odpověď z **AccessTokenEndpoint** obsahuje další `openid`parametr, jako je například , což je povinný parametr kromě access_token v řetězec dotazu požadavku **ClaimsEndpoint.** Více názvů parametrů by měl být uvozen a oddělen y oddělovačem čárek .' oddělovač. |
| Požadavek na ukončení koncového bodu ExtraParamsInClaims | Ne | Obsahuje další parametry, které mohou být vráceny v **claimsendpoint** požadavek některých zprostředkovatelů identity. Více názvů parametrů by měl být uvozen a oddělen y oddělovačem čárek .' oddělovač. |
| IncludeClaimResolvingInClaimsHandling  | Ne | Pro vstupní a výstupní deklarace určuje, zda je [řešení deklarací](claim-resolver-overview.md) zahrnuto do technického profilu. Možné hodnoty: `true` `false`  , nebo (výchozí). Pokud chcete použít překladač deklarací identity v `true`technickém profilu, nastavte toto na . |
| VyřešitJsonPathsInJsonTokens  | Ne | Označuje, zda technický profil řeší cesty JSON. Možné hodnoty: `true` `false` , nebo (výchozí). Tato metadata slouží ke čtení dat z vnořeného prvku JSON. V [outputclaim](technicalprofiles.md#outputclaims)nastavte `PartnerClaimType` na prvek cesty JSON, který chcete výstup. Například: `firstName.localized`, `data.0.to.0.email`nebo .|

## <a name="cryptographic-keys"></a>Kryptografické klíče

Prvek **CryptographicKeys** obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| client_secret | Ano | Tajný klíč klienta aplikace zprostředkovatele identity. Kryptografický klíč je vyžadován pouze v `code`případě, že **je** response_types metadata nastavena na . V takovém případě Azure AD B2C provede další volání k výměně autorizační kód pro přístupový token. Pokud je metadata `id_token`nastavena na , můžete kryptografický klíč vynechat. |

## <a name="redirect-uri"></a>Identifikátor URI pro přesměrování

Při konfiguraci adresy URL přesměrování poskytovatele identity zadejte . `https://login.microsoftonline.com/te/tenant/policyId/oauth2/authresp` Ujistěte se, že nahradit **tenanta** s názvem vašeho klienta (například contosob2c.onmicrosoft.com) a **policyId** s identifikátorem zásad (například b2c_1a_policy). Identifikátor URI přesměrování musí být ve všech složených písmenech.

Pokud používáte **doménu b2clogin.com** místo **login.microsoftonline.com** Ujistěte se, že místo login.microsoftonline.com používáte b2clogin.com.

Příklady:

- [Přidání Google+ jako poskytovatele identity OAuth2 pomocí vlastních zásad](identity-provider-google-custom.md)














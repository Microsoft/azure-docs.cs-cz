---
title: Definování technického profilu OAuth2 ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Definujte technický profil OAuth2 ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 35b1f57a2361c5a4360e2ff1944b93e767168799
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91259386"
---
# <a name="define-an-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu OAuth2 v Azure Active Directory B2C vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) poskytuje podporu pro poskytovatele identity protokolu OAuth2. OAuth2 je primární protokol pro autorizaci a delegované ověřování. Další informace najdete v [dokumentu RFC 6749 autorizačního rozhraní OAuth 2,0](https://tools.ietf.org/html/rfc6749). S technickým profilem OAuth2 můžete federovat s poskytovatelem identity založeném na OAuth2, jako je Facebook. Federování se zprostředkovatelem identity umožňuje uživatelům přihlašovat se pomocí jejich stávajících sociálních nebo podnikových identit.

## <a name="protocol"></a>Protokol

Atribut **Name** elementu **Protocol** musí být nastaven na hodnotu `OAuth2` . Například protokol pro technický profil **Facebooku-OAuth** je `OAuth2` :

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
  ...
```

## <a name="input-claims"></a>Vstupní deklarace identity

Prvky **InputClaims** a **InputClaimsTransformations** nejsou požadovány. Je ale možné, že bude poskytovateli identity poslat další parametry. Následující příklad přidá parametr řetězce dotazu **domain_hint** s hodnotou `contoso.com` do autorizační žádosti.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Deklarace výstupů

Element **OutputClaims** obsahuje seznam deklarací identity vrácených zprostředkovatelem identity OAuth2. Možná budete muset namapovat název deklarace identity definované v zásadě na název definovaný v poskytovateli identity. Můžete také zahrnout deklarace identity, které nevrací poskytovatel identity, pokud nastavíte `DefaultValue` atribut.

Element **OutputClaimsTransformations** může obsahovat kolekci prvků **OutputClaimsTransformation** , které se používají k úpravě výstupních deklarací identity nebo k vygenerování nových.

Následující příklad ukazuje deklarace identity, které vrátil poskytovatel identity Facebooku:

- Deklarace **first_name** je namapovaná na **danou** deklaraci identity.
- Deklarace **last_name** je namapována na deklaraci identity na **příjmení** .
- Parametr **DisplayName** deklarace identity bez mapování názvu.
- Deklarace **e-mailu** bez mapování názvů

Technický profil také vrací deklarace identity, které nejsou vráceny zprostředkovatelem identity:

- Deklarace identity **identityProvider** , která obsahuje název poskytovatele identity.
- Deklarace identity **authenticationSource** s výchozí hodnotou **socialIdpAuthentication**.

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

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| client_id | Yes | Identifikátor aplikace zprostředkovatele identity |
| IdTokenAudience | No | Cílová skupina id_token. Když se tato možnost zadá, Azure AD B2C ověří, jestli je token v deklaraci identity vrácené zprostředkovatelem identity a který se rovná zadanému typu. |
| authorization_endpoint | Yes | Adresa URL koncového bodu autorizace na základě RFC 6749. |
| AccessTokenEndpoint | Yes | Adresa URL koncového bodu tokenu na základě RFC 6749. |
| ClaimsEndpoint | Yes | Adresa URL koncového bodu pro informace o uživateli podle RFC 6749. |
| AccessTokenResponseFormat | No | Formát volání koncového bodu přístupového tokenu. Například Facebook vyžaduje metodu HTTP GET, ale odpověď přístupového tokenu je ve formátu JSON. |
| AdditionalRequestQueryParameters | No | Další parametry dotazu žádosti Například může být vhodné odeslat poskytovateli identity další parametry. Můžete zahrnout více parametrů pomocí oddělovače čárky. |
| ClaimsEndpointAccessTokenName | No | Název parametru řetězce dotazu přístupového tokenu. Někteří poskytovatelé deklarací koncových bodů identity podporují požadavek GET HTTP. V takovém případě se nosný token posílá pomocí parametru řetězce dotazu místo hlavičky Authorization. |
| ClaimsEndpointFormatName | No | Název parametru řetězce formátu dotazu. Můžete například nastavit název jako `format` v tomto koncovém bodu deklarací LinkedIn `https://api.linkedin.com/v1/people/~?format=json` . |
| ClaimsEndpointFormat | No | Hodnota parametru řetězce formátu dotazu. Můžete například nastavit hodnotu jako `json` v tomto koncovém bodě deklarací LinkedIn `https://api.linkedin.com/v1/people/~?format=json` . |
| ProviderName | No | Název zprostředkovatele identity |
| response_mode | No | Metoda, kterou zprostředkovatel identity používá k odeslání výsledku zpět do Azure AD B2C. Možné hodnoty: `query` , `form_post` (výchozí) nebo `fragment` . |
| scope | No | Rozsah požadavku, který je definován podle specifikace poskytovatele identity OAuth2. Například `openid` , `profile` a `email` . |
| HttpBinding | No | Očekávaná vazba protokolu HTTP k přístupovému tokenu a koncovým bodům tokenu deklarací identity. Možné hodnoty: `GET` nebo `POST` .  |
| ResponseErrorCodeParamName | No | Název parametru, který obsahuje chybovou zprávu vrácenou přes HTTP 200 (ok). |
| ExtraParamsInAccessTokenEndpointResponse | No | Obsahuje další parametry, které mohou být vráceny v odpovědi od **AccessTokenEndpoint** některými zprostředkovateli identity. Například odpověď z **AccessTokenEndpoint** obsahuje další parametr `openid` , například, což je povinný parametr kromě access_token v řetězci dotazu **ClaimsEndpoint** žádosti. Více názvů parametrů by mělo být uvozeno řídicím znakem a odděleno čárkou, oddělovačem. |
| ExtraParamsInClaimsEndpointRequest | No | Obsahuje další parametry, které mohou být vráceny v žádosti **ClaimsEndpoint** některými zprostředkovateli identity. Více názvů parametrů by mělo být uvozeno řídicím znakem a odděleno čárkou, oddělovačem. |
| IncludeClaimResolvingInClaimsHandling  | No | Pro vstupní a výstupní deklarace identity určuje, jestli je [řešení deklarací identity](claim-resolver-overview.md) zahrnuté v technickém profilu. Možné hodnoty: `true` , nebo `false`   (výchozí). Pokud chcete použít překladač deklarací identity v technickém profilu, nastavte tuto hodnotu na `true` . |
| ResolveJsonPathsInJsonTokens  | No | Určuje, zda technický profil řeší cesty JSON. Možné hodnoty: `true` , nebo `false` (výchozí). Tato metadata slouží ke čtení dat z vnořeného prvku JSON. V [OutputClaim](technicalprofiles.md#outputclaims)nastavte na `PartnerClaimType` element cesty JSON, který chcete výstup. Například: `firstName.localized` nebo `data.0.to.0.email` .|
|token_endpoint_auth_method| No| Určuje způsob, jakým Azure AD B2C odesílá hlavičku ověřování na koncový bod tokenu. Možné hodnoty: `client_secret_post` (výchozí) a `client_secret_basic` (Public Preview). Další informace najdete v [části ověřování klientů OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication). |

## <a name="cryptographic-keys"></a>Kryptografické klíče

Element **CryptographicKeys** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| client_secret | Yes | Tajný kód klienta aplikace zprostředkovatele identity. Kryptografický klíč je vyžadován pouze v případě, že jsou metadata **response_types** nastavena na hodnotu `code` . V takovém případě Azure AD B2C provede další volání výměny autorizačního kódu pro přístupový token. Pokud jsou metadata nastavená na `id_token` , můžete kryptografický klíč vynechat. |

## <a name="redirect-uri"></a>Identifikátor URI pro přesměrování

Když nakonfigurujete identifikátor URI pro přesměrování poskytovatele identity, zadejte `https://{tenant-name}.b2clogin.com/{tenant-name}.onmicrosoft.com/oauth2/authresp` . Ujistěte `{tenant-name}` se, že jste nahradili názvem vašeho tenanta (například contosob2c). Identifikátor URI pro přesměrování musí být malými písmeny.

Příklady:

- [Přidání Google + jako poskytovatele identity OAuth2 pomocí vlastních zásad](identity-provider-google-custom.md)

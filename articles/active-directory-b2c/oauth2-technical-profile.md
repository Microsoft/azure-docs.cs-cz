---
title: Definování technického profilu OAuth2 ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Definujte technický profil OAuth2 ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 53190eda66347c23b981c5d6e0631630e9989deb
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840362"
---
# <a name="define-an-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu OAuth2 v Azure Active Directory B2C vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) poskytuje podporu pro poskytovatele identity protokolu OAuth2. OAuth2 je primární protokol pro autorizaci a delegované ověřování. Další informace najdete v [dokumentu RFC 6749 autorizačního rozhraní OAuth 2,0](https://tools.ietf.org/html/rfc6749). S technickým profilem OAuth2 můžete federovat s poskytovatelem identity založeném na OAuth2, jako je Facebook. Federování se zprostředkovatelem identity umožňuje uživatelům přihlašovat se pomocí jejich stávajících sociálních nebo podnikových identit.

## <a name="protocol"></a>Protocol (Protokol)

Atribut **Name** elementu **Protocol** musí být nastaven na `OAuth2`. Například protokol pro technický profil **Facebooku-OAuth** je `OAuth2`:

```XML
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
  ...
```

## <a name="input-claims"></a>Vstupní deklarace identity

Prvky **InputClaims** a **InputClaimsTransformations** nejsou požadovány. Je ale možné, že bude poskytovateli identity poslat další parametry. Následující příklad přidá parametr řetězce dotazu **domain_hint** s hodnotou `contoso.com` na žádost o autorizaci.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Deklarace výstupů

Element **OutputClaims** obsahuje seznam deklarací identity vrácených zprostředkovatelem identity OAuth2. Možná budete muset namapovat název deklarace identity definované v zásadě na název definovaný v poskytovateli identity. Můžete také zahrnout deklarace identity, které nevrací poskytovatel identity, pokud nastavíte atribut `DefaultValue`.

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

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| client_id | Ano | Identifikátor aplikace zprostředkovatele identity |
| IdTokenAudience | Ne | Cílová skupina id_token. Když se tato možnost zadá, Azure AD B2C ověří, jestli je token v deklaraci identity vrácené zprostředkovatelem identity a který se rovná zadanému typu. |
| authorization_endpoint | Ano | Adresa URL koncového bodu autorizace na základě RFC 6749. |
| AccessTokenEndpoint | Ano | Adresa URL koncového bodu tokenu na základě RFC 6749. |
| ClaimsEndpoint | Ano | Adresa URL koncového bodu pro informace o uživateli podle RFC 6749. |
| AccessTokenResponseFormat | Ne | Formát volání koncového bodu přístupového tokenu. Například Facebook vyžaduje metodu HTTP GET, ale odpověď přístupového tokenu je ve formátu JSON. |
| AdditionalRequestQueryParameters | Ne | Další parametry dotazu žádosti Například může být vhodné odeslat poskytovateli identity další parametry. Můžete zahrnout více parametrů pomocí oddělovače čárky. |
| ClaimsEndpointAccessTokenName | Ne | Název parametru řetězce dotazu přístupového tokenu. Někteří poskytovatelé deklarací koncových bodů identity podporují požadavek GET HTTP. V takovém případě se nosný token posílá pomocí parametru řetězce dotazu místo hlavičky Authorization. |
| ClaimsEndpointFormatName | Ne | Název parametru řetězce formátu dotazu. Název můžete například nastavit jako `format` v tomto koncovém bodu deklarací LinkedIn `https://api.linkedin.com/v1/people/~?format=json`. |
| ClaimsEndpointFormat | Ne | Hodnota parametru řetězce formátu dotazu. Můžete například nastavit hodnotu jako `json` v tomto koncovém bodu deklarací LinkedIn `https://api.linkedin.com/v1/people/~?format=json`. |
| ProviderName | Ne | Název zprostředkovatele identity |
| response_mode | Ne | Metoda, kterou zprostředkovatel identity používá k odeslání výsledku zpět do Azure AD B2C. Možné hodnoty: `query`, `form_post` (výchozí) nebo `fragment`. |
| scope | Ne | Rozsah požadavku, který je definován podle specifikace poskytovatele identity OAuth2. Například `openid`, `profile`a `email`. |
| HttpBinding | Ne | Očekávaná vazba protokolu HTTP k přístupovému tokenu a koncovým bodům tokenu deklarací identity. Možné hodnoty: `GET` nebo `POST`.  |
| ResponseErrorCodeParamName | Ne | Název parametru, který obsahuje chybovou zprávu vrácenou přes HTTP 200 (ok). |
| ExtraParamsInAccessTokenEndpointResponse | Ne | Obsahuje další parametry, které mohou být vráceny v odpovědi od **AccessTokenEndpoint** některými zprostředkovateli identity. Například odpověď z **AccessTokenEndpoint** obsahuje další parametr, jako je například `openid`, což je povinný parametr kromě access_token v řetězci dotazu žádosti **ClaimsEndpoint** . Více názvů parametrů by mělo být uvozeno řídicím znakem a odděleno čárkou, oddělovačem. |
| ExtraParamsInClaimsEndpointRequest | Ne | Obsahuje další parametry, které mohou být vráceny v žádosti **ClaimsEndpoint** některými zprostředkovateli identity. Více názvů parametrů by mělo být uvozeno řídicím znakem a odděleno čárkou, oddělovačem. |

## <a name="cryptographic-keys"></a>Kryptografické klíče

Element **CryptographicKeys** obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| client_secret | Ano | Tajný kód klienta aplikace zprostředkovatele identity. Kryptografický klíč je vyžadován pouze v případě, že jsou metadata **response_types** nastavena na hodnotu `code`. V takovém případě Azure AD B2C provede další volání výměny autorizačního kódu pro přístupový token. Pokud jsou metadata nastavená na `id_token`, můžete kryptografický klíč vynechat. |

## <a name="redirect-uri"></a>Identifikátor URI pro přesměrování

Když nakonfigurujete adresu URL pro přesměrování poskytovatele identity, zadejte `https://login.microsoftonline.com/te/tenant/policyId/oauth2/authresp`. Ujistěte se, že jste **klienta** nahradili názvem vašeho tenanta (například contosob2c.onmicrosoft.com) a **policyId** s identifikátorem zásady (například b2c_1a_policy). Identifikátor URI pro přesměrování musí být malými písmeny.

Pokud používáte doménu **b2clogin.com** místo **Login.microsoftonline.com** , nezapomeňte místo Login.microsoftonline.com použít b2clogin.com.

Příklady:

- [Přidání Google + jako poskytovatele identity OAuth2 pomocí vlastních zásad](identity-provider-google-custom.md)














---
title: Definování profilu technické OAuth2 vlastních zásad v Azure Active Directory B2C | Dokumentace Microsoftu
description: Technický profil OAuth2 definování vlastních zásad v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 86682525ae5246f9335e64e7dd3a812eaaf3c273
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55157716"
---
# <a name="define-a-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Technický profil OAuth2 definování ve vlastních zásadách pro Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C poskytuje podporu pro zprostředkovatele identity protokolu OAuth2. Toto je primární protokol pro delegované ověřování a autorizace. Další informace najdete v tématu [RFC 6749 OAuth 2.0 autorizace rámci](https://tools.ietf.org/html/rfc6749). Technický profil může provést federaci s OAuth2 s OAuth2 podle zprostředkovatele identity, jako je Facebook nebo Live.com, umožňuje uživatelům přihlásit se s jejich existujícími sociálních sítí nebo podnikové identity.

## <a name="protocol"></a>Protocol (Protokol)

**Název** atribut **protokol** elementu musí být nastavena na `OAuth2`. Například protokol **OAUTH pro Facebook** technickém profilu je `OAuth2`:

```XML
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
  ...    
```

## <a name="input-claims"></a>Vstupní deklarace identity

**InputClaims** a **InputClaimsTransformations** prvky nejsou povinné. Ale můžete chtít odeslat další parametry pro zprostředkovatele identity. Následující příklad přidá **domain_hint** parametr řetězce s použitím hodnoty z dotazu `contoso.com` na žádost o autorizaci.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Výstupní deklarace identit.

**OutputClaims** prvek obsahuje seznam deklarací identity vrácený zprostředkovatelem identity OAuth2. Budete muset namapovat název deklarace identity, definovaný ve svojí zásadě název definovaný ve zprostředkovateli identity. Může také obsahovat deklarace identity, které nejsou vrácené zprostředkovatelem identity tak dlouho, dokud jste nastavili `DefaultValue` atribut.

**OutputClaimsTransformations** element může obsahovat kolekci **OutputClaimsTransformation** prvky, které se používají ke změně výstupní deklarace identit nebo generovat nové značky.

Následující příklad ukazuje deklarace vrácené poskytovatelem identity Facebooku:

- **Křestní_jméno** deklarace identity se mapuje na **givenName** deklarací identity.
- **Příjmení** deklarace identity se mapuje na **příjmení** deklarací identity.
- **DisplayName** deklarace identity bez mapování názvů...
- **E-mailu** deklarace identity bez mapování názvů.

Technický profil také vrátí hodnotu deklarace identity, které nejsou vrácené zprostředkovatelem identity: 

- **IdentityProvider** deklarace identity, který obsahuje název zprostředkovatele identity.
- **AuthenticationSource** deklarace identity s výchozí hodnotou **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="facebook.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| client_id | Ano | Identifikátor aplikace zprostředkovatele identity. |
| IdTokenAudience | Ne | Cílová skupina požadavku id_token. Je-li zadána, Azure AD B2C ověří, zda token, který je v deklaraci identity vrácený zprostředkovatelem identity a rovná zadanému. |
| authorization_endpoint | Ano | Adresa URL koncového bodu autorizace podle RFC 6749. |
| AccessTokenEndpoint | Ano | Adresa URL koncového bodu tokenu podle RFC 6749. |  
| ClaimsEndpoint | Ano | Adresa URL koncového bodu uživatelské informace podle RFC 6749. | 
| AccessTokenResponseFormat | Ne | Formát volání koncového bodu tokenu přístupu. Například Facebook vyžaduje metodu GET protokolu HTTP, ale odpovědi tokenu přístupu je ve formátu JSON. |
| AdditionalRequestQueryParameters | Ne | Žádost o další parametry dotazu. Můžete například odeslat další parametry pro zprostředkovatele identity. Může obsahovat více parametrů pomocí oddělovače čárky. | 
| ClaimsEndpointAccessTokenName | Ne | Název parametru řetězce dotazu tokenu přístupu. Koncové body někteří zprostředkovatelé identitu deklarací identity podporují žádosti GET HTTP. V takovém případě nosný token posílá prostřednictvím parametru řetězce dotazu namísto autorizační hlavičky. |
| ClaimsEndpointFormatName | Ne | Název parametru řetězce dotazu formátu. Například můžete nastavit název jako `format` v tomto LinkedIn deklarací koncový bod `https://api.linkedin.com/v1/people/~?format=json`. | 
| ClaimsEndpointFormat | Ne | Hodnota parametru řetězce dotazu formátu. Například můžete nastavit na hodnotu jako hodnota `json` v tomto LinkedIn deklarací koncový bod `https://api.linkedin.com/v1/people/~?format=json`. | 
| ProviderName | Ne | Název zprostředkovatele identity. |
| response_mode | Ne | Metoda, která používá zprostředkovatel identity k odesílání výsledek zpět do Azure AD B2C. Možné hodnoty: `query`, `form_post` (výchozí), nebo `fragment`. |
| scope | Ne | Rozsah žádosti o přístup, definována podle specifikace OAuth2 zprostředkovatele identity. Například `openid`, `profile`, a `email`. |
| HttpBinding | Ne | Očekávané vazby HTTP na token a deklarací identity tokenu koncové body přístupu. Možné hodnoty: `GET` nebo `POST`.  |
| ResponseErrorCodeParamName | Ne | Název parametru, který obsahuje chybovou zprávu vrácenou přes HTTP 200 (Ok). |
| ExtraParamsInAccessTokenEndpointResponse | Ne | Obsahuje další parametry, které se můžete vrátit v odpovědi z **AccessTokenEndpoint** některé poskytovateli identity. Například odpověď z **AccessTokenEndpoint** obsahuje další parametr, například `openid`, což je povinný parametr kromě access_token v **ClaimsEndpoint** dotazu žádosti o řetězec. Více názvy parametrů by být uvozeny řídicími znaky a oddělené čárkou ',' oddělovač. |
| ExtraParamsInClaimsEndpointRequest | Ne | Obsahuje další parametry, které mohou být vráceny v **ClaimsEndpoint** žádost někteří poskytovatelé identity. Více názvy parametrů by být uvozeny řídicími znaky a oddělené čárkou ',' oddělovač. |

## <a name="cryptographic-keys"></a>Kryptografické klíče

**CryptographicKeys** prvek obsahuje následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| client_secret | Ano | Tajný kód klienta aplikace zprostředkovatele identity. Šifrovací klíč je požadován, pouze v případě, **response_types** metadat je nastavena na `code`. V tomto případě Azure AD B2C umožňuje jiného volání k výměně autorizačního kódu pro přístupový token. Pokud metadata nastavená na `id_token` kryptografický klíč můžete vynechat.  |  

## <a name="redirect-uri"></a>Přesměrovat identifikátor URI

Při konfiguraci adresy URL pro přesměrování vašeho zprostředkovatele identity, zadejte `https://login.microsoftonline.com/te/tenant/policyId/oauth2/authresp`. Nezapomeňte nahradit **tenanta** s názvem vašeho tenanta (například contosob2c.onmicrosoft.com) a **policyId** s identifikátorem zásady (například b2c_1a_policy). Identifikátor URI pro přesměrování musí být v všechna písmena malá.

Pokud používáte **b2clogin.com** domény namísto **login.microsoftonline.com** Ujistěte se, že použití b2clogin.com místo login.microsoftonline.com.

Příklady:

- [Přidat služby Google + jako zprostředkovatele identity OAuth2 pomocí vlastních zásad](active-directory-b2c-custom-setup-goog-idp.md)














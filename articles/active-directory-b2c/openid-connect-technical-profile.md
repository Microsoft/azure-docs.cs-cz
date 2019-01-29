---
title: Definování profilu technické OpenId Connect ve vlastních zásadách v Azure Active Directory B2C | Dokumentace Microsoftu
description: Definování profilu technické OpenId Connect ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 730d32fd5b9451c2b4edfe0fb1e50c06c2156815
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55159467"
---
# <a name="define-a-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování profilu technické OpenId Connect ve vlastních zásadách pro Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C poskytuje podporu pro [OpenId Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) protokol zprostředkovatele identity. OpenID Connect 1.0 definuje vrstvu identit přes OAuth 2.0 a představuje nejmodernější optimalizaci pspo v moderních ověřovacích protokolů.  Technický profil může provést federaci s OpenId Connect s OpenId Connect základě zprostředkovatele identity, jako je Azure AD umožňují uživatelům přihlásit se s jejich existujícími sociálních sítí nebo podnikové identity.

## <a name="protocol"></a>Protocol (Protokol)

**Název** atribut **protokol** elementu musí být nastavena na `OpenIdConnect`. Například protokol **MSA OIDC** technickém profilu je `OpenIdConnect`:

```XML
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
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

**OutputClaims** prvek obsahuje seznam deklarací identity vrácený zprostředkovatelem identity OpenId Connect. Budete muset namapovat název deklarace identity, definovaný ve svojí zásadě název definovaný ve zprostředkovateli identity. Můžete také zahrnout deklarace identity, které nejsou vrácené zprostředkovatelem identity tak dlouho, dokud je nastavit `DefaultValue` atribut.

**OutputClaimsTransformations** element může obsahovat kolekci **OutputClaimsTransformation** prvky, které se používají ke změně výstupní deklarace identit nebo generovat nové značky.

Následující příklad ukazuje deklarace vrácené poskytovatelem Account Microsoft identity:

- **Sub** deklarace identity, který je namapovaný na **socialIdpUserId** deklarací identity.
- **Název** deklarace identity, který je namapovaný na **displayName** deklarací identity.
- **E-mailu** bez mapování názvů.

Technický profil také vrátí hodnotu deklarace identity, které nejsou vrácené zprostředkovatelem identity:

- **IdentityProvider** deklarace identity, který obsahuje název zprostředkovatele identity.
- **AuthenticationSource** deklarace identity s výchozí hodnotou **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
  <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| client_id | Ano | Identifikátor aplikace zprostředkovatele identity. |
| IdTokenAudience | Ne | Cílová skupina požadavku id_token. Je-li zadána, Azure AD B2C ověří, zda token, který je v deklaraci identity vrácený zprostředkovatelem identity a rovná zadanému. |
| METADATA | Ano | Adresa URL odkazující na dokument JSON konfigurace ve formátu podle specifikace zjišťování OpenID Connect, která se také označuje jako koncový bod konfigurace dobře známé openid. |
| ProviderName | Ne | Název zprostředkovatele identity. |
| response_types | Ne | Typ odpovědi podle specifikace OpenID Connect Core 1.0. Možné hodnoty: `id_token`, `code`, nebo `token`. |
| response_mode | Ne | Metoda, která používá zprostředkovatel identity k odesílání výsledek zpět do Azure AD B2C. Možné hodnoty: `query`, `form_post` (výchozí), nebo `fragment`. |
| scope | Ne | Rozsah definovaný podle specifikace OpenID Connect 1.0 jádra na žádost o přístup. Například `openid`, `profile`, a `email`. |
| HttpBinding | Ne | Očekávané vazby HTTP na token a deklarací identity tokenu koncové body přístupu. Možné hodnoty: `GET` nebo `POST`.  |
| ValidTokenIssuerPrefixes | Ne | Klíč, který lze použít k přihlášení na jednotlivé klienty při použití zprostředkovatele identity s více tenanty jako je Azure Active Directory. |
| UsePolicyInRedirectUri | Ne | Určuje, jestli se má použít při vytváření identifikátor URI pro přesměrování zásady. Při konfiguraci vaší aplikace v poskytovateli identit, musíte zadat identifikátor URI pro přesměrování. Přesměrování identifikátor URI odkazuje na Azure AD B2C, `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` (login.microsoftonline.com může změnit pomocí vašeho tenanta name.b2clogin.com).  Pokud zadáte `false`, budete muset přidat identifikátor URI přesměrování pro jednotlivé zásady použití. Například: `https://login.microsoftonline.com/te/{tenant}/{policy}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Ne | Určuje, zda požadavek na externí služby musí být označené jako selhání, pokud kód stavu Http je v rozsahu 5xx. Výchozí formát je `false`. |
| DiscoverMetadataByTokenIssuer | Ne | Určuje, zda by měly být zjištěny OIDC metadat pomocí vystavitele tokenu JWT. |

## <a name="cryptographic-keys"></a>Kryptografické klíče

**CryptographicKeys** prvek obsahuje následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| client_secret | Ano | Tajný kód klienta aplikace zprostředkovatele identity. Šifrovací klíč je požadován, pouze v případě, **response_types** metadat je nastavena na `code`. V tomto případě Azure AD B2C umožňuje jiného volání k výměně autorizačního kódu pro přístupový token. Pokud metadata nastavená na `id_token` kryptografický klíč můžete vynechat.  |  

## <a name="redirect-uri"></a>Identifikátor Uri pro přesměrování
 
Při konfiguraci vašeho zprostředkovatele identity URI pro přesměrování, zadejte `https://login.microsoftonline.com/te/tenant/oauth2/authresp`. Nezapomeňte nahradit **tenanta** s názvem vašeho tenanta (například contosob2c.onmicrosoft.com) nebo ID tenanta. Identifikátor URI pro přesměrování musí být v všechna písmena malá.

Pokud používáte **b2clogin.com** domény namísto **login.microsoftonline.com** Ujistěte se, že použití b2clogin.com místo login.microsoftonline.com.

Příklady:

- [Přidat účet Microsoft (MSA) jako zprostředkovatele identity pomocí vlastních zásad](active-directory-b2c-custom-setup-msa-idp.md)
- [Přihlaste se pomocí účtů v Azure AD](active-directory-b2c-setup-aad-custom.md)
- [Povolit uživatelům umožní přihlásit k poskytovateli identity více tenantů Azure AD pomocí vlastních zásad](active-directory-b2c-setup-commonaad-custom.md)

 















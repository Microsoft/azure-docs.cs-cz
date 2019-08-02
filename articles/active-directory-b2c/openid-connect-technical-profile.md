---
title: Definování technického profilu OpenID Connect ve vlastních zásadách v Azure Active Directory B2C | Microsoft Docs
description: Definujte technický profil OpenID Connect ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1e8f03b17c5e8ea68affa9fe83875382fd5d8512
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716705"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu OpenID Connect v Azure Active Directory B2C vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C poskytuje podporu pro poskytovatele identity protokolu [OpenID Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) . OpenID Connect 1,0 definuje vrstvu identity nad OAuth 2,0 a představuje stav kresby v moderních ověřovacích protokolech. S technickým profilem OpenID Connect můžete federovat pomocí poskytovatele identity založeného na OpenID připojení, jako je například Azure AD. Federování se zprostředkovatelem identity umožňuje uživatelům přihlašovat se pomocí jejich stávajících sociálních nebo podnikových identit.

## <a name="protocol"></a>Protocol

Atribut **Name** elementu **Protocol** musí být nastaven na `OpenIdConnect`hodnotu. Například protokol pro technický profil **MSA-OIDC** je `OpenIdConnect`:

```XML
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
  ...
```

## <a name="input-claims"></a>Vstupní deklarace identity

Prvky **InputClaims** a **InputClaimsTransformations** nejsou požadovány. Je ale možné, že bude poskytovateli identity poslat další parametry. Následující příklad přidá parametr řetězce dotazu **domain_hint** s hodnotou `contoso.com` do autorizační žádosti.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Deklarace výstupů

Element **OutputClaims** obsahuje seznam deklarací vrácených zprostředkovatelem identity OpenID Connect. Možná budete muset namapovat název deklarace identity definované v zásadě na název definovaný v poskytovateli identity. Můžete také zahrnout deklarace identity, které nevrací poskytovatel identity, pokud nastavíte `DefaultValue` atribut.

Element **OutputClaimsTransformations** může obsahovat kolekci prvků **OutputClaimsTransformation** , které se používají k úpravě výstupních deklarací identity nebo k vygenerování nových.

Následující příklad ukazuje deklarace identity, které vrátil poskytovatel identity účtu Microsoft:

- **Podřízená** deklarace identity, která je namapovaná na deklaraci identity **issuerUserId**
- Deklarace **identity** , která je namapovaná na deklaraci **DisplayName** .
- **E-mail** bez mapování názvu

Technický profil také vrací deklarace identity, které nejsou vráceny zprostředkovatelem identity:

- Deklarace identity **identityProvider** , která obsahuje název poskytovatele identity.
- Deklarace identity **authenticationSource** s výchozí hodnotou **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| client_id | Ano | Identifikátor aplikace zprostředkovatele identity |
| IdTokenAudience | Ne | Cílová skupina pro id_token. Když se tato možnost zadá, Azure AD B2C ověří, jestli je token v deklaraci identity vrácené zprostředkovatelem identity a který se rovná zadanému typu. |
| METADATA | Ano | Adresa URL, která odkazuje na dokument konfigurace JSON formátovaný podle specifikace zjišťování OpenID Connect, která se také označuje jako známý koncový bod konfigurace OpenID. |
| ProviderName | Ne | Název zprostředkovatele identity |
| response_types | Ne | Typ odpovědi v souladu se specifikací OpenID Connect Core 1,0. Možné hodnoty: `id_token`, `code`, nebo `token`. |
| response_mode | Ne | Metoda, kterou zprostředkovatel identity používá k odeslání výsledku zpět do Azure AD B2C. Možné hodnoty: `query`, `form_post` (výchozí) nebo `fragment`. |
| scope | Ne | Rozsah požadavku, který je definován podle specifikace 1,0 OpenID Connect Core. `openid`Například, `profile`a. `email` |
| HttpBinding | Ne | Očekávaná vazba protokolu HTTP k přístupovému tokenu a koncovým bodům tokenu deklarací identity. Možné hodnoty: `GET` nebo `POST`.  |
| ValidTokenIssuerPrefixes | Ne | Klíč, který se dá použít k přihlášení ke každému klientovi při použití poskytovatele identity s více klienty, jako je například Azure Active Directory. |
| UsePolicyInRedirectUri | Ne | Určuje, jestli se při vytváření identifikátoru URI přesměrování má použít zásada. Při konfiguraci aplikace ve zprostředkovateli identity je nutné zadat identifikátor URI přesměrování. Identifikátor URI přesměrování odkazuje na Azure AD B2C, `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` (login.microsoftonline.com se může změnit pomocí Your-tenant-Name.b2clogin.com).  Pokud zadáte `false`, budete muset pro každou zásadu, kterou používáte, přidat identifikátor URI přesměrování. Například: `https://login.microsoftonline.com/te/{tenant}/{policy}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Ne | Určuje, zda má být požadavek na externí službu označený jako selhání, pokud je stavový kód HTTP v rozsahu 5xx. Výchozí hodnota je `false`. |
| DiscoverMetadataByTokenIssuer | Ne | Určuje, zda mají být metadata OIDC zjištěna pomocí vystavitele v tokenu JWT. |

## <a name="cryptographic-keys"></a>Kryptografické klíče

Element **CryptographicKeys** obsahuje následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| client_secret | Ano | Tajný kód klienta aplikace zprostředkovatele identity. Kryptografický klíč je vyžadován pouze v případě, že jsou metadata **response_types** nastavena `code`na hodnotu. V takovém případě Azure AD B2C provede další volání výměny autorizačního kódu pro přístupový token. Pokud jsou metadata nastavená na `id_token` , můžete kryptografický klíč vynechat.  |

## <a name="redirect-uri"></a>Identifikátor URI pro přesměrování

Když nakonfigurujete identifikátor URI pro přesměrování poskytovatele identity, zadejte `https://login.microsoftonline.com/te/tenant/oauth2/authresp`. Ujistěte se, že jste **klienta** nahradili názvem vašeho tenanta (například contosob2c.onmicrosoft.com) nebo ID tenanta. Identifikátor URI pro přesměrování musí být malými písmeny.

Pokud používáte doménu **b2clogin.com** místo **Login.microsoftonline.com** , nezapomeňte místo Login.microsoftonline.com použít b2clogin.com.

Příklady:

- [Přidání účtu Microsoft (MSA) jako zprostředkovatele identity pomocí vlastních zásad](active-directory-b2c-custom-setup-msa-idp.md)
- [Přihlášení pomocí účtů Azure AD](active-directory-b2c-setup-aad-custom.md)
- [Umožňuje uživatelům přihlásit se k poskytovateli identit Azure AD s více klienty pomocí vlastních zásad.](active-directory-b2c-setup-commonaad-custom.md)

 















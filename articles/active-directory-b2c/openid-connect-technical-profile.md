---
title: Definování technického profilu OpenID Connect ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Definujte technický profil OpenID Connect ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8e8a56fdfd57b44677cf5459eb1a4e6e46e6bdae
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399062"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu OpenID Connect v Azure Active Directory B2C vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) poskytuje podporu pro poskytovatele identity protokolu [OpenID Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) . OpenID Connect 1,0 definuje vrstvu identity nad OAuth 2,0 a představuje stav kresby v moderních ověřovacích protokolech. S technickým profilem OpenID Connect můžete federovat pomocí poskytovatele identity založeného na OpenID připojení, jako je například Azure AD. Federování se zprostředkovatelem identity umožňuje uživatelům přihlašovat se pomocí jejich stávajících sociálních nebo podnikových identit.

## <a name="protocol"></a>Protocol (Protokol)

Atribut **Name** elementu **Protocol** musí být nastaven na `OpenIdConnect`. Například protokol pro technický profil **MSA-OIDC** je `OpenIdConnect`:

```XML
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
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

Element **OutputClaims** obsahuje seznam deklarací vrácených zprostředkovatelem identity OpenID Connect. Možná budete muset namapovat název deklarace identity definované v zásadě na název definovaný v poskytovateli identity. Můžete také zahrnout deklarace identity, které nevrací poskytovatel identity, pokud nastavíte atribut `DefaultValue`.

Element **OutputClaimsTransformations** může obsahovat kolekci prvků **OutputClaimsTransformation** , které se používají k úpravě výstupních deklarací identity nebo k vygenerování nových.

Následující příklad ukazuje deklarace identity, které vrátil poskytovatel identity účtu Microsoft:

- **Podřízená** deklarace identity, která je namapovaná na deklaraci identity **issuerUserId**
- Deklarace identity, která je namapovaná **na deklaraci** **DisplayName** .
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

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| client_id | Ano | Identifikátor aplikace zprostředkovatele identity |
| IdTokenAudience | Ne | Cílová skupina id_token. Když se tato možnost zadá, Azure AD B2C ověří, jestli je deklarace identity `aud` v tokenu, kterou vrátil zprostředkovatel identity, rovna hodnotě určené v metadatech IdTokenAudience.  |
| METADATA | Ano | Adresa URL, která odkazuje na dokument konfigurace zprostředkovatele identity OpenID Connect, který se taky označuje jako OpenID dobře známý koncový bod konfigurace. Adresa URL může obsahovat výraz `{tenant}`, který je nahrazen názvem tenanta.  |
| authorization_endpoint | Ne | Adresa URL, která odkazuje na koncový bod autorizace konfigurace zprostředkovatele identity OpenID Connect. Hodnota metadat authorization_endpoint má přednost před `authorization_endpoint` zadanou v OpenID dobře známém koncovém bodu konfigurace. Adresa URL může obsahovat výraz `{tenant}`, který je nahrazen názvem tenanta. |
| stavil | Ne | Jedinečný identifikátor poskytovatele identity OpenID Connect. Hodnota metadat vystavitele má přednost před `issuer` zadanou v OpenID dobře známém koncovém bodu konfigurace.  Když se tato možnost zadá, Azure AD B2C zkontroluje, jestli je deklarace identity `iss` v tokenu, kterou vrátil zprostředkovatel identity, rovna hodnotě určené v metadatech vystavitele. |
| ProviderName | Ne | Název zprostředkovatele identity  |
| response_types | Ne | Typ odpovědi v souladu se specifikací OpenID Connect Core 1,0. Možné hodnoty: `id_token`, `code`nebo `token`. |
| response_mode | Ne | Metoda, kterou zprostředkovatel identity používá k odeslání výsledku zpět do Azure AD B2C. Možné hodnoty: `query`, `form_post` (výchozí) nebo `fragment`. |
| scope | Ne | Rozsah požadavku, který je definován podle specifikace 1,0 OpenID Connect Core. Například `openid`, `profile`a `email`. |
| HttpBinding | Ne | Očekávaná vazba protokolu HTTP k přístupovému tokenu a koncovým bodům tokenu deklarací identity. Možné hodnoty: `GET` nebo `POST`.  |
| ValidTokenIssuerPrefixes | Ne | Klíč, který se dá použít k přihlášení ke každému klientovi při použití poskytovatele identity s více klienty, jako je například Azure Active Directory. |
| UsePolicyInRedirectUri | Ne | Určuje, jestli se při vytváření identifikátoru URI přesměrování má použít zásada. Při konfiguraci aplikace ve zprostředkovateli identity je nutné zadat identifikátor URI přesměrování. Identifikátor URI přesměrování odkazuje na Azure AD B2C, `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`.  Pokud zadáte `false`, budete muset pro každou zásadu, kterou používáte, přidat identifikátor URI přesměrování. Například: `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Ne | Určuje, zda má být požadavek na externí službu označený jako selhání, pokud je stavový kód HTTP v rozsahu 5xx. Výchozí formát je `false`. |
| DiscoverMetadataByTokenIssuer | Ne | Určuje, zda mají být metadata OIDC zjištěna pomocí vystavitele v tokenu JWT. |
| IncludeClaimResolvingInClaimsHandling  | Ne | Pro vstupní a výstupní deklarace identity určuje, jestli je [řešení deklarací identity](claim-resolver-overview.md) zahrnuté v technickém profilu. Možné hodnoty: `true`nebo `false` (výchozí). Pokud chcete použít překladač deklarací identity v technickém profilu, nastavte tuto hodnotu na `true`. |

## <a name="cryptographic-keys"></a>Kryptografické klíče

Element **CryptographicKeys** obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| client_secret | Ano | Tajný kód klienta aplikace zprostředkovatele identity. Kryptografický klíč je vyžadován pouze v případě, že jsou metadata **response_types** nastavena na hodnotu `code`. V takovém případě Azure AD B2C provede další volání výměny autorizačního kódu pro přístupový token. Pokud jsou metadata nastavená na `id_token`, můžete kryptografický klíč vynechat.  |

## <a name="redirect-uri"></a>Identifikátor URI pro přesměrování

Když nakonfigurujete identifikátor URI pro přesměrování poskytovatele identity, zadejte `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`. Nezapomeňte nahradit `{your-tenant-name}` názvem vašeho tenanta. Identifikátor URI pro přesměrování musí být malými písmeny.

Příklady:

- [Přidání účtu Microsoft (MSA) jako zprostředkovatele identity pomocí vlastních zásad](identity-provider-microsoft-account-custom.md)
- [Přihlášení pomocí účtů Azure AD](identity-provider-azure-ad-single-tenant-custom.md)
- [Umožňuje uživatelům přihlásit se k poskytovateli identit Azure AD s více klienty pomocí vlastních zásad.](identity-provider-azure-ad-multi-tenant-custom.md)

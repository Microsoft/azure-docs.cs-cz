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
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: fea42cb89dce717431c188deeb2ce83f9413f560
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107283877"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu OpenID Connect v Azure Active Directory B2C vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) poskytuje podporu pro poskytovatele identity protokolu [OpenID Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) . OpenID Connect 1,0 definuje vrstvu identity nad OAuth 2,0 a představuje stav kresby v moderních ověřovacích protokolech. S technickým profilem OpenID Connect můžete federovat pomocí poskytovatele identity založeného na OpenID připojení, jako je například Azure AD. Federování se zprostředkovatelem identity umožňuje uživatelům přihlašovat se pomocí jejich stávajících sociálních nebo podnikových identit.

## <a name="protocol"></a>Protokol

Atribut **Name** elementu **Protocol** musí být nastaven na hodnotu `OpenIdConnect` . Například protokol pro technický profil **MSA-OIDC** je `OpenIdConnect` :

```xml
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
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

Element **OutputClaims** obsahuje seznam deklarací vrácených zprostředkovatelem identity OpenID Connect. Možná budete muset namapovat název deklarace identity definované v zásadě na název definovaný v poskytovateli identity. Můžete také zahrnout deklarace identity, které nevrací poskytovatel identity, pokud nastavíte `DefaultValue` atribut.

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

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| client_id | Yes | Identifikátor aplikace zprostředkovatele identity |
| IdTokenAudience | No | Cílová skupina id_token. Když se tato možnost zadá, Azure AD B2C ověří, jestli je `aud` deklarace identity v tokenu, kterou vrátil poskytovatel identity, rovna hodnotě uvedené v metadatech IdTokenAudience.  |
| MEZIPAMĚŤ | Yes | Adresa URL, která odkazuje na dokument konfigurace zprostředkovatele identity OpenID Connect, který se taky označuje jako OpenID dobře známý koncový bod konfigurace. Adresa URL může obsahovat `{tenant}` výraz, který je nahrazen názvem tenanta.  |
| authorization_endpoint | No | Adresa URL, která odkazuje na koncový bod autorizace konfigurace zprostředkovatele identity OpenID Connect. Hodnota metadat authorization_endpoint má přednost před `authorization_endpoint` zadanou hodnotou v koncovém bodu známé konfigurace OpenID. Adresa URL může obsahovat `{tenant}` výraz, který je nahrazen názvem tenanta. |
| end_session_endpoint | No | Adresa URL koncového bodu koncové relace. Hodnota metadat authorization_endpoint má přednost před `end_session_endpoint` zadanou hodnotou v koncovém bodu známé konfigurace OpenID. |
| issuer | No | Jedinečný identifikátor poskytovatele identity OpenID Connect. Hodnota metadat vystavitele má přednost před `issuer` zadanou hodnotou v OpenIDm známém koncovém bodu konfigurace.  Když se tato možnost zadá, Azure AD B2C ověří, jestli je `iss` deklarace identity v tokenu, kterou vrátil poskytovatel identity, rovna hodnotě určené v metadatech vystavitele. |
| ProviderName | No | Název zprostředkovatele identity  |
| response_types | No | Typ odpovědi v souladu se specifikací OpenID Connect Core 1,0. Možné hodnoty: `id_token` , `code` , nebo `token` . |
| response_mode | No | Metoda, kterou zprostředkovatel identity používá k odeslání výsledku zpět do Azure AD B2C. Možné hodnoty: `query` , `form_post` (výchozí) nebo `fragment` . |
| scope | No | Rozsah požadavku, který je definován podle specifikace 1,0 OpenID Connect Core. Například `openid` , `profile` a `email` . |
| HttpBinding | No | Očekávaná vazba protokolu HTTP k přístupovému tokenu a koncovým bodům tokenu deklarací identity. Možné hodnoty: `GET` nebo `POST` .  |
| ValidTokenIssuerPrefixes | No | Klíč, který se dá použít k přihlášení ke každému klientovi při použití poskytovatele identity s více klienty, jako je například Azure Active Directory. |
| UsePolicyInRedirectUri | No | Určuje, jestli se při vytváření identifikátoru URI přesměrování má použít zásada. Při konfiguraci aplikace ve zprostředkovateli identity je nutné zadat identifikátor URI přesměrování. Identifikátor URI přesměrování odkazuje na Azure AD B2C, `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp` .  Pokud zadáte `true` , budete muset pro každou zásadu, kterou používáte, přidat identifikátor URI přesměrování. Příklad: `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | No | Určuje, zda má být požadavek na externí službu označený jako selhání, pokud je stavový kód HTTP v rozsahu 5xx. Výchozí formát je `false`. |
| DiscoverMetadataByTokenIssuer | No | Určuje, zda mají být metadata OIDC zjištěna pomocí vystavitele v tokenu JWT. |
| IncludeClaimResolvingInClaimsHandling  | No | Pro vstupní a výstupní deklarace identity určuje, jestli je [řešení deklarací identity](claim-resolver-overview.md) zahrnuté v technickém profilu. Možné hodnoty: `true` , nebo `false` (výchozí). Pokud chcete použít překladač deklarací identity v technickém profilu, nastavte tuto hodnotu na `true` . |
|token_endpoint_auth_method| No | Určuje způsob, jakým Azure AD B2C odesílá hlavičku ověřování na koncový bod tokenu. Možné hodnoty: `client_secret_post` (výchozí) a `client_secret_basic` (Public Preview), `private_key_jwt` (Public Preview). Další informace najdete v [části ověřování klientů OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication). |
|token_signing_algorithm| No | Určuje podpisový algoritmus, který má být použit, pokud `token_endpoint_auth_method` je nastaven na hodnotu `private_key_jwt` . Možné hodnoty: `RS256` (výchozí) nebo `RS512` .|
| SingleLogoutEnabled | No | Určuje, jestli se během přihlašování technického profilu pokusí odhlásit od zprostředkovatelů federovaných identit. Další informace najdete v tématu věnovaném [odhlášení Azure AD B2C relace](./session-behavior.md#sign-out).  Možné hodnoty: `true` (výchozí), nebo `false` . |
|ReadBodyClaimsOnIdpRedirect| No| Nastavte na hodnotu `true` číst deklarace identity z těla odpovědi na přesměrování zprostředkovatele identity. Tato metadata se používají s [Apple ID](identity-provider-apple-id.md), kde deklarace identity vrací v datové části odpovědi.|

```xml
<Metadata>
  <Item Key="ProviderName">https://login.live.com</Item>
  <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
  <Item Key="response_types">code</Item>
  <Item Key="response_mode">form_post</Item>
  <Item Key="scope">openid profile email</Item>
  <Item Key="HttpBinding">POST</Item>
  <Item Key="UsePolicyInRedirectUri">false</Item>
  <Item Key="client_id">Your Microsoft application client ID</Item>
</Metadata>
```

### <a name="ui-elements"></a>Prvky uživatelského rozhraní
 
Následující nastavení lze použít ke konfiguraci chybové zprávy, která se zobrazí po selhání. Metadata by měla být nakonfigurovaná ve technickém profilu OpenID Connect. Chybové zprávy lze [lokalizovat](localization-string-ids.md#sign-up-or-sign-in-error-messages).

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalDoesNotExist | No | Zpráva, která se zobrazí uživateli, pokud se v adresáři nenajde účet se zadaným uživatelským jménem |
| UserMessageIfInvalidPassword | No | Zpráva, která se zobrazí uživateli, pokud je chybné heslo |
| UserMessageIfOldPasswordUsed| No |  Zpráva, která se zobrazí uživateli, pokud se použilo původní heslo|

## <a name="cryptographic-keys"></a>Kryptografické klíče

Element **CryptographicKeys** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| client_secret | Yes | Tajný kód klienta aplikace zprostředkovatele identity. Tento kryptografický klíč je vyžadován pouze v případě, že je **response_types** metadat nastavena na hodnotu `code` a **token_endpoint_auth_method** je nastavena na hodnotu `client_secret_post` nebo `client_secret_basic` . V takovém případě Azure AD B2C provede další volání výměny autorizačního kódu pro přístupový token. Pokud jsou metadata nastavená na `id_token` , můžete kryptografický klíč vynechat.  |
| assertion_signing_key | Yes | Privátní klíč RSA, který bude použit k podepsání kontrolního výrazu klienta. Tento kryptografický klíč je vyžadován pouze v případě, že jsou metadata **token_endpoint_auth_method** nastavena na hodnotu `private_key_jwt` . |

## <a name="redirect-uri"></a>Identifikátor URI pro přesměrování

Když nakonfigurujete identifikátor URI pro přesměrování poskytovatele identity, zadejte `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp` . Ujistěte se, že `{your-tenant-name}` jste se svým jménem svého tenanta nahradili. Identifikátor URI pro přesměrování musí být malými písmeny.

Příklady:

- [Přidání účtu Microsoft (MSA) jako zprostředkovatele identity pomocí vlastních zásad](identity-provider-microsoft-account.md)
- [Přihlášení pomocí účtů Azure AD](identity-provider-azure-ad-single-tenant.md)
- [Umožňuje uživatelům přihlásit se k poskytovateli identit Azure AD s více klienty pomocí vlastních zásad.](identity-provider-azure-ad-multi-tenant.md)

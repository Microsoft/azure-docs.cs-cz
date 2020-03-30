---
title: Definování technického profilu OpenID Connect ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Ve vlastní chodníčkové službě Azure Active Directory B2C definujte technický profil OpenID Connect ve vlastních zásadách.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e259a57a9cd6b24362862ffd6cb738157ca912d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332767"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu OpenID Connect ve vlastních zásadách služby Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) poskytuje podporu pro poskytovatele identity protokolu [OpenID Connect.](https://openid.net/2015/04/17/openid-connect-certification-program/) OpenID Connect 1.0 definuje vrstvu identity nad OAuth 2.0 a představuje stav techniky v moderních autentizačních protokolech. S technickým profilem OpenID Connect můžete federate s poskytovatelem identit y založeným na OpenID Connect, jako je Azure AD. Federace s poskytovatelem identity umožňuje uživatelům přihlásit se pomocí svých stávajících sociálních nebo podnikových identit.

## <a name="protocol"></a>Protocol (Protokol)

**Atribut Name** elementu **Protocol** musí být `OpenIdConnect`nastaven na . Například protokol pro technický profil **MSA-OIDC** je: `OpenIdConnect`

```XML
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
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

Element **OutputClaims** obsahuje seznam deklarací vrácených poskytovatelem identity OpenID Connect. Možná budete muset namapovat název deklarace identity definované v zásadách na název definovaný v poskytovateli identity. Můžete také zahrnout deklarace identity, které nejsou vráceny zprostředkovatelem `DefaultValue` identity, pokud nastavíte atribut.

**OutputClaimsTransformations** Element může obsahovat kolekci **OutputClaimsTransformation** prvky, které se používají k úpravě výstupní deklarace nebo generovat nové.

Následující příklad ukazuje deklarace identity vrácené poskytovatelem identity účtu Microsoft:

- **Dílčí** deklarace, která je mapována na deklaraci **issuerUserId.**
- Deklarace **názvu,** která je mapována na deklaraci **displayName.**
- **E-mail** bez mapování názvu.

Technický profil také vrátí deklarace identity, které nejsou vráceny zprostředkovatelem identity:

- **IdentityProvider** deklarace, která obsahuje název zprostředkovatele identity.
- Deklarace **authenticationSource** s výchozí hodnotou **socialIdpAuthentication**.

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
| client_id | Ano | Identifikátor aplikace zprostředkovatele identity. |
| IdTokenAudience | Ne | Publikum id_token. Pokud je zadán, Azure AD `aud` B2C zkontroluje, zda deklarace identity vrácené zprostředkovatelem identity se rovná jedné zadané v metadatech IdTokenAudience.  |
| Metadata | Ano | Adresa URL, která odkazuje na konfigurační dokument poskytovatele identity OpenID Connect, který je také známý jako OpenID známý koncový bod konfigurace. Adresa URL může `{tenant}` obsahovat výraz, který je nahrazen názvem klienta.  |
| authorization_endpoint | Ne | Adresa URL, která odkazuje na koncový bod autorizace konfigurace poskytovatele identity OpenID Connect. Hodnota metadat authorization_endpoint má přednost před `authorization_endpoint` zadaným v koncovém bodu openid dobře známé konfigurace. Adresa URL může `{tenant}` obsahovat výraz, který je nahrazen názvem klienta. |
| issuer | Ne | Jedinečný identifikátor poskytovatele identity OpenID Connect. Hodnota metadat vystavitho nuitáře `issuer` má přednost před zadaným v koncovém bodu openid dobře známé konfigurace.  Pokud je zadán, Azure AD `iss` B2C zkontroluje, zda deklarace identity vrácené zprostředkovatelem identity se rovná jedné zadané v metadatech vystavitele. |
| Providername | Ne | Název zprostředkovatele identity.  |
| response_types | Ne | Typ odezvy podle specifikace OpenID Connect Core 1.0. Možné `id_token`hodnoty: `code`, `token`, nebo . |
| response_mode | Ne | Metoda, která zprostředkovatel identity používá k odeslání výsledku zpět do Azure AD B2C. Možné `query`hodnoty: `form_post` , (výchozí) nebo `fragment`. |
| scope | Ne | Rozsah požadavku, který je definován podle specifikace OpenID Connect Core 1.0. Například `openid` `profile`, `email`a . |
| HttpBinding | Ne | Očekávaná vazba HTTP pro koncový bod přístupového tokenu a tokenu deklarací identity. Možné `GET` hodnoty: `POST`nebo .  |
| Oprava validatokenu | Ne | Klíč, který lze použít k přihlášení ke každému z klientů při použití víceklientského zprostředkovatele identity, jako je například Azure Active Directory. |
| UsePolicyInRedirectUri | Ne | Označuje, zda se má použít zásada při vytváření identifikátoru URI přesměrování. Při konfiguraci aplikace v zprostředkovateli identity je třeba zadat identifikátor URI přesměrování. Identifikátor URI přesměrování odkazuje na Azure AD B2C, `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`.  Pokud zadáte `false`, je třeba přidat identifikátor URI přesměrování pro každou zásadu, kterou používáte. Například: `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Ne | Označuje, zda má být požadavek na externí službu označen jako neúspěšný, pokud je stavový kód http v rozsahu 5xx. Výchozí formát je `false`. |
| DiscoverMetadataByTokenIssuer | Ne | Označuje, zda by měla být metadata OIDC zjištěna pomocí vystavittele v tokenu JWT. |
| IncludeClaimResolvingInClaimsHandling  | Ne | Pro vstupní a výstupní deklarace určuje, zda je [řešení deklarací](claim-resolver-overview.md) zahrnuto do technického profilu. Možné hodnoty: `true` `false`  , nebo (výchozí). Pokud chcete použít překladač deklarací identity v `true`technickém profilu, nastavte toto na . |

### <a name="ui-elements"></a>Prvky uživatelského rozhraní
 
Následující nastavení lze použít ke konfiguraci chybové zprávy zobrazené při selhání. Metadata by měla být nakonfigurována v technickém profilu OpenID Connect. Chybové zprávy lze [lokalizovat](localization-string-ids.md#sign-up-or-sign-in-error-messages).

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalDoesNotExist | Ne | Zpráva, která se zobrazí uživateli, pokud účet s poskytnutým uživatelským jménem nebyl v adresáři nalezen. |
| Uživatelské zprávyIfInvalidPassword | Ne | Zpráva, která se má uživateli zobrazit, pokud je heslo nesprávné. |
| UserMessageIfOldPasswordUsed| Ne |  Zpráva, která se má uživateli zobrazit, pokud bylo použito staré heslo.|

## <a name="cryptographic-keys"></a>Kryptografické klíče

Prvek **CryptographicKeys** obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| client_secret | Ano | Tajný klíč klienta aplikace zprostředkovatele identity. Kryptografický klíč je vyžadován pouze v `code`případě, že **je** response_types metadata nastavena na . V takovém případě Azure AD B2C provede další volání k výměně autorizační kód pro přístupový token. Pokud je metadata `id_token` nastavena na můžete vynechat kryptografický klíč.  |

## <a name="redirect-uri"></a>Přesměrovat Uri

Při konfiguraci identifikátoru URI přesměrování poskytovatele `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`identity zadejte . Nezapomeňte nahradit `{your-tenant-name}` jméno vašeho tenanta. Identifikátor URI přesměrování musí být ve všech složených písmenech.

Příklady:

- [Přidání účtu Microsoft (MSA) jako poskytovatele identity pomocí vlastních zásad](identity-provider-microsoft-account-custom.md)
- [Přihlášení pomocí účtů Azure AD](identity-provider-azure-ad-single-tenant-custom.md)
- [Povolit uživatelům přihlásit se k víceklientské zprostředkovateli identity Azure AD pomocí vlastních zásad](identity-provider-azure-ad-multi-tenant-custom.md)

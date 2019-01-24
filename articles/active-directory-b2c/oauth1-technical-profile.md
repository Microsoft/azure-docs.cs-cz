---
title: Definování profilu technické OAuth1 vlastních zásad v Azure Active Directory B2C | Dokumentace Microsoftu
description: Technický profil OAuth1 definování vlastních zásad v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6e93f42540492d138afef3a1d4b826165201b04c
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54851347"
---
# <a name="define-a-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Technický profil OAuth1 definování ve vlastních zásadách pro Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C poskytuje podporu pro [protokol OAuth 1.0](https://tools.ietf.org/html/rfc5849) zprostředkovatele identity. Tento článek popisuje, jaké jsou specifikace technický profil pro interakci s zprostředkovatele deklarací identity, která podporuje tento protokol standardizované. Technický profil může provést federaci s OAuth1 s OAuth1 podle zprostředkovatele identity, jako je Twitter, umožňuje uživatelům přihlásit se s jejich existujícími sociálních sítí nebo podnikové identity.

## <a name="protocol"></a>Protocol (Protokol)

**Název** atribut **protokol** elementu musí být nastavena na `OAuth1`. Například protokol **Twitter OAUTH1** technickém profilu je `OAuth1`.

```XML
<TechnicalProfile Id="Twitter-OAUTH1">
  <DisplayName>Twitter</DisplayName>
  <Protocol Name="OAuth1" />
  ...    
```

## <a name="input-claims"></a>Vstupní deklarace identity

**InputClaims** a **InputClaimsTransformations** prvky jsou prázdné nebo zcela chybět.

## <a name="output-claims"></a>Výstupní deklarace identit.

**OutputClaims** prvek obsahuje seznam vrácený zprostředkovatelem OAuth1 identit deklarací identity. Budete muset namapovat název deklarace identity, definovaný ve svojí zásadě název definovaný ve zprostředkovateli identity. Může také obsahovat deklarace identity, které nejsou vrácené zprostředkovatelem identity tak dlouho, dokud jste nastavili **DefaultValue** atribut.

**OutputClaimsTransformations** element může obsahovat kolekci **OutputClaimsTransformation** prvky, které se používají ke změně výstupní deklarace identit nebo generovat nové značky.

Následující příklad ukazuje deklarace vrácené poskytovatelem identity Twitteru:

- **User_id** deklarace identity, který je namapovaný na **socialIdpUserId** deklarací identity.
- **Screen_name** deklarace identity, který je namapovaný na **displayName** deklarací identity.
- **E-mailu** deklarace identity bez mapování názvů.

Technický profil také vrátí hodnotu deklarace identity, které nejsou vrácené zprostředkovatelem identity: 

- **IdentityProvider** deklarace identity, který obsahuje název zprostředkovatele identity.
- **AuthenticationSource** deklarace identity s výchozí hodnotou `socialIdpAuthentication`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="user_id" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| client_id | Ano | Identifikátor aplikace zprostředkovatele identity. |
| ProviderName | Ne | Název zprostředkovatele identity. |
| request_token_endpoint | Ano | Adresa URL požadavku koncového bodu tokenu podle RFC 5849. |
| authorization_endpoint | Ano | Adresa URL koncového bodu autorizace podle RFC 5849. |
| access_token_endpoint | Ano | Adresa URL koncového bodu tokenu podle RFC 5849. |
| ClaimsEndpoint | Ne | Adresa URL koncového bodu uživatelské informace. | 
| ClaimsResponseFormat | Ne | Formát odpovědi deklarací identity.|

## <a name="cryptographic-keys"></a>Kryptografické klíče

**CryptographicKeys** prvek obsahuje následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| client_secret | Ano | Tajný kód klienta aplikace zprostředkovatele identity.   | 

## <a name="redirect-uri"></a>Přesměrovat identifikátor URI

Při konfiguraci adresy URL pro přesměrování vašeho zprostředkovatele identity, zadejte `https://login.microsoftonline.com/te/tenant/policyId/oauth1/authresp`. Nezapomeňte nahradit **tenanta** názvem vašeho tenanta (například contosob2c.onmicrosoft.com) a **policyId** s identifikátorem zásady (například b2c_1a_policy). Identifikátor URI pro přesměrování musí být v všechna písmena malá. Měli byste přidat UR přesměrování pro všechny zásady, které používají přihlášení zprostředkovatele identity. 

Pokud používáte **b2clogin.com** domény namísto **login.microsoftonline.com** Ujistěte se, že použití b2clogin.com místo login.microsoftonline.com.

Příklady:

- [Přidat Twitteru jako zprostředkovatele identity OAuth1 pomocí vlastních zásad](active-directory-b2c-custom-setup-twitter-idp.md)














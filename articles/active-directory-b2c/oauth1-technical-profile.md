---
title: Technický profil OAuth1 definování ve vlastních zásadách v Azure Active Directory B2C | Dokumentace Microsoftu
description: Technický profil OAuth1 definování ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 993fc8b2e318b59775f61de391ac75fa765485f0
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66513119"
---
# <a name="define-an-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Technický profil OAuth1 definování ve vlastních zásadách pro Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C poskytuje podporu pro [protokol OAuth 1.0](https://tools.ietf.org/html/rfc5849) zprostředkovatele identity. Tento článek popisuje, jaké jsou specifikace technický profil pro interakci s zprostředkovatele deklarací identity, která podporuje tento protokol standardizované. S OAuth1 technický profil může provést federaci pomocí zprostředkovatele identity na základě OAuth1, jako je Twitter. Federování s využitím zprostředkovatele identity umožňuje uživatelům Přihlaste se pomocí své stávající sociálních sítí nebo podnikové identity.

## <a name="protocol"></a>Protocol

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

- **User_id** deklarace identity, který je namapovaný na **issuerUserId** deklarací identity.
- **Screen_name** deklarace identity, který je namapovaný na **displayName** deklarací identity.
- **E-mailu** deklarace identity bez mapování názvů.

Technický profil také vrátí hodnotu deklarace identity, které nejsou vrácené zprostředkovatelem identity: 

- **IdentityProvider** deklarace identity, který obsahuje název zprostředkovatele identity.
- **AuthenticationSource** deklarace identity s výchozí hodnotou `socialIdpAuthentication`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
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

## <a name="redirect-uri"></a>Identifikátor URI pro přesměrování

Při konfiguraci adresy URL pro přesměrování vašeho zprostředkovatele identity, zadejte `https://login.microsoftonline.com/te/tenant/policyId/oauth1/authresp`. Nezapomeňte nahradit **tenanta** názvem vašeho tenanta (například contosob2c.onmicrosoft.com) a **policyId** s identifikátorem zásady (například b2c_1a_policy). Identifikátor URI pro přesměrování musí být v všechna písmena malá. Přidáte adresu URL pro přesměrování pro všechny zásady, které používají přihlášení zprostředkovatele identity. 

Pokud používáte **b2clogin.com** domény namísto **login.microsoftonline.com** Ujistěte se, že použití b2clogin.com místo login.microsoftonline.com.

Příklady:

- [Přidat Twitteru jako zprostředkovatele identity OAuth1 pomocí vlastních zásad](active-directory-b2c-custom-setup-twitter-idp.md)














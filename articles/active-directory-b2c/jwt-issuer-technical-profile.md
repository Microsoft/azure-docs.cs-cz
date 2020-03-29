---
title: Definování technického profilu pro vystavittele JWT ve vlastní chod
titleSuffix: Azure AD B2C
description: Definujte technický profil pro vystavitela webového tokenu JSON (JWT) ve vlastní chodnících ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/06/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c23648d70192607b2a5b977dcdd445931e995154
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671790"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu pro vystavitela tokenu JWT ve vlastních zásadách služby Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) vydává několik typů tokenů zabezpečení při procesu každého toku ověřování. Technický profil pro vystavitele tokenu JWT vydává token JWT, který je vrácen zpět do aplikace předávající strany. Obvykle tento technický profil je poslední krok orchestrace v cestě uživatele.

## <a name="protocol"></a>Protocol (Protokol)

**Atribut Name** elementu **Protocol** musí být `None`nastaven na . Nastavte element **OutputTokenFormat** na `JWT`.

Následující příklad ukazuje technický `JwtIssuer`profil pro :

```XML
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="None" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  ...
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Vstupní, výstupní a trvalé deklarace identity

**InputClaims**, **OutputClaims**a **PersistClaims** prvky jsou prázdné nebo chybí. **InutputClaimsTransformations** a **OutputClaimsTransformations prvky** jsou také chybí.

## <a name="metadata"></a>Metadata

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | Ano | Deklarace, která by měla být použita jako deklarace identity uživatele v rámci autorizačních kódů OAuth2 a tokenů aktualizace. Ve výchozím nastavení byste `objectId`ji měli nastavit na , pokud nezadáte jiný typ deklarace subjectnaminginfo. |
| SendTokenResponseBodyWithJsonNumbers | Ne | Vždy nastavena na `true`. Pro starší formát, kde jsou číselné hodnoty uvedeny jako `false`řetězce namísto čísel JSON, nastavte na . Tento atribut je potřeba pro klienty, kteří přijali závislost na dřívější implementaci, která vrátila takové vlastnosti jako řetězce. |
| token_lifetime_secs | Ne | Životnost přístupových tokenů. Životnost tokenu nosiče OAuth 2.0, který slouží k získání přístupu k chráněnému prostředku. Výchozí hodnota je 3 600 sekund (1 hodina). Minimální (včetně) je 300 sekund (5 minut). Maximální (včetně) je 86 400 sekund (24 hodin). |
| id_token_lifetime_secs | Ne | Životnost tokenu ID. Výchozí hodnota je 3 600 sekund (1 hodina). Minimální (včetně) je 300 sekund (5 minut). Maximální (včetně) je 86 400 sekund (24 hodin). |
| refresh_token_lifetime_secs | Ne | Životnost imitace tokenu aktualizace. Maximální doba, před kterou lze aktualizovat token získat nový přístupový token, pokud vaše aplikace byla udělena offline_access oboru. Výchozí hodnota je 120 9600 sekund (14 dní). Minimální (včetně) je 86 400 sekund (24 hodin). Maximální (včetně) je 7 776 000 sekund (90 dní). |
| rolling_refresh_token_lifetime_secs | Ne | Obnovovat token posuvné okno životnost. Po uplynutí této doby je uživatel nucen k opětovnému ověření, bez ohledu na dobu platnosti posledního obnovovacího tokenu získaného aplikací. Pokud nechcete vynutit životnost posuvných oken, nastavte `true`hodnotu allow_infinite_rolling_refresh_token na . Výchozí hodnota je 7 776 000 sekund (90 dní). Minimální (včetně) je 86 400 sekund (24 hodin). Maximální (včetně) je 31 536 000 sekund (365 dní). |
| allow_infinite_rolling_refresh_token | Ne | Pokud je `true`nastavena na , životnost posuvného okna tokenu aktualizace nikdy nevyprší. |
| Vzor vystavování pohledávky | Ne | Řídí deklaraci vydavatele (iss). Jedna z hodnot:<ul><li>AuthorityAndTenantGuid - Deklarace iss zahrnuje název `login.microsoftonline` `tenant-name.b2clogin.com`vaší domény, například\/nebo , a identifikátor klienta https: /login.microsoftonline.com/00000000-0000-0000-0000-000000000000/v2.0/</li><li>AuthorityWithTfp - Deklarace iss zahrnuje název `login.microsoftonline` `tenant-name.b2clogin.com`domény, například nebo , identifikátor klienta a název zásad předávající strany. https:\//login.microsoftonline.com/tfp/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-sign-in/v2.0/</li></ul> Výchozí hodnota: AuthorityAndTenantGuid |
| OvěřeníContextReferenceClaimPattern | Ne | Řídí `acr` hodnotu deklarace.<ul><li>Žádné – Azure AD B2C nevydává deklaraci acr</li><li>PolicyId - `acr` deklarace obsahuje název zásady</li></ul>Možnosti pro nastavení této hodnoty jsou TFP (zásady architektury důvěryhodnosti) a ACR (odkaz na kontext ověřování). Doporučuje se nastavit tuto hodnotu na TFP, `<Item>` nastavit `Key="AuthenticationContextReferenceClaimPattern"` hodnotu, ujistěte `None`se, že s existuje a hodnota je . V zásadách předávající `<OutputClaims>` strany přidejte položku, přidejte tento prvek `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />`. Také se ujistěte, že vaše zásady obsahují typ deklarace.`<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` |
|RefreshTokenUserJourneyId| Ne | Identifikátor cesty uživatele, která by měla být provedena během aktualizace `/token` požadavku POST [přístupového tokenu](authorization-code-flow.md#4-refresh-the-token) do koncového bodu. |

## <a name="cryptographic-keys"></a>Kryptografické klíče

Prvek CryptographicKeys obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| issuer_secret | Ano | Certifikát X509 (sada klíčů RSA), který se má použít k podepsání tokenu JWT. Toto `B2C_1A_TokenSigningKeyContainer` je klíč, který jste vymysleli v [začínáme s vlastními zásadami](custom-policy-get-started.md). |
| issuer_refresh_token_key | Ano | Certifikát X509 (sada klíčů RSA), který se používá k šifrování obnovovacího tokenu. Nakonfigurovali jste klíč v `B2C_1A_TokenEncryptionKeyContainer` [tématu Začínáme s vlastními zásadami](custom-policy-get-started.md) |















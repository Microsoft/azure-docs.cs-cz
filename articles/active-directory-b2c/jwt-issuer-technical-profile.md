---
title: Definování technického profilu pro vystavitele JWT ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Definujte technický profil vystavitele tokenu JSON (JWT) ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4faa7e68b50b83368837b75cd04be566d816f6d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102119803"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu pro vystavitele tokenů JWT v Azure Active Directory B2C vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) emituje několik typů tokenů zabezpečení při zpracovávání každého toku ověřování. Technický profil vystavitele tokenu JWT vygeneruje token JWT, který se vrátí zpět do aplikace předávající strany. Obvykle je tento technický profil posledním krokem orchestrace v cestě uživatele.

## <a name="protocol"></a>Protokol

Atribut **Name** elementu **Protocol** musí být nastaven na hodnotu `OpenIdConnect` . Nastavte element **OutputTokenFormat** na `JWT` .

Následující příklad ukazuje technický profil pro `JwtIssuer` :

```xml
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="OpenIdConnect" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  <Metadata>
    <Item Key="client_id">{service:te}</Item>
    <Item Key="issuer_refresh_token_user_identity_claim_type">objectId</Item>
    <Item Key="SendTokenResponseBodyWithJsonNumbers">true</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
    <Key Id="issuer_refresh_token_key" StorageReferenceId="B2C_1A_TokenEncryptionKeyContainer" />
  </CryptographicKeys>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-jwt-issuer" />
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Vstupní, výstupní a trvalé deklarace identity

Prvky **InputClaims**, **OutputClaims** a **PersistClaims** jsou prázdné nebo chybí. Chybí také elementy **InutputClaimsTransformations** a **OutputClaimsTransformations** .

## <a name="metadata"></a>Metadata

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | Yes | Deklarace identity, která se má použít jako deklarace identity uživatele v rámci autorizačních kódů OAuth2 a obnovovacích tokenů. Ve výchozím nastavení byste ji měli nastavit na `objectId` , pokud nezadáte jiný typ deklarace identity SubjectNamingInfo. |
| SendTokenResponseBodyWithJsonNumbers | No | Vždy nastavte na `true` . Pro formát starší verze, kde jsou číselné hodnoty zadány jako řetězce místo čísel JSON, nastavte na `false` . Tento atribut je vyžadován pro klienty, kteří mají závislost na předchozí implementaci, která vrátila tyto vlastnosti jako řetězce. |
| token_lifetime_secs | No | Doba života přístupového tokenu. Doba života nosného tokenu OAuth 2,0, který se používá k získání přístupu k chráněnému prostředku. Výchozí hodnota je 3 600 sekund (1 hodina). Minimální (včetně) je 300 sekund (5 minut). Maximální (včetně) je 86 400 sekund (24 hodin). |
| id_token_lifetime_secs | No | Životnost tokenu ID Výchozí hodnota je 3 600 sekund (1 hodina). Minimální (včetně) je 300 sekund (5 minut). Maximální (včetně) je sekund 86 400 (24 hodin). |
| refresh_token_lifetime_secs | No | Aktualizujte životnosti tokenů. Maximální časový interval, po kterém se dá obnovovací token použít k získání nového přístupového tokenu, pokud by vaše aplikace získala obor offline_access. Výchozí hodnota je 120, 9 600 sekund (14 dní). Minimální (včetně) je 86 400 sekund (24 hodin). Maximální (včetně) je 7 776 000 sekund (90 dní). |
| rolling_refresh_token_lifetime_secs | No | Doba životnosti posuvných oken obnovovacího tokenu Po uplynutí tohoto časového období se uživatel nuceně znovu ověří bez ohledu na dobu platnosti nejnovějšího obnovovacího tokenu, který aplikace získala. Pokud nechcete vymáhat dobu trvání klouzavého okna, nastavte hodnotu allow_infinite_rolling_refresh_token na `true` . Výchozí hodnota je 7 776 000 sekund (90 dní). Minimální (včetně) je 86 400 sekund (24 hodin). Maximální (včetně) je 31 536 000 sekund (365 dní). |
| allow_infinite_rolling_refresh_token | No | Je-li nastavena na hodnotu `true` , doba vypršení platnosti posuvných oken aktualizačního tokenu vypršela. |
| IssuanceClaimPattern | No | Řídí deklaraci identity vystavitele (ISS). Jedna z hodnot:<ul><li>AuthorityAndTenantGuid – deklarace ISS zahrnuje název domény, například `login.microsoftonline` nebo `tenant-name.b2clogin.com` , a identifikátor vašeho tenanta https: \/ /Login.microsoftonline.com/00000000-0000-0000-0000-000000000000/v2.0/.</li><li>AuthorityWithTfp – deklarace ISS zahrnuje název domény, například `login.microsoftonline` nebo `tenant-name.b2clogin.com` , identifikátor vašeho tenanta a název zásady předávající strany. https: \/ /Login.microsoftonline.com/TFP/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-Sign-in/v2.0/</li></ul> Výchozí hodnota: AuthorityAndTenantGuid |
| AuthenticationContextReferenceClaimPattern | No | Řídí `acr` hodnotu deklarace identity.<ul><li>Žádné – Azure AD B2C nevydá deklaraci identity ACR</li><li>PolicyId – `acr` deklarace obsahuje název zásady.</li></ul>Možnosti pro nastavení této hodnoty jsou TFP (zásady pro vztahy důvěryhodnosti) a ACR (Referenční dokumentace kontextu ověřování). Doporučuje se nastavení této hodnoty na TFP, aby se hodnota nastavila, zajistěte, aby `<Item>` `Key="AuthenticationContextReferenceClaimPattern"` existovala hodnota s hodnotou a `None` . Do zásady předávající strany přidejte `<OutputClaims>` položku a přidejte tento element `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />` . Také se ujistěte, že vaše zásada obsahuje typ deklarace identity. `<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` |
|RefreshTokenUserJourneyId| No | Identifikátor cesty uživatele, který by měl být proveden během aktualizace požadavku POST [přístupového tokenu](authorization-code-flow.md#4-refresh-the-token) do `/token` koncového bodu. |

## <a name="cryptographic-keys"></a>Kryptografické klíče

Element CryptographicKeys obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| issuer_secret | Yes | Certifikát x509 (sada klíčů RSA), který se použije k podepsání tokenu JWT. `B2C_1A_TokenSigningKeyContainer`Klíč, který nakonfigurujete v části Začínáme [s vlastními zásadami](custom-policy-get-started.md). |
| issuer_refresh_token_key | Yes | Certifikát x509 (sada klíčů RSA), který se použije k zašifrování obnovovacího tokenu. Klíč jste nakonfigurovali v části Začínáme `B2C_1A_TokenEncryptionKeyContainer` [s vlastními zásadami](custom-policy-get-started.md) . |

## <a name="session-management"></a>Správa relací

Chcete-li nakonfigurovat Azure AD B2C relace mezi Azure AD B2C a aplikací předávající strany, v atributu `UseTechnicalProfileForSessionManagement` elementu přidejte odkaz na [OAuthSSOSessionProvider](custom-policy-reference-sso.md#oauthssosessionprovider) relace jednotného přihlašování.















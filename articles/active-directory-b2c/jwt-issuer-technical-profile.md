---
title: Definujte technický profil pro vystavitele tokenu JWT ve vlastních zásadách v Azure Active Directory B2C | Microsoft Docs
description: Definujte technický profil pro vystavitele tokenu JWT ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a5b8f299826c5688eb80eaea11ffc3b2b5176297
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959664"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu pro vystavitele tokenů JWT v Azure Active Directory B2C vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) emituje několik typů tokenů zabezpečení při zpracovávání každého toku ověřování. Technický profil vystavitele tokenu JWT vygeneruje token JWT, který se vrátí zpět do aplikace předávající strany. Obvykle je tento technický profil posledním krokem orchestrace v cestě uživatele.

## <a name="protocol"></a>Protokol

Atribut **Name** elementu **Protocol** musí být nastaven na `None`. Nastavte element **OutputTokenFormat** na hodnotu `JWT`.

Následující příklad ukazuje technický profil pro `JwtIssuer`:

```XML
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="None" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  ...
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Vstupní, výstupní a trvalé deklarace identity

Prvky **InputClaims**, **OutputClaims**a **PersistClaims** jsou prázdné nebo chybí. Chybí také elementy **InutputClaimsTransformations** a **OutputClaimsTransformations** .

## <a name="metadata"></a>Metadata

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | Ano | Deklarace identity, která se má použít jako deklarace identity uživatele v rámci autorizačních kódů OAuth2 a obnovovacích tokenů. Ve výchozím nastavení byste ji měli nastavit na hodnotu `objectId`, pokud nezadáte jiný typ deklarace identity SubjectNamingInfo. |
| SendTokenResponseBodyWithJsonNumbers | Ne | Vždy nastavte na `true`. Pro formát starší verze, kde jsou číselné hodnoty zadány jako řetězce místo čísel JSON, nastavte na `false`. Tento atribut je vyžadován pro klienty, kteří mají závislost na předchozí implementaci, která vrátila tyto vlastnosti jako řetězce. |
| token_lifetime_secs | Ne | Doba života přístupového tokenu. Doba života nosného tokenu OAuth 2,0, který se používá k získání přístupu k chráněnému prostředku. Výchozí hodnota je 3 600 sekund (1 hodina). Minimální (včetně) je 300 sekund (5 minut). Maximální (včetně) je 86 400 sekund (24 hodin). |
| id_token_lifetime_secs | Ne | Životnost tokenu ID Výchozí hodnota je 3 600 sekund (1 hodina). Minimální (včetně) je 300 sekund (5 minut). Maximální (včetně) je sekund 86 400 (24 hodin). |
| refresh_token_lifetime_secs | Ne | Aktualizujte životnosti tokenů. Maximální časový interval, po kterém se dá obnovovací token použít k získání nového přístupového tokenu, pokud by vaše aplikace měla udělený obor offline_access. Výchozí hodnota je 120, 9 600 sekund (14 dní). Minimální (včetně) je 86 400 sekund (24 hodin). Maximální (včetně) je 7 776 000 sekund (90 dní). |
| rolling_refresh_token_lifetime_secs | Ne | Doba životnosti posuvných oken obnovovacího tokenu Po uplynutí tohoto časového období se uživatel nuceně znovu ověří bez ohledu na dobu platnosti nejnovějšího obnovovacího tokenu, který aplikace získala. Pokud nechcete vymáhat dobu trvání klouzavého okna, nastavte hodnotu allow_infinite_rolling_refresh_token na `true`. Výchozí hodnota je 7 776 000 sekund (90 dní). Minimální (včetně) je 86 400 sekund (24 hodin). Maximální (včetně) je 31 536 000 sekund (365 dní). |
| allow_infinite_rolling_refresh_token | Ne | Pokud je nastavená na `true`, doba vypršení platnosti posuvných oken aktualizačního tokenu |
| IssuanceClaimPattern | Ano | Řídí deklaraci identity vystavitele (ISS). Jedna z hodnot:<ul><li>AuthorityAndTenantGuid – deklarace ISS zahrnuje název vaší domény, například `login.microsoftonline` nebo `tenant-name.b2clogin.com`, a identifikátor vašeho tenanta https: \//Login. microsoftonline. com/00000000-0000-0000-0000-000000000000/v 2.0/</li><li>AuthorityWithTfp – deklarace ISS zahrnuje název vaší domény, například `login.microsoftonline` nebo `tenant-name.b2clogin.com`, identifikátor tenanta a název zásady předávající strany. https: \//Login. microsoftonline. com/TFP/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-nebo-Sign-in/v 2.0/</li></ul> |
| AuthenticationContextReferenceClaimPattern | Ne | Určuje hodnotu deklarace `acr`.<ul><li>Žádné – Azure AD B2C nevydá deklaraci identity ACR</li><li>PolicyId – deklarace identity `acr` obsahuje název zásady.</li></ul>Možnosti pro nastavení této hodnoty jsou TFP (zásady pro vztahy důvěryhodnosti) a ACR (Referenční dokumentace kontextu ověřování). Doporučuje se nastavení této hodnoty na TFP. Pokud chcete nastavit hodnotu, ujistěte se, že `<Item>` s `Key="AuthenticationContextReferenceClaimPattern"` existuje a že hodnota je `None`. V zásadách předávající strany přidejte položku `<OutputClaims>`, přidejte tento prvek `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />`. Také se ujistěte, že vaše zásada obsahuje typ deklarace `<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>`. |

## <a name="cryptographic-keys"></a>Kryptografické klíče

Element CryptographicKeys obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| issuer_secret | Ano | Certifikát x509 (sada klíčů RSA), který se použije k podepsání tokenu JWT. Toto je klíč `B2C_1A_TokenSigningKeyContainer`, který jste společně zjistili v části Začínáme [s vlastními zásadami](active-directory-b2c-get-started-custom.md). |
| issuer_refresh_token_key | Ano | Certifikát x509 (sada klíčů RSA), který se použije k zašifrování obnovovacího tokenu. Nakonfigurovali jste klíč `B2C_1A_TokenEncryptionKeyContainer` v části Začínáme [s vlastními zásadami](active-directory-b2c-get-started-custom.md) . |















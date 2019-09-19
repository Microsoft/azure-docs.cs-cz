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
ms.openlocfilehash: 47c5f9a364f4968784cea96a09e938906f39ef4f
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064111"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu pro vystavitele tokenů JWT v Azure Active Directory B2C vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) emituje několik typů tokenů zabezpečení při zpracovávání každého toku ověřování. Technický profil vystavitele tokenu JWT vygeneruje token JWT, který se vrátí zpět do aplikace předávající strany. Obvykle je tento technický profil posledním krokem orchestrace v cestě uživatele.

## <a name="protocol"></a>Protocol

Atribut **Name** elementu **Protocol** musí být nastaven na `None`hodnotu. Nastavte element **OutputTokenFormat** na `JWT`.

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
| issuer_refresh_token_user_identity_claim_type | Ano | Deklarace identity, která se má použít jako deklarace identity uživatele v rámci autorizačních kódů OAuth2 a obnovovacích tokenů. Ve výchozím nastavení byste ji měli nastavit na `objectId`, pokud nezadáte jiný typ deklarace identity SubjectNamingInfo. |
| SendTokenResponseBodyWithJsonNumbers | Ne | Vždy nastavte na `true`. Pro formát starší verze, kde jsou číselné hodnoty zadány jako řetězce místo čísel JSON, `false`nastavte na. Tento atribut je vyžadován pro klienty, kteří mají závislost na předchozí implementaci, která vrátila tyto vlastnosti jako řetězce. |
| token_lifetime_secs | Ne | Doba života přístupového tokenu. Doba života nosného tokenu OAuth 2,0, který se používá k získání přístupu k chráněnému prostředku. Výchozí hodnota je 3 600 sekund (1 hodina). Minimální (včetně) je 300 sekund (5 minut). Maximální (včetně) je 86 400 sekund (24 hodin). |
| id_token_lifetime_secs | Ne | Životnost tokenu ID Výchozí hodnota je 3 600 sekund (1 hodina). Minimální (včetně) je 300 sekund (5 minut). Maximální (včetně) je sekund 86 400 (24 hodin). |
| refresh_token_lifetime_secs | Ne | Aktualizujte životnosti tokenů. Maximální časový interval, po kterém se dá obnovovací token použít k získání nového přístupového tokenu, pokud by vaše aplikace měla udělený obor offline_access. Výchozí hodnota je 120, 9 600 sekund (14 dní). Minimální (včetně) je 86 400 sekund (24 hodin). Maximální (včetně) je 7 776 000 sekund (90 dní). |
| rolling_refresh_token_lifetime_secs | Ne | Doba životnosti posuvných oken obnovovacího tokenu Po uplynutí tohoto časového období se uživatel nuceně znovu ověří bez ohledu na dobu platnosti nejnovějšího obnovovacího tokenu, který aplikace získala. Pokud nechcete vymáhat dobu trvání klouzavého okna, nastavte hodnotu allow_infinite_rolling_refresh_token na `true`. Výchozí hodnota je 7 776 000 sekund (90 dní). Minimální (včetně) je 86 400 sekund (24 hodin). Maximální (včetně) je 31 536 000 sekund (365 dní). |
| allow_infinite_rolling_refresh_token | Ne | Je-li `true`nastavena na hodnotu, doba vypršení platnosti posuvných oken aktualizačního tokenu vypršela. |
| IssuanceClaimPattern | Ano | Řídí deklaraci identity vystavitele (ISS). Jedna z hodnot:<ul><li>AuthorityAndTenantGuid – deklarace ISS zahrnuje název vaší domény, například `login.microsoftonline` nebo `tenant-name.b2clogin.com`, a identifikátor tenanta. https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/v2.0/</li><li>AuthorityWithTfp – deklarace ISS zahrnuje název domény, například `login.microsoftonline` nebo `tenant-name.b2clogin.com`, identifikátor vašeho tenanta a název zásady předávající strany. https://login.microsoftonline.com/tfp/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-sign-in/v2.0/</li></ul> |
| AuthenticationContextReferenceClaimPattern | Ne | Řídí hodnotu `acr` deklarace identity.<ul><li>Žádné – Azure AD B2C nevydá deklaraci identity ACR</li><li>PolicyId – `acr` deklarace obsahuje název zásady.</li></ul>Možnosti pro nastavení této hodnoty jsou TFP (zásady pro vztahy důvěryhodnosti) a ACR (Referenční dokumentace kontextu ověřování). Doporučuje se nastavení této hodnoty na TFP, aby se hodnota nastavila, `<Item>` zajistěte `Key="AuthenticationContextReferenceClaimPattern"` , aby `None`existovala hodnota s hodnotou a. Do zásady `<OutputClaims>` předávající strany přidejte položku a přidejte tento element `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />`. Také se ujistěte, že vaše zásada obsahuje typ deklarace identity.`<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` |

## <a name="cryptographic-keys"></a>Kryptografické klíče

Element CryptographicKeys obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| issuer_secret | Ano | Certifikát x509 (sada klíčů RSA), který se použije k podepsání tokenu JWT. To je klíč `B2C_1A_TokenSigningKeyContainer` , který jste zjistili v části [Začínáme s vlastními zásadami](active-directory-b2c-get-started-custom.md). |
| issuer_refresh_token_key | Ano | Certifikát x509 (sada klíčů RSA), který se použije k zašifrování obnovovacího tokenu. `B2C_1A_TokenEncryptionKeyContainer` Klíč jste nakonfigurovali v části Začínáme [s vlastními zásadami](active-directory-b2c-get-started-custom.md) . |















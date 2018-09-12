---
title: Definování technický profil pro vystavitele tokenu JWT ve vlastních zásadách v Azure Active Directory B2C | Dokumentace Microsoftu
description: Definice technický profil vystavitele tokenu JWT ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: cb004745cfbc6af185a06c4787fb34326eccc69a
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44381130"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technický profil pro vystavitele tokenu JWT ve vlastních zásadách pro Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C vyzařuje několik typů tokeny zabezpečení, které zpracovává každý tok ověřování. Technický profil vystavitele tokenu JWT vydá token JWT, který se vrátí zpět do aplikace předávající strany. Tento technický profil je obvykle poslední krok Orchestrace v cestě uživatele.

## <a name="protocol"></a>Protocol (Protokol)

**Název** atribut **protokol** elementu musí být nastavena na `None`. Nastavte **OutputTokenFormat** elementu `JWT`.

Následující příklad ukazuje technický profil pro `JwtIssuer`:

```XML
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="None" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  ...
</TechnicalProfile>
```
 
## <a name="input-output-and-persist-claims"></a>Vstupní, výstupní a zachovat deklarací identity

**InputClaims**, **OutputClaims**, a **PersistClaims** prvky jsou prázdné nebo zcela chybět. **InutputClaimsTransformations** a **OutputClaimsTransformations** prvky jsou také chybí.

## <a name="metadata"></a>Metadata

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | Ano | Deklarace identity, který se má použít jako identitu uživatele deklarací identity v rámci OAuth2 autorizační kódy a obnovovacích tokenů. Ve výchozím nastavení, měli byste ho nastavit `objectId`, pokud neurčíte jinou SubjectNamingInfo typ deklarace identity. | 
| SendTokenResponseBodyWithJsonNumbers | Ne | Vždy nastaven na hodnotu `true`. Pro starší verze formátu, ve kterém jsou číselné hodnoty zadané jako řetězce místo čísla JSON, nastavte na `false`. Tento atribut je potřeba pro klienty, kteří trvalo závislost na předchozích implementace, která vrátí vlastnosti jako řetězce. | 
| token_lifetime_secs | Ne | Životnost tokenu přístupu. Doba života nosného tokenu OAuth 2.0, který se používá k získání přístupu ke chráněnému prostředku. Výchozí hodnota je 3 600 sekund (1 hodina). (Včetně) minimální hodnota je 300 sekund (5 minut). Maximální počet (včetně) je 86 400 sekund (24 hodin). | 
| id_token_lifetime_secs | Ne | Životnost tokenů ID. Výchozí hodnota je 3 600 sekund (1 hodina). (Včetně) minimální hodnota je 300 sekund (5 minut). Maximální počet (včetně) je sekund 86,400 (24 hodin). | 
| refresh_token_lifetime_secs | Ne | Aktualizujte životností tokenů. Maximální časové období, před kterým token obnovení je možné získat nový přístupový token, pokud vaše aplikace byl udělen offline_access oboru. Výchozí hodnota je 120,9600 sekund (14 dní). (Včetně) minimální hodnota je 86 400 sekund (24 hodin). Maximální počet (včetně) je 7,776,000 sekund (90 dnů). | 
| rolling_refresh_token_lifetime_secs | Ne | Aktualizujte token doba života posuvného okna. Po tomto časovém období uplyne uživatel bude muset donutit, bez ohledu na období platnosti posledního obnovovací token získaný aplikace. Pokud nechcete, aby k vynucení doba života posuvného okna, nastavte hodnotu allow_infinite_rolling_refresh_token k `true`. Výchozí hodnota je 7,776,000 sekund (90 dnů). (Včetně) minimální hodnota je 86 400 sekund (24 hodin). Maximální počet (včetně) je 31,536,000 sekund (365 dní). | 
| rolling_refresh_token_lifetime_secs | Ne | Pokud nastavena na `true`, obnovovací token klouzavým 5minutovým životnost nikdy nevyprší. |
| IssuanceClaimPattern | Ano | Ovládací prvky deklarace identity vystavitele (iss). Jedna z hodnot:<ul><li>AuthorityAndTenantGuid - deklaraci identity iss zahrnuje název domény, jako například `login.microsoftonline` nebo `tenant-name.b2clogin.com`a svůj identifikátor tenanta https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/v2.0/</li><li>AuthorityWithTfp - deklaraci identity iss zahrnuje název domény, jako například `login.microsoftonline` nebo `tenant-name.b2clogin.com`, váš tenant identifikátor a název zásady předávající strany. https://login.microsoftonline.com/tfp/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-sign-in/v2.0/</li></ul> | 
| AuthenticationContextReferenceClaimPattern | Ne | Ovládací prvky `acr` hodnoty deklarace identity.<ul><li>Žádný - není Azure AD B2C vydávání deklarací acr</li><li>Vlastnosti PolicyId - `acr` deklarací identity obsahuje název zásady</li></ul>Možnosti pro nastavení této hodnoty jsou TFP (zásady důvěryhodnosti framework) a služby ACR (authentication kontextu odkaz). Doporučuje se tuto hodnotu nastavíte na TFP, ujistěte se, pokud chcete nastavit hodnotu, `<Item>` s `Key="AuthenticationContextReferenceClaimPattern"` existuje a má hodnotu `None`. V přijímající strany zásady, přidejte <OutputClaims> položky, přidejte tento element `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />`. Také se ujistěte, že vaše zásady obsahuje typ deklarace identity `<ClaimType Id="trustFrameworkPolicy"> <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` | 

## <a name="cryptographic-keys"></a>Kryptografické klíče

CryptographicKeys element obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| issuer_secret | Ano | X509 certifikát (sadu klíčů RSA) se má použít k podepisování tokenů JWT token. Toto je `B2C_1A_TokenSigningKeyContainer` klíče, které jste cofigured v [začít pracovat s vlastními zásadami](active-directory-b2c-get-started-custom.md). | 
| issuer_refresh_token_key | Ano | X509 certifikát (sadu klíčů RSA) se má použít k šifrování tokenu obnovení. Můžete nakonfigurovat `B2C_1A_TokenEncryptionKeyContainer` klíče v [začít pracovat s vlastními zásadami](active-directory-b2c-get-started-custom.md) |















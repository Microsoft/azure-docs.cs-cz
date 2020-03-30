---
title: Žádost o přístupový token – Služba Azure Active Directory B2C | Dokumenty společnosti Microsoft
description: Přečtěte si, jak požádat o přístupový token z Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8358d3378ea892ebeef653bcb51243c9f1aa0b8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259770"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Vyžádání přístupového tokenu ve službě Azure Active Directory B2C

*Přístupový token* obsahuje deklarace identity, které můžete použít ve službě Azure Active Directory B2C (Azure AD B2C) k identifikaci udělených oprávnění pro vaše rozhraní API. Při volání serveru prostředků musí být v požadavku HTTP k dispozici přístupový token. Přístupový token se označuje jako **access_token** v odpovědích z Azure AD B2C.

Tento článek ukazuje, jak požádat o přístupový token pro webovou aplikaci a webové rozhraní API. Další informace o tokenech v Azure AD B2C najdete [v přehledu tokenů ve službě Azure Active Directory B2C](tokens-overview.md).

> [!NOTE]
> **Řetězce webového rozhraní API (on-behalf-Of) není podporována Azure AD B2C.** - Mnoho architektur obsahuje webové rozhraní API, které potřebuje volat jiné podřízené webové rozhraní API, obě zabezpečené Azure AD B2C. Tento scénář je běžné v klientech, které mají webové rozhraní API back-end, což zase volá jinou službu. Tento scénář zřetězené webové rozhraní API lze podporovat pomocí OAuth 2.0 JWT pověření grant, jinak známý jako tok on-behalf-of. Tok na účet však není aktuálně implementována v Azure AD B2C.

## <a name="prerequisites"></a>Požadavky

- [Vytvořte tok uživatelů,](tutorial-create-user-flows.md) který uživatelům umožní přihlásit se a přihlásit se k vaší aplikaci.
- Pokud jste tak ještě neučinili, [přidejte do klienta Služby Azure Active Directory B2C aplikaci webového rozhraní API](add-web-application.md).

## <a name="scopes"></a>Obory

Obory poskytují způsob správy oprávnění k chráněným prostředkům. Je-li požadován přístupový token, musí klientská aplikace zadat požadovaná oprávnění v parametru **oboru** požadavku. Chcete-li například zadat `read` **hodnotu oboru** pro rozhraní API, `https://contoso.onmicrosoft.com/api`které má `https://contoso.onmicrosoft.com/api/read`identifikátor URI **ID aplikace** , bude oboru .

Webové rozhraní API používá obory k implementaci řízení přístupu na základě oboru. Například uživatelé webového rozhraní API můžou mít přístup ke čtení i zápisu nebo přístup pouze ke čtení. Chcete-li získat více oprávnění ve stejném požadavku, můžete přidat více položek v parametru jednoho **oboru** požadavku, oddělenémezerami.

Následující příklad ukazuje, že obory jsou dekódovány v adrese URL:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

Následující příklad ukazuje obory kódované v adrese URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Pokud požadujete více oborů, než je uděleno pro klientskou aplikaci, volání proběhne úspěšně, pokud je uděleno alespoň jedno oprávnění. Deklarace **scp** ve výsledném přístupovém tokenu je naplněna pouze oprávněními, která byla úspěšně udělena. Standard OpenID Connect určuje několik speciálních hodnot oboru. Následující obory představují oprávnění pro přístup k profilu uživatele:

- **openid** - Požaduje token ID.
- **offline_access** - Požaduje obnovovací token pomocí [toků kódu ověřování](authorization-code-flow.md).

Pokud **parametr response_type** `/authorize` v `token`požadavku obsahuje , musí parametr **oboru** obsahovat alespoň jeden obor prostředků než `openid` a `offline_access` který bude udělen. V opačném `/authorize` případě se požadavek nezdaří.

## <a name="request-a-token"></a>Žádost o token

Chcete-li požádat o přístupový token, potřebujete autorizační kód. Níže je uveden příklad požadavku `/authorize` na koncový bod pro autorizační kód. Vlastní domény nejsou podporovány pro použití s přístupovými tokeny. V adrese URL požadavku použijte svou doménu tenant-name.onmicrosoft.com.

V následujícím příkladu nahradíte tyto hodnoty:

- `<tenant-name>`- Název vašeho klienta Azure AD B2C.
- `<policy-name>`- Název vlastní zásady nebo toku uživatele.
- `<application-ID>`- Identifikátor aplikace webové aplikace, kterou jste zaregistrovali pro podporu toku uživatelů.
- `<redirect-uri>`- Identifikátor **URI přesměrování,** který jste zadali při registraci klientské aplikace.

```HTTP
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&response_type=code
```

Odpověď s autorizačním kódem by měla být podobná tomuto příkladu:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

Po úspěšném přijetí autorizačního kódu jej můžete použít k vyžádání přístupového tokenu:

```HTTP
POST <tenant-name>.onmicrosoft.com/oauth2/v2.0/token?p=<policy-name> HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

Měli byste vidět něco podobného následující odpověď:

```JSON
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrN...",
    "token_type": "Bearer",
    "not_before": 1549647431,
    "expires_in": 3600,
    "expires_on": 1549651031,
    "resource": "f2a76e08-93f2-4350-833c-965c02483b11",
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiJjNjRhNGY3ZC0zMDkxLTRjNzMtYTcyMi1hM2YwNjk0Z..."
}
```

Při https://jwt.ms použití ke kontrole přístupový token, který byl vrácen, měli byste vidět něco podobného v následujícím příkladu:

```JSON
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dl..."
}.{
  "iss": "https://contoso0926tenant.b2clogin.com/c64a4f7d-3091-4c73-a7.../v2.0/",
  "exp": 1549651031,
  "nbf": 1549647431,
  "aud": "f2a76e08-93f2-4350-833c-965...",
  "oid": "1558f87f-452b-4757-bcd1-883...",
  "sub": "1558f87f-452b-4757-bcd1-883...",
  "name": "David",
  "tfp": "B2C_1_signupsignin1",
  "nonce": "anyRandomValue",
  "scp": "read",
  "azp": "38307aee-303c-4fff-8087-d8d2...",
  "ver": "1.0",
  "iat": 1549647431
}.[Signature]
```

## <a name="next-steps"></a>Další kroky

- Informace o [konfiguraci tokenů v Azure AD B2C](configure-tokens.md)

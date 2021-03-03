---
title: Vyžádání přístupového tokenu – Azure Active Directory B2C | Microsoft Docs
description: Tady se dozvíte, jak si ve službě Azure Active Directory B2C vyžádat přístupový token.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/26/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e5168d5e5e3935da267fb26f38735a88bdfd7837
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101654472"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Vyžádání přístupového tokenu ve službě Azure Active Directory B2C

*Přístupový token* obsahuje deklarace identity, pomocí kterých můžete v Azure Active Directory B2C (Azure AD B2C) identifikovat oprávnění udělená vašim rozhraním API. Při volání serveru prostředků musí požadavek HTTP obsahovat přístupový token. Přístupový token se v odpovědích služby Azure AD B2C označuje textem **access_token**.

V tomto článku se dozvíte, jak požádat o přístupový token pro webovou aplikaci a webové rozhraní API. Další informace o tokenech v Azure AD B2C najdete v [přehledu tokenů v Azure Active Directory B2C](tokens-overview.md).

> [!NOTE]
> **Azure AD B2C nepodporuje řetězce webových rozhraní API (On-Behalf-Of).** - Mnoho architektur obsahuje webové rozhraní API, které potřebuje volat podřízené webové rozhraní API, přičemž obě rozhraní jsou zabezpečená pomocí Azure AD B2C. Tento scénář je běžný u klientů, které mají back-end s webovým rozhraním API, který volá zase jinou službu. Podporu tohoto scénáře se zřetězenými webovými rozhraními API umožňuje udělení přihlašovacích údajů nosného tokenu OAuth 2.0 JWT, označovaného také jako tok On-Behalf-Of. V Azure AD B2C nicméně není tok On-Behalf-Of v současné době implementovaný.

## <a name="prerequisites"></a>Předpoklady

- [Vytvořte uživatelský tok](tutorial-create-user-flows.md), který uživatelům umožní registraci a přihlášení do vaší aplikace.
- Pokud jste to ještě neudělali, [přidejte do svého tenanta Azure Active Directory B2C aplikaci webového rozhraní API](add-web-api-application.md).

## <a name="scopes"></a>Obory

Obory představují způsob, jak spravovat oprávnění k chráněným prostředkům. Při žádosti o přístupový token musí klientská aplikace uvést požadovaná oprávnění v parametru **scope** požadavku. Pokud například chcete u rozhraní API, které má **identifikátor URI aplikace** `https://contoso.onmicrosoft.com/api`, určit **hodnotu oboru** `read`, bude obor `https://contoso.onmicrosoft.com/api/read`.

Webové rozhraní API používá obory k implementaci řízení přístupu na základě oboru. Například uživatelé webového rozhraní API můžou mít přístup ke čtení i zápisu nebo přístup pouze ke čtení. Pokud chcete ve stejném požadavku získat více oprávnění, můžete do jednoho parametru **scope** požadavku zadat více položek oddělených mezerami.

Následující příklad ukazuje dekódované obory v adrese URL:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

Následující příklad ukazuje zakódované obory v adrese URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Pokud zadáte požadavek na více oborů, než kolik se pro danou klientskou aplikaci uděluje, bude volání úspěšné, pokud dojde k udělení alespoň jednoho oprávnění. V deklaraci identity **scp** ve výsledném přístupovém tokenu se automaticky uvedou jednom úspěšně udělená oprávnění. 

### <a name="openid-connect-scopes"></a>Obory OpenID Connect

Standard OpenID Connect určuje několik speciálních hodnot oboru. Následující obory reprezentují oprávnění pro přístup k profilu uživatele:

- **openid** – Vyžádá token ID.
- **offline_access** – Vyžádá token obnovení pomocí [toků autorizačního kódu](authorization-code-flow.md).
- **00000000-0000-0000-0000-000000000000** – použití ID klienta jako oboru znamená, že vaše aplikace potřebuje přístupový token, který můžete použít pro vlastní službu nebo webové rozhraní API reprezentované stejným ID klienta.

Pokud parametr **response_type** požadavku `/authorize` obsahuje `token`, musí parametr **scope** obsahovat nejméně jeden jiný obor prostředku než `openid` a `offline_access`, který se má udělit. V opačném případě nebude požadavek `/authorize` úspěšný.

## <a name="request-a-token"></a>Vyžádání tokenu

K vyžádání přístupového tokenu potřebujete autorizační kód. Následuje příklad požadavku na autorizační kód zaslaného koncovému bodu `/authorize`. U vlastních domén se použití přístupových tokenů nepodporuje. V adrese URL požadavku použijte svoji doménu nazev-tenanta.onmicrosoft.com.

V následujícím příkladu nahraďte tyto hodnoty:

- `<tenant-name>` – název domény tenanta Azure AD B2C.
- `<policy-name>` – název vlastní zásady nebo toku uživatele.
- `<application-ID>` – identifikátor webové aplikace, kterou jste zaregistrovali za účelem zpracování toku uživatele.
- `<redirect-uri>` – **identifikátor URI pro přesměrování**, který jste zadali při registraci klientské aplikace.

```http
GET https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&response_type=code
```

Odpověď s autorizačním kódem by se měla podobat tomuto příkladu:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

Po úspěšném přijetí autorizačního kódu můžete kód použít k vyžádání přístupového tokenu:

```http
POST <tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/token HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

Měla by se zobrazit odpověď podobná této:

```json
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

Když zkusíte vrácený přístupový token zadat do nástroje https://jwt.ms, měl by se zobrazit podobný výstup jako v tomto příkladu:

```json
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

- Přečtěte si, jak [konfigurovat tokeny v Azure AD B2C](configure-tokens.md).

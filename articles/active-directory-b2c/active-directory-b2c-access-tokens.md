---
title: Žádost o přístupový token – Azure Active Directory B2C | Dokumentace Microsoftu
description: Zjistěte, jak požádat o přístupový token z Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 5670d8b3c97cc1f9f6d149e8eadaa60d527e45f5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60361111"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Žádost o přístupový token v Azure Active Directory B2C

*Přístupový token* deklaracemi identity, můžete použít v Azure Active Directory (Azure AD) B2C k identifikaci udělená oprávnění k rozhraním API. Při volání metody zdrojový server, musí být přístupový token v požadavku HTTP. Přístupový token je označena jako **access_token** v odpovědi z Azure AD B2C. 

V tomto článku se dozvíte, jak požádat o přístupový token pro webové aplikace a webového rozhraní API. Další informace o tokeny v Azure AD B2C, najdete v článku [přehled tokenů v Azure Active Directory B2C](active-directory-b2c-reference-tokens.md).

> [!NOTE]
> **Webové rozhraní API řetězy (On-Behalf-Of) není podporována službou Azure AD B2C.** – Mnoho architektur zahrnuje webové rozhraní API, které potřebuje volat podřízené webové rozhraní API, obě jsou zabezpečené pomocí Azure AD B2C. Tento scénář je častý u klientů, kteří mají webového rozhraní API back-end, který pak volá jinou službu. Tento scénář zřetězených webových rozhraní API může být podporován pomocí udělení přihlašovacích údajů nosiče OAuth 2.0 JWT, jinak známé jako tok On-Behalf-Of. Nicméně tok On-Behalf-Of není aktuálně implementován v Azure AD B2C.

## <a name="prerequisites"></a>Požadavky

- [Vytvořit tok uživatele](tutorial-create-user-flows.md) umožňují uživatelům registraci a přihlášení do vaší aplikace.
- Pokud jste tak již neučinili, [přidat webovou aplikaci s rozhraním API pro vašeho tenanta Azure Active Directory B2C](add-web-application.md).

## <a name="scopes"></a>Obory

Obory poskytují způsob, jak spravovat oprávnění k chráněným prostředkům. Pokud se požaduje přístupového tokenu, klientská aplikace potřebuje k určení požadovaných oprávnění v **oboru** parametr žádosti. Například, chcete-li určit **hodnota rozsahu** z `read` pro rozhraní API, která má **identifikátor ID URI aplikace** z `https://contoso.onmicrosoft.com/api`, oboru by `https://contoso.onmicrosoft.com/api/read`.

Webové rozhraní API používá obory k implementaci řízení přístupu na základě oboru. Například uživatelé webového rozhraní API můžou mít přístup ke čtení i zápisu nebo přístup pouze ke čtení. Chcete-li získat více oprávnění u stejné žádosti, můžete přidat více položek v jedné **oboru** parametr požadavku, oddělené mezerami.

Následující příklad ukazuje obory dekódovat. v adrese URL:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

Následující příklad ukazuje obory kódovaný v adrese URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Pokud jste požádali o další obory, než jaké jsou udělena pro klientské aplikace, bude volání úspěšné, pokud aspoň jedno oprávnění jsou udělena. **Spojovací bod služby** deklarace identity v výsledný přístupový token se vyplní úspěšně udělená oprávnění. Standard OpenID Connect určuje několika speciální rozsah hodnot. Následující obory představují oprávnění k přístupu k profilu uživatele:

- **openid** -vyžádá ID token.
- **offline_access** -vyžádá aktualizaci tokenu pomocí [toku kódu autorizace](active-directory-b2c-reference-oauth-code.md).

Pokud **typ odpovědi** parametr `/authorize` požadavek zahrnuje `token`, **oboru** parametr musí obsahovat nejméně jeden prostředek oboru jiné než `openid` a `offline_access`, která budou udělena. V opačném případě `/authorize` z žádostí selže.

## <a name="request-a-token"></a>Požádat o token

Požádat o token přístupu, potřebujete autorizační kód. Tady je příklad požadavku na `/authorize` koncový bod pro autorizační kód. Vlastní domény nejsou podporovány pro použití s přístupovými tokeny. Použití vašeho tenanta name.onmicrosoft.com domény v adrese URL požadavku.

V následujícím příkladu nahraďte tyto hodnoty:

- `<tenant-name>` – Název vašeho tenanta Azure AD B2C.
- `<policy-name>` -Název vlastní zásady nebo uživatel toku.
- `<application-ID>` -Identifikátor aplikace webové aplikace, které jste zaregistrovali pro podporu tok uživatele.
- `<redirect-uri>` – **Identifikátor URI pro přesměrování** , kterou jste zadali při registraci klientské aplikace.

```
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://tenant-name>.onmicrosoft.com/api/read
&response_type=code 
```

Odpověď se autorizační kód by měl vypadat přibližně takto:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

Po přijetí úspěšně autorizační kód, slouží k vyžádání tokenu přístupu:

```
POST <tenant-name>.onmicrosoft.com/oauth2/v2.0/token?p=<policy-name> HTTP/1.1
Host: https://<tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

Zobrazí se podobná následující odpovědi:

```
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

Při použití https://jwt.ms prozkoumat přístupový token, který byl vrácen, by měl vypadat podobně jako v následujícím příkladu:

```
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

## <a name="next-steps"></a>Další postup

- Další informace o tom, jak [konfigurace tokenů v Azure AD B2C](configure-tokens.md)

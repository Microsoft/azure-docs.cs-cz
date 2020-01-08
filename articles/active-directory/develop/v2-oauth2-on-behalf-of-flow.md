---
title: Microsoft Identity Platform & OAuth 2.0 za běhu služby Flow | Azure
description: Tento článek popisuje, jak pomocí zpráv HTTP implementovat ověřování Service to Service over pomocí toku OAuth 2.0 za běhu.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 811fc7a4fc5d8ffba894bad837e95d6b27ecc8c3
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689413"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Microsoft Identity Platform a OAuth 2,0 s tokem za chodu

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

OBO (OAuth 2,0 on-of-jménem toku) slouží k použití případu, kdy aplikace vyvolá službu nebo webové rozhraní API, která zase potřebuje volat jiné služby nebo webové rozhraní API. Nápad je rozšířit identitu delegovaného uživatele a oprávnění prostřednictvím řetězce požadavků. Aby služba střední vrstvy prováděla ověřené požadavky na službu pro příjem dat, musí jménem uživatele zabezpečit přístupový token od platformy Microsoft identity.

Tento článek popisuje, jak programovat přímo s protokolem ve vaší aplikaci.  Pokud je to možné, doporučujeme místo toho použít podporované knihovny Microsoft Authentication Library (MSAL) k [získání tokenů a volání zabezpečených webových rozhraní API](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Podívejte se také na [ukázkové aplikace, které používají MSAL](sample-v2-code.md).

> [!NOTE]
>
> - Koncový bod platformy Microsoft Identity Platform nepodporuje všechny scénáře a funkce. Pokud chcete zjistit, jestli byste měli použít koncový bod platformy Microsoft identity, přečtěte si informace o [omezeních platformy Microsoft Identity](active-directory-v2-limitations.md). 
> - Od května 2018 se v OBO toku nedá použít nějaký `id_token` odvozený z implicitního toku. Jednostránkové aplikace (jednostránkové) by měly předat **přístupovému** klientovi střední vrstvy přístupový token, aby se místo toho prováděly OBO toky. Další informace o tom, kteří klienti můžou provádět volání OBO, najdete v tématu [omezení](#client-limitations).

## <a name="protocol-diagram"></a>Diagram protokolu

Předpokládejme, že uživatel byl ověřený v aplikaci pomocí [toku udělení autorizačního kódu OAuth 2,0](v2-oauth2-auth-code-flow.md) nebo jiného toku přihlášení. V tomto okamžiku má aplikace přístupový token *pro rozhraní API a* (token A) s deklaracemi identity uživatele a souhlasem pro přístup k webovému rozhraní API střední vrstvy (API a). Rozhraní API nyní potřebuje ověřený požadavek webového rozhraní API pro příjem dat (API B).

Následující kroky představují tok OBO a jsou vysvětleny pomocí následujícího diagramu.

![Zobrazuje tok za běhu OAuth 2.0.](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. Klientská aplikace vytvoří požadavek na rozhraní API A s tokenem A (s `aud` deklarací API A).
1. Rozhraní API A ověřuje koncový bod vystavování tokenu platformy Microsoft identity a požaduje token pro přístup k rozhraní API B.
1. Koncový bod vystavování tokenu platformy Microsoft Identity ověřuje přihlašovací údaje A pověření rozhraní API spolu s tokenem a a vydá přístupový token pro rozhraní API B (token B) k rozhraní API A.
1. Token B je nastaven rozhraním API A v autorizační hlavičce požadavku na rozhraní API B.
1. Data z zabezpečeného prostředku jsou vrácena rozhraním API B do rozhraní API a a odtud na klienta.

> [!NOTE]
> V tomto scénáři nemá služba střední vrstvy žádnou interakci s uživatelem, aby získala souhlas uživatele s přístupem k rozhraní API pro příjem dat. Proto možnost udělení přístupu k rozhraní API pro příjem dat se při ověřování prezentuje jako součást kroku souhlasu. Informace o tom, jak tuto aplikaci nastavit, najdete v tématu [získání souhlasu pro aplikaci střední vrstvy](#gaining-consent-for-the-middle-tier-application).

## <a name="service-to-service-access-token-request"></a>Žádost o přístupový token služby na službu

Pokud chcete požádat o přístupový token, vytvořte HTTP POST do koncového bodu tokenu platformy Microsoft Identity Platform-Specific s následujícími parametry.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Existují dva případy, v závislosti na tom, jestli se klientská aplikace rozhodne zabezpečit sdíleným tajným klíčem nebo certifikátem.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>První případ: žádost přístupového tokenu se sdíleným tajným klíčem

Při použití sdíleného tajného klíče obsahuje požadavek na přístupový token služby na službu následující parametry:

| Parametr |  | Popis |
| --- | --- | --- |
| `grant_type` | Požaduje se | Typ žádosti o token Pro požadavek používající token JWT musí být hodnota `urn:ietf:params:oauth:grant-type:jwt-bearer`. |
| `client_id` | Požaduje se | ID aplikace (klienta), které stránka [Azure Portal-registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) přiřadila k vaší aplikaci. |
| `client_secret` | Požaduje se | Tajný kód klienta, který jste vygenerovali pro vaši aplikaci na stránce Azure Portal-Registrace aplikací. |
| `assertion` | Požaduje se | Hodnota tokenu použitého v požadavku.  Tento token musí mít cílovou skupinu aplikace, která tuto žádost OBO (aplikace označuje pole `client-id`). |
| `scope` | Požaduje se | Mezerou oddělený seznam oborů pro požadavek na token. Další informace najdete v tématu [obory](v2-permissions-and-consent.md). |
| `requested_token_use` | Požaduje se | Určuje, jak se má požadavek zpracovat. V toku OBO musí být hodnota nastavená na `on_behalf_of`. |

#### <a name="example"></a>Příklad:

Následující příspěvek HTTP požaduje přístupový token a aktualizuje token s `user.read`m oborem pro https://graph.microsoft.com webové rozhraní API.

```
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE0OTM5MjA5MTYsIm5iZiI6MTQ5MzkyMDkxNiwiZXhwIjoxNDkzOTI0ODE2LCJhaW8iOiJBU1FBMi84REFBQUFnZm8vNk9CR0NaaFV2NjJ6MFFYSEZKR0VVYUIwRUlIV3NhcGducndMMnVrPSIsIm5hbWUiOiJOYXZ5YSBDYW51bWFsbGEiLCJvaWQiOiJkNWU5NzljNy0zZDJkLTQyYWYtOGYzMC03MjdkZDRjMmQzODMiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJuYWNhbnVtYUBtaWNyb3NvZnQuY29tIiwic3ViIjoiZ1Q5a1FMN2hXRUpUUGg1OWJlX1l5dVZNRDFOTEdiREJFWFRhbEQzU3FZYyIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjN5U3F4UHJweUVPd0ZsTWFFMU1PQUEiLCJ2ZXIiOiIyLjAifQ.TPPJSvpNCSCyUeIiKQoLMixN1-M-Y5U0QxtxVkpepjyoWNG0i49YFAJC6ADdCs5nJXr6f-ozIRuaiPzy29yRUOdSz_8KqG42luCyC1c951HyeDgqUJSz91Ku150D9kP5B9-2R-jgCerD_VVuxXUdkuPFEl3VEADC_1qkGBiIg0AyLLbz7DTMp5DvmbC09DhrQQiouHQGFSk2TPmksqHm3-b3RgeNM1rJmpLThis2ZWBEIPx662pjxL6NJDmV08cPVIcGX4KkFo54Z3rfwiYg4YssiUc4w-w3NJUBQhnzfTl4_Mtq2d7cVlul9uDzras091vFy32tWkrpa970UvdVfQ
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Druhý případ: žádost o přístupový token s certifikátem

Požadavek na přístupový token služby na službu s certifikátem obsahuje následující parametry:

| Parametr |  | Popis |
| --- | --- | --- |
| `grant_type` | Požaduje se | Typ požadavku tokenu Pro požadavek používající token JWT musí být hodnota `urn:ietf:params:oauth:grant-type:jwt-bearer`. |
| `client_id` | Požaduje se |  ID aplikace (klienta), které stránka [Azure Portal-registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) přiřadila k vaší aplikaci. |
| `client_assertion_type` | Požaduje se | Hodnota musí být `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| `client_assertion` | Požaduje se | Kontrolní výraz (webový token JSON), který potřebujete k vytvoření a podepsání certifikátu, který jste zaregistrovali jako přihlašovací údaje pro vaši aplikaci. Informace o tom, jak zaregistrovat certifikát a formát kontrolního výrazu, najdete v tématu [přihlašovací údaje certifikátu](active-directory-certificate-credentials.md). |
| `assertion` | Požaduje se | Hodnota tokenu použitého v požadavku. |
| `requested_token_use` | Požaduje se | Určuje, jak se má požadavek zpracovat. V toku OBO musí být hodnota nastavená na `on_behalf_of`. |
| `scope` | Požaduje se | Mezerou oddělený seznam oborů pro požadavek na token. Další informace najdete v tématu [obory](v2-permissions-and-consent.md).|

Všimněte si, že parametry jsou skoro stejné jako v případě požadavku pomocí sdíleného tajného klíče s tím rozdílem, že parametr `client_secret` je nahrazen dvěma parametry: `client_assertion_type` a `client_assertion`.

#### <a name="example"></a>Příklad:

Následující příspěvek HTTP požaduje přístupový token s oborem `user.read` pro https://graph.microsoft.com webové rozhraní API s certifikátem.

```
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="service-to-service-access-token-response"></a>Odezva na token Service to Service Access

Odpověď na úspěch je odpověď protokolu JSON OAuth 2,0 s následujícími parametry.

| Parametr | Popis |
| --- | --- |
| `token_type` | Určuje hodnotu typu tokenu. Jediným typem, který podporuje platforma Microsoft identity, je `Bearer`. Další informace o nosných tokenech najdete v části [autorizační rozhraní OAuth 2,0: použití nosných tokenů (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| `scope` | Rozsah přístupu uděleného v tokenu. |
| `expires_in` | Doba, po kterou je přístupový token platný (v sekundách). |
| `access_token` | Požadovaný přístupový token Volající služba může tento token použít k ověření pro přijímající službu. |
| `refresh_token` | Obnovovací token požadovaného přístupového tokenu. Volající služba může tento token použít k vyžádání jiného přístupového tokenu po vypršení platnosti aktuálního přístupového tokenu. Aktualizační token je poskytován pouze v případě, že byl požadován obor `offline_access`. |

### <a name="success-response-example"></a>Příklad odpovědi na úspěch

Následující příklad ukazuje odpověď na úspěch na žádost o přístupový token pro https://graph.microsoft.com webové rozhraní API.

```
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4AnqPnvFqnzS3GiikHr5wBM1bV1YyjH3nUeIhKhqJWGwqJFRqs2sE_rqUfz7__3J92JDpi6gDdCZNNaXgreQsH89kLCVNYZeN6kGuFGZrjwxp1wS2JYc97E_3reXBxkHrA09K5aR-WsSKCEjf6WI23FhZMTLhk_ZKOe_nWvcvLj13FyvSrTMZV2cmzyCZDqEHtPVLJgSoASuQlD2NXrfmtcmgWfc3uJSrWLIDSn4FEmVDA63X6EikNp9cllH3Gp7Vzapjlnws1NQ1_Ff5QrmBHp_LKEIwfzVKnLLrQXN0EzP8f6AX6fdVTaeKzm7iw6nH0vkPRpUeLc3q_aNsPzqcTOnFfgng7t2CXUsMAGH5wclAyFCAwL_Cds7KnyDLL7kzOS5AVZ3Mqk2tsPlqopAiHijZaJumdTILDudwKYCFAMpUeUwEf9JmyFjl2eIWPmlbwU7cHKWNvuRCOYVqbsTTpJthwh4PvsL5ov5CawH_TaV8omG_tV6RkziHG9urk9yp2PH9gl7Cv9ATa3Vt3PJWUS8LszjRIAJmyw_EhgHBfYCvEZ8U9PYarvgqrtweLcnlO7BfnnXYEC18z_u5wemAzNBFUje2ttpGtRmRic4AzZ708tBHva2ePJWGX6pgQbiWF8esOrvWjfrrlfOvEn1h6YiBW291M022undMdXzum6t1Y1huwxHPHjCAA"
}
```

> [!NOTE]
> Výše přístupový token je ve formátu tokenu v 1.0. Důvodem je to, že token je k dispozici na základě dostupného **prostředku** . Microsoft Graph je instalační program, který přijímá tokeny v 1.0, takže platforma Microsoft Identity Platform generuje přístupové tokeny v 1.0, když klient požaduje tokeny pro Microsoft Graph. V tokenech přístupu by se měly zobrazit jenom aplikace. Klienti je **nesmí** kontrolovat.

### <a name="error-response-example"></a>Příklad chybové odpovědi

Koncový bod tokenu vrátí chybovou odpověď při pokusu o získání přístupového tokenu pro rozhraní API pro příjem dat, pokud pro podřízené rozhraní API existuje zásada podmíněného přístupu (například Multi-Factor Authentication) nastavená na něm. Služba střední vrstvy by tuto chybu měla obit klientské aplikaci, aby mohla klientská aplikace poskytnout interakci uživatele, aby splnila zásady podmíněného přístupu.

```
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Přístup k zabezpečenému prostředku pomocí přístupového tokenu

Služba střední vrstvy teď může použít token získaný výše k provádění ověřených požadavků webového rozhraní API pro příjem dat, a to nastavením tokenu v hlavičce `Authorization`.

### <a name="example"></a>Příklad:

```
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkSzdNN0RyNXlvUUdLNmFEc19vdDF3cEQyZjNqRkxiNlVrcm9PcXA2cXBJclAxZVV0QktzMHEza29HN3RzXzJpSkYtQjY1UV8zVGgzSnktUHZsMjkxaFNBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMDE2LCJuYmYiOjE0OTM5MzAwMTYsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQUlzQjN5ZUljNkZ1aEhkd1YxckoxS1dlbzJPckZOUUQwN2FENTVjUVRtems9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJzUVlVekYxdUVVS0NQS0dRTVFVRkFBIiwidmVyIjoiMS4wIn0.Hrn__RGi-HMAzYRyCqX3kBGb6OS7z7y49XPVPpwK_7rJ6nik9E4s6PNY4XkIamJYn7tphpmsHdfM9lQ1gqeeFvFGhweIACsNBWhJ9Nx4dvQnGRkqZ17KnF_wf_QLcyOrOWpUxdSD_oPKcPS-Qr5AFkjw0t7GOKLY-Xw3QLJhzeKmYuuOkmMDJDAl0eNDbH0HiCh3g189a176BfyaR0MgK8wrXI_6MTnFSVfBePqklQeLhcr50YTBfWg3Svgl6MuK_g1hOuaO-XpjUxpdv5dZ0SvI47fAuVDdpCE48igCX5VMj4KUVytDIf6T78aIXMkYHGgW3-xAmuSyYH_Fr0yVAQ
```

## <a name="gaining-consent-for-the-middle-tier-application"></a>Získání souhlasu pro aplikaci střední vrstvy

V závislosti na architektuře nebo využití vaší aplikace můžete zvážit různé strategie pro zajištění úspěšnosti OBO toku. Ve všech případech je konečným cílem zajistit, aby klientská aplikace mohla zavolat aplikaci střední vrstvy a aplikace střední vrstvy má oprávnění k volání prostředku back-endu. 

> [!NOTE]
> Dřív účet Microsoft systém (osobní účty) nepodporoval pole známá klientská aplikace, ani by nemohl zobrazit kombinovaný souhlas.  Přidaná a všechny aplikace na platformě Microsoft Identity můžou používat k gettign souhlasu s aplikací známý klient přístup k OBO. 

### <a name="default-and-combined-consent"></a>/.Default a kombinovaný souhlas

Aplikace střední vrstvy přidá klienta do seznamu známých klientských aplikací ve svém manifestu a klient může aktivovat kombinovaný postup souhlasu pro sebe i pro aplikaci střední vrstvy. Na koncovém bodu Microsoft Identity Platform se to dělá pomocí [oboru`/.default`](v2-permissions-and-consent.md#the-default-scope). Při aktivaci obrazovky pro vyjádření souhlasu pomocí známých klientských aplikací a `/.default`se na obrazovce pro vyjádření souhlasu zobrazí oprávnění pro klienta **i** k rozhraní API střední vrstvy. také si vyžádají všechna oprávnění, která jsou vyžadována rozhraním API střední vrstvy. Uživatel poskytne souhlas obou aplikací a OBO tok funguje.

### <a name="pre-authorized-applications"></a>Předem autorizované aplikace

Prostředky mohou indikovat, že daná aplikace má vždy oprávnění přijímat určité obory. To je primárně užitečné pro zajištění bezproblémového propojení mezi front-end klientem a prostředkem back-endu. Prostředek může deklarovat několik předem autorizovaných aplikací – každá taková aplikace může požadovat tato oprávnění v OBO toku a přijímat je bez souhlasu uživatele.

### <a name="admin-consent"></a>Souhlas správce

Správce tenanta může zaručit, že aplikace mají oprávnění volat požadovaná rozhraní API poskytnutím souhlasu správce pro aplikaci střední vrstvy. Pokud to chcete udělat, správce může ve svém tenantovi najít aplikaci střední vrstvy, otevřít stránku požadovaná oprávnění a rozhodnout, že aplikaci udělíte oprávnění. Další informace o souhlasu správce najdete v [dokumentaci k souhlasu a oprávnění](v2-permissions-and-consent.md).

### <a name="use-of-a-single-application"></a>Použití jedné aplikace

V některých scénářích můžete mít jenom jednu dvojici klienta střední vrstvy a klienta front-end. V tomto scénáři může být jednodušší vytvořit tuto jednu aplikaci, která bude mít na starosti nutnost použití střední vrstvy zcela. K ověření mezi front-end a webovým rozhraním API můžete použít soubory cookie, id_token nebo přístupový token, který je požadován pro samotnou aplikaci. Pak žádost o souhlas z této jediné aplikace na prostředek back-endu.

## <a name="client-limitations"></a>Omezení klienta

Pokud klient používá implicitní tok k získání id_token a tento klient má také zástupné znaky v adrese URL odpovědi, id_token nelze použít pro OBO tok.  Přístup k tokenům získaným pomocí implicitního toku udělení se ale dá dál uplatnit u důvěrného klienta, i když má zahajovací klient adresu URL odpovědi na zástupný znak.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o protokolu OAuth 2,0 a další způsob, jak provádět ověřování pomocí pověření klienta prostřednictvím služby.

* [Udělení přihlašovacích údajů klienta OAuth 2,0 v platformě Microsoft Identity Platform](v2-oauth2-client-creds-grant-flow.md)
* [Tok kódu OAuth 2,0 na platformě Microsoft Identity Platform](v2-oauth2-auth-code-flow.md)
* [Použití oboru `/.default`](v2-permissions-and-consent.md#the-default-scope)

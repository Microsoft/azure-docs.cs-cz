---
title: Platforma identit y Microsoft a OAuth2.0 On-Behalf-Of flow | Azure
description: Tento článek popisuje použití zpráv HTTP k implementaci služby pro ověřování služeb pomocí toku OAuth2.0 On-Behalf-Of.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: a689e0f588046296591069801d621f3608a372e5
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886258"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Platforma identit y Microsoft a OAuth 2.0 On-Behalf-Of flow


OAuth 2.0 On-Behalf-Of flow (OBO) slouží případ použití, kdy aplikace vyvolá službu / webové rozhraní API, které zase potřebuje volat jinou službu / webové rozhraní API. Cílem je šířit delegovanou identitu uživatele a oprávnění prostřednictvím řetězce požadavků. Aby služba střední vrstvy mohla provádět ověřené požadavky na službu navazujícím vysílání, musí jménem uživatele zabezpečit přístupový token z platformy identit microsoftu.

Tento článek popisuje, jak programovat přímo proti protokolu ve vaší aplikaci.  Pokud je to možné, doporučujeme místo toho použít podporované knihovny ověřování společnosti Microsoft (MSAL) k [získání tokenů a volání zabezpečených webových rozhraní API](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Také se podívejte na [ukázkové aplikace, které používají MSAL](sample-v2-code.md).

> [!NOTE]
>
> - Koncový bod platformy identit microsoftu nepodporuje všechny scénáře a funkce. Chcete-li zjistit, zda byste měli používat koncový bod platformy identit společnosti Microsoft, přečtěte si o [omezení platformy identit společnosti Microsoft](active-directory-v2-limitations.md). 
> - Od května 2018 některé implicitní `id_token` toky odvozené nelze použít pro tok OBO. Jednostránkové aplikace (SPA) by měl předat **přístupový** token pro střední vrstvy důvěrné klienta provádět toky OBO místo. Další informace o tom, kteří klienti mohou provádět volání OBO, naleznete v [tématu omezení](#client-limitations).

## <a name="protocol-diagram"></a>Protokolový diagram

Předpokládejme, že uživatel byl ověřen v aplikaci pomocí [toku udělení autorizačního kódu OAuth 2.0](v2-oauth2-auth-code-flow.md) nebo jiného toku přihlášení. V tomto okamžiku aplikace má přístupový token *pro rozhraní API A* (token A) s deklaracemi identity uživatele a souhlas s přístupem k webovému rozhraní API střední vrstvy (API A). Nyní rozhraní API a musí provést ověřený požadavek na příjem webového rozhraní API (API B).

Následující kroky představují tok OBO a jsou vysvětleny pomocí následujícího diagramu.

![Zobrazuje tok OAuth2.0 On-Behalf-Of](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. Klientská aplikace provede požadavek na rozhraní API `aud` A s tokenem A (s deklarací API A).
1. Rozhraní API A se ověřuje na koncovém bodě vystavování tokenů platformy identit microsoftu a požaduje token pro přístup k rozhraní API B.
1. Koncový bod pro vystavení tokenu platformy identity společnosti Microsoft ověřuje pověření rozhraní API A spolu s tokenem A a vydává přístupový token pro rozhraní API B (token B) do rozhraní API A.
1. Token B je nastaven rozhraním API A v hlavičce autorizace požadavku na rozhraní API B.
1. Data ze zabezpečeného prostředku je vrácena rozhraním API B do rozhraní API A a odtud klientovi.

> [!NOTE]
> V tomto scénáři služba střední vrstvy nemá žádnou interakci s uživatelem k získání souhlasu uživatele s přístupem k rozhraní API pro příjem dat. Proto možnost udělit přístup k rozhraní API pro příjem dat je prezentována předem jako součást kroku souhlasu během ověřování. Informace o tom, jak tuto aplikaci nastavit, najdete v [tématu Získání souhlasu pro aplikaci střední vrstvy](#gaining-consent-for-the-middle-tier-application).

## <a name="service-to-service-access-token-request"></a>Požadavek na přístupový token služby

Chcete-li požádat o přístupový token, vytvořte HTTP POST do koncového bodu tokenu platformy identity Microsoftu specifického pro klienta s následujícími parametry.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Existují dva případy v závislosti na tom, zda se klientská aplikace rozhodne pro zabezpečené sdíleným tajným kódem nebo certifikátem.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>První případ: Žádost o přístupový token se sdíleným tajným tajemstvím

Při použití sdíleného tajného klíče obsahuje požadavek přístupového tokenu služby ke službě následující parametry:

| Parametr |  | Popis |
| --- | --- | --- |
| `grant_type` | Požaduje se | Typ požadavku tokenu. Pro požadavek pomocí JWT musí `urn:ietf:params:oauth:grant-type:jwt-bearer`být hodnota . |
| `client_id` | Požaduje se | ID aplikace (klienta), které vaší aplikaci přiřadila stránka Registrace aplikací na webu Azure – [registrace aplikací.](https://go.microsoft.com/fwlink/?linkid=2083908) |
| `client_secret` | Požaduje se | Tajný klíč klienta, který jste vygenerovali pro vaši aplikaci na stránce Registrace aplikací – k registracím aplikací. |
| `assertion` | Požaduje se | Hodnota tokenu použitého v požadavku.  Tento token musí mít okruh uživatelů aplikace, která tento požadavek `client-id` obo tento požadavek (aplikace označená polem). |
| `scope` | Požaduje se | Mezera oddělený seznam oborů pro požadavek tokenu. Další informace naleznete v [tématu obory](v2-permissions-and-consent.md). |
| `requested_token_use` | Požaduje se | Určuje způsob zpracování požadavku. V toku OBO musí být `on_behalf_of`hodnota nastavena na . |

#### <a name="example"></a>Příklad

Následující funkce HTTP POST požaduje přístupový `user.read` token a https://graph.microsoft.com obnovovací token s oborem pro webové rozhraní API.

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

### <a name="second-case-access-token-request-with-a-certificate"></a>Druhý případ: Žádost o přístupový token s certifikátem

Požadavek na přístupový token služby k službě s certifikátem obsahuje následující parametry:

| Parametr |  | Popis |
| --- | --- | --- |
| `grant_type` | Požaduje se | Typ požadavku tokenu. Pro požadavek pomocí JWT musí `urn:ietf:params:oauth:grant-type:jwt-bearer`být hodnota . |
| `client_id` | Požaduje se |  ID aplikace (klienta), které vaší aplikaci přiřadila stránka Registrace aplikací na webu Azure – [registrace aplikací.](https://go.microsoft.com/fwlink/?linkid=2083908) |
| `client_assertion_type` | Požaduje se | Hodnota musí `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`být . |
| `client_assertion` | Požaduje se | Kontrolní výraz (webový token JSON), který potřebujete vytvořit a podepsat pomocí certifikátu, který jste zaregistrovali jako pověření pro vaši aplikaci. Informace o registraci certifikátu a formátu kontrolního výrazu naleznete v [tématu pověření certifikátu](active-directory-certificate-credentials.md). |
| `assertion` | Požaduje se | Hodnota tokenu použitého v požadavku. |
| `requested_token_use` | Požaduje se | Určuje způsob zpracování požadavku. V toku OBO musí být `on_behalf_of`hodnota nastavena na . |
| `scope` | Požaduje se | Mezery oddělený seznam oborů pro požadavek tokenu. Další informace naleznete v [tématu obory](v2-permissions-and-consent.md).|

Všimněte si, že parametry jsou téměř stejné jako v případě `client_secret` požadavku sdíleným tajným tajemstvím s tím rozdílem, že parametr je nahrazen dvěma parametry: a `client_assertion_type` a `client_assertion`.

#### <a name="example"></a>Příklad

Následující funkce HTTP POST požaduje `user.read` přístupový https://graph.microsoft.com token s oborem pro webové rozhraní API s certifikátem.

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

## <a name="service-to-service-access-token-response"></a>Odpověď tokenu přístupu služby

Úspěšnou odpovědí je odpověď JSON OAuth 2.0 s následujícími parametry.

| Parametr | Popis |
| --- | --- |
| `token_type` | Označuje hodnotu typu tokenu. Jediný typ, který podporuje platforma identit společnosti Microsoft `Bearer`je . Další informace o žetonech nosičů naleznete v [rámci autorizace OAuth 2.0: Použití žetonu nosiče (RFC 6750).](https://www.rfc-editor.org/rfc/rfc6750.txt) |
| `scope` | Rozsah přístupu uděleného v tokenu. |
| `expires_in` | Doba v sekundách, po kterou je platný přístupový token. |
| `access_token` | Požadovaný přístupový token. Volající služba můžete použít tento token k ověření přijímající služby. |
| `refresh_token` | Obnovovací token pro požadovaný přístupový token. Volající služba můžete použít tento token požádat o jiný přístupový token po vypršení platnosti aktuálního přístupového tokenu. Obnovovací token je k `offline_access` dispozici pouze v případě, že byl požadován obor. |

### <a name="success-response-example"></a>Příklad odpovědi na úspěch

Následující příklad ukazuje odpověď na úspěch na žádost https://graph.microsoft.com o přístupový token pro webové rozhraní API.

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
> Výše uvedený přístupový token je token ve formátu v1.0. Důvodem je, že token je k dispozici na základě **prostředku,** ke zdroji, ke zdroji, ke který se přistupuje. Microsoft Graph je nastaven a přijímá tokeny v1.0, takže platforma identit microsoftu vytváří přístupové tokeny v1.0, když klient požaduje tokeny pro Microsoft Graph. Pouze aplikace by měly hledat přístupové tokeny. Klienti je **nesmí** kontrolovat.

### <a name="error-response-example"></a>Příklad odpovědi na chybu

Odpověď na chybu je vrácena koncovým bodem tokenu při pokusu o získání přístupového tokenu pro rozhraní API pro příjem dat, pokud má rozhraní API pro příjem dat zásady podmíněného přístupu (například vícefaktorové ověřování) nastavené na něm. Služba střední vrstvy by měla tuto chybu předložit klientské aplikaci, aby klientská aplikace mohla poskytnout interakci s uživatelem, aby splňovala zásady podmíněného přístupu.

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

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Použití přístupového tokenu pro přístup k zabezpečenému prostředku

Nyní služba střední vrstvy můžete použít výše získaný token k vytváření ověřených požadavků na rozhraní `Authorization` API pro příjem sítě, nastavením tokenu v záhlaví.

### <a name="example"></a>Příklad

```
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkSzdNN0RyNXlvUUdLNmFEc19vdDF3cEQyZjNqRkxiNlVrcm9PcXA2cXBJclAxZVV0QktzMHEza29HN3RzXzJpSkYtQjY1UV8zVGgzSnktUHZsMjkxaFNBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMDE2LCJuYmYiOjE0OTM5MzAwMTYsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQUlzQjN5ZUljNkZ1aEhkd1YxckoxS1dlbzJPckZOUUQwN2FENTVjUVRtems9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJzUVlVekYxdUVVS0NQS0dRTVFVRkFBIiwidmVyIjoiMS4wIn0.Hrn__RGi-HMAzYRyCqX3kBGb6OS7z7y49XPVPpwK_7rJ6nik9E4s6PNY4XkIamJYn7tphpmsHdfM9lQ1gqeeFvFGhweIACsNBWhJ9Nx4dvQnGRkqZ17KnF_wf_QLcyOrOWpUxdSD_oPKcPS-Qr5AFkjw0t7GOKLY-Xw3QLJhzeKmYuuOkmMDJDAl0eNDbH0HiCh3g189a176BfyaR0MgK8wrXI_6MTnFSVfBePqklQeLhcr50YTBfWg3Svgl6MuK_g1hOuaO-XpjUxpdv5dZ0SvI47fAuVDdpCE48igCX5VMj4KUVytDIf6T78aIXMkYHGgW3-xAmuSyYH_Fr0yVAQ
```

## <a name="gaining-consent-for-the-middle-tier-application"></a>Získání souhlasu pro aplikaci střední vrstvy

V závislosti na architektuře nebo použití vaší aplikace můžete zvážit různé strategie pro zajištění, že tok Objektu Pro bo je úspěšný. Ve všech případech je konečným cílem zajistit, aby byl udělen správný souhlas, aby klientská aplikace mohla volat aplikaci střední vrstvy a aplikace střední vrstvy má oprávnění k volání prostředku back-endu. 

> [!NOTE]
> Dříve systém účtů Microsoft (osobní účty) nepodporoval pole "Známá klientská aplikace" ani nemohl zobrazit kombinovaný souhlas.  To bylo přidáno a všechny aplikace v platformě identit microsoftu můžete použít známý přístup klientské aplikace pro gettign souhlas pro volání OBO. 

### <a name="default-and-combined-consent"></a>/.výchozí a kombinovaný souhlas

Aplikace střední vrstvy přidá klienta do seznamu známých klientských aplikací v jeho manifestu a pak klient může aktivovat kombinovaný tok souhlasu pro sebe i pro aplikaci střední vrstvy. V koncovém bodě platformy identity společnosti Microsoft se to provádí pomocí [ `/.default` oboru](v2-permissions-and-consent.md#the-default-scope). Při aktivaci obrazovky souhlasu pomocí `/.default`známých klientských aplikací a , na obrazovce souhlas zobrazí oprávnění **pro** klienta k rozhraní API střední vrstvy a také požádat o oprávnění vyžadovaná rozhraní API střední vrstvy. Uživatel poskytuje souhlas pro obě aplikace, a pak tok OBO funguje.

### <a name="pre-authorized-applications"></a>Předautorizované aplikace

Prostředky mohou znamenat, že daná aplikace má vždy oprávnění přijímat určité obory. To je užitečné především k bezproblémovosti připojení mezi front-endovým klientem a back-endovým prostředkem. Prostředek může deklarovat více předautorizovaných aplikací – každá taková aplikace může požadovat tato oprávnění v toku OBO a přijímat je bez souhlasu uživatele.

### <a name="admin-consent"></a>Souhlas správce

Správce klienta může zaručit, že aplikace mají oprávnění k volání jejich požadovaných rozhraní API poskytnutím souhlasu správce pro aplikaci střední vrstvy. Chcete-li to provést, správce může najít aplikaci střední vrstvy ve svém tenantovi, otevřete stránku požadovaných oprávnění a zvolte udělit oprávnění pro aplikaci. Další informace o souhlasu správce naleznete v dokumentaci k [souhlasu a oprávněním](v2-permissions-and-consent.md).

### <a name="use-of-a-single-application"></a>Použití jedné aplikace

V některých případech můžete mít pouze jedno párování středního a front-endového klienta. V tomto scénáři může být jednodušší, aby to jednu aplikaci, negace potřebu aplikace střední vrstvy úplně. Chcete-li ověřit mezi front-endem a webovým rozhraním API, můžete použít soubory cookie, id_token nebo přístupový token požadovaný pro samotnou aplikaci. Potom požádejte o souhlas této jediné aplikace s back-endovým prostředkem.

## <a name="client-limitations"></a>Omezení klientů

Pokud klient používá implicitní tok získat id_token a tento klient má také zástupné znaky v adrese URL odpovědi, id_token nelze použít pro tok OBO.  Přístupové tokeny získané prostřednictvím implicitního toku grantu však mohou být stále uplatněny důvěrným klientem, i když má zahajující klient registrovanou adresu URL odpovědi se zástupnými symboly.

## <a name="next-steps"></a>Další kroky

Další informace o protokolu OAuth 2.0 a další způsob, jak provádět služby služby ověřování pomocí pověření klienta.

* [Udělení pověření klienta OAuth 2.0 v platformě identit Microsoftu](v2-oauth2-client-creds-grant-flow.md)
* [Tok kódu OAuth 2.0 v platformě identit Microsoftu](v2-oauth2-auth-code-flow.md)
* [Použití `/.default` oboru](v2-permissions-and-consent.md#the-default-scope)

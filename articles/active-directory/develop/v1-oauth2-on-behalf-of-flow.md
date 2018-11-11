---
title: Služba Služba ověřování Azure Active Directory, který používá OAuth 2.0 On-Behalf-Of koncept specifikace | Dokumentace Microsoftu
description: Tento článek popisuje, jak používat zprávy HTTP k implementaci ověřování služba služba pomocí OAuth 2.0 On-Behalf-Of toku.
services: active-directory
documentationcenter: .net
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: celested
ms.reviewer: hirsin, nacanuma
ms.custom: aaddev
ms.openlocfilehash: ab9f2638de6f74944eb27f024be3000209554cdf
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51345128"
---
# <a name="service-to-service-calls-that-use-delegated-user-identity-in-the-on-behalf-of-flow"></a>Služba služba volá tuto identitu uživatele použijte delegovaný v tok On-Behalf-Of

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

Tok OAuth 2.0 On-Behalf-Of (OBO) umožňuje aplikaci, která vyvolává službu nebo webové rozhraní API k předání ověřování uživatelů do jiné služby nebo webové rozhraní API. Tok OBO rozšíří delegovaný uživatel identit a oprávnění pomocí řetězce požadavku. Pro služby střední vrstvy, aby ověřených požadavků pro příjem dat služby musí zajistit přístupového tokenu z Azure Active Directory (Azure AD) jménem uživatele.

> [!IMPORTANT]
> Od května 2018 je `id_token` nelze použít pro tok On-Behalf-Of.  Jednostránkové aplikace (SPA), musíte předat přístupový token k důvěrnému klientovi střední vrstvy proveďte OBO toky. Další podrobnosti o klientech, které můžete provádět volání On-Behalf-Of najdete [omezení](#client-limitations).

## <a name="on-behalf-of-flow-diagram"></a>On-Behalf-Of vývojový diagram

OBO tok spustí po ověření uživatele v aplikaci, která se používá [toku udělení autorizačního kódu OAuth 2.0](v1-protocols-oauth-code.md). V tomto okamžiku aplikace odesílá do střední vrstvy webového rozhraní API (rozhraní API A) obsahující deklarace identity uživatele a vyjádření souhlasu pro přístup k rozhraní API a přístupový token (token A) Rozhraní API A Další, zašle ověřený požadavek na podřízené webové rozhraní API (API B).

Tyto kroky tvoří tok On-Behalf-Of: ![On-Behalf-Of tok OAuth 2.0](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)

1. Klientská aplikace odešle požadavek rozhraní API A s tokenem A.
1. Rozhraní API A ověřuje u konkrétního koncového bodu vystavení tokenu Azure AD a žádá token pro přístup k rozhraní API služby serveru B.
1. Koncový bod vystavení tokenu Azure AD ověří přihlašovací údaje A rozhraní API pomocí tokenu A a vydá přístupový token pro rozhraní API B (token B).
1. Požadavek na rozhraní API B obsahuje B tokenu v autorizační hlavičce.
1. B rozhraní API vrátí data z zabezpečeným prostředkům.

>[!NOTE]
>Cílovou skupinu deklarací identity v tokenu přístupu použije k vyžádání tokenu pro podřízené služby musí být ID služby, který zadal žádost OBO. Token také musí být podepsány pomocí Azure Active Directory globální podpisový klíč (což je výchozí hodnota pro aplikace registrované přes **registrace aplikací** na portálu).

## <a name="register-the-application-and-service-in-azure-ad"></a>Registrace aplikace a služby v Azure AD

Registrace služby střední vrstvy a klientskou aplikaci v Azure AD.

### <a name="register-the-middle-tier-service"></a>Registrace služby střední vrstvy

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na horním panelu, vyberte svůj účet a podívejte se do části **Directory** seznam a vyberte tenanta služby Active Directory pro vaši aplikaci.
1. Vyberte **další služby** v levém podokně a zvolte **Azure Active Directory**.
1. Vyberte **registrace aplikací** a potom **registrace nové aplikace**.
1. Zadejte popisný název pro aplikaci a vyberte typ aplikace.
    1. Adresa URL přihlašování nebo adresa URL pro přesměrování v závislosti na typu aplikace, nastavte základní adresu URL.
    1. Vyberte **vytvořit** k vytvoření aplikace.
1. Tajný kód klienta generovat před ukončením na webu Azure portal.
    1. Na webu Azure Portal, vyberte aplikaci a vyberte **nastavení**.
    1. Vyberte **klíče** v nabídce nastavení a přidejte klíč s délkou klíče buď jeden nebo dva roky.
    1. Při ukládání této stránky na webu Azure portal zobrazí hodnotu klíče. Zkopírujte a uložte hodnotu klíče na bezpečném místě.

    > [!IMPORTANT]
    > Budete potřebovat klíč, který chcete nakonfigurovat nastavení aplikace ve vaší implementaci. Hodnota tohoto klíče se nezobrazí a není retrievable jiným způsobem. Poznamenejte si ho hned, jak je viditelný na webu Azure Portal.

### <a name="register-the-client-application"></a>Registrace aplikace klienta

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na horním panelu, vyberte svůj účet a podívejte se do části **Directory** seznam a vyberte tenanta služby Active Directory pro vaši aplikaci.
1. Vyberte **další služby** v levém podokně a zvolte **Azure Active Directory**.
1. Vyberte **registrace aplikací** a potom **registrace nové aplikace**.
1. Zadejte popisný název pro aplikaci a vyberte typ aplikace.
   1. Adresa URL přihlašování nebo adresa URL pro přesměrování v závislosti na typu aplikace, nastavte základní adresu URL.
   1. Vyberte **vytvořit** k vytvoření aplikace.
1. Konfigurace oprávnění pro vaši aplikaci.
   1. V nabídce nastavení, zvolte **požadovaná oprávnění** a potom vyberte **přidat** a **vyberte rozhraní API**.
   1. Do textového pole zadejte název služby střední vrstvy.
   1. Zvolte **oprávnění Select** a pak vyberte **název přístupu služby**.

### <a name="configure-known-client-applications"></a>Konfigurace známé klientské aplikace

V tomto scénáři je potřeba získat souhlas uživatele pro přístup k rozhraní API pro příjem dat bez zásahu uživatele služby střední vrstvy. Možnost udělit přístup k rozhraní API pro příjem dat musí být předloženy nahoru přední jako součást kroku souhlasu během ověřování.

Postupujte podle následujících kroků, abyste explicitně vytvořit vazbu registrace klientskou aplikaci ve službě Azure AD s registrací služby střední vrstvy. Tato operace sloučení souhlas potřebný klienta a střední vrstvy do jednoho dialogového okna.

1. Přejít na registrace služby střední vrstvy a vyberte **Manifest** otevřete editor manifestu.
1. Vyhledejte `knownClientApplications` pole vlastností a přidejte klientské ID klientské aplikace jako element.
1. Uložení manifestu vyberte **Uložit**.

## <a name="service-to-service-access-token-request"></a>Žádost o Service to service přístupový token

Požádat o přístupový token, ujistěte se, HTTP POST do konkrétního klienta koncového bodu Azure AD s následujícími parametry:

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```

Klientská aplikace je zabezpečená, sdílený tajný klíč nebo certifikát.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>První věc: žádost o přístupový token s sdílený tajný klíč

Pokud používáte sdílený tajný klíč, žádosti o token přístupu service to service obsahuje následující parametry:

| Parametr |  | Popis |
| --- | --- | --- |
| Parametr grant_type |povinné | Typ požadavku na token. Požadavek OBO používá JSON Web Token (JWT), takže hodnota musí být **urn: ietf:params:oauth:grant-typu: jwt-nosiče**. |
| kontrolní výraz |povinné | Hodnota přístupový token, který je použitý v požadavku. |
| client_id |povinné | ID aplikace přiřazené k volání služby během registrace ve službě Azure AD. Pokud chcete najít ID aplikace na webu Azure Portal, vyberte **služby Active Directory**, vyberte adresář a potom vyberte název aplikace. |
| Hodnota client_secret |povinné | Klíč zaregistrovaný pro volání služby ve službě Azure AD. Tato hodnota by bylo zaznamenáno v době registrace. |
| prostředek |povinné | Identifikátor URI ID aplikace přijímající služby (zabezpečeným prostředkům). Chcete-li najít identifikátor URI ID aplikace na webu Azure Portal, vyberte **služby Active Directory** a vyberte adresář. Vyberte název aplikace, zvolte **všechna nastavení**a pak vyberte **vlastnosti**. |
| requested_token_use |povinné | Určuje, jak by měl být požadavek zpracovat. Tok On-Behalf-Of, musí být hodnota **on_behalf_of**. |
| scope |povinné | Mezerou oddělený seznam oborů pro žádosti o token. Pro OpenID Connect, oboru **openid** musí být zadán.|

#### <a name="example"></a>Příklad:

Vyžádá přístupový token pro následující HTTP POST https://graph.windows.net webové rozhraní API. `client_id` Se identifikuje služba, která požaduje přístupový token.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_secret=0Y1W%2BY3yYb3d9N8vSjvm8WrGzVZaAaHbHHcGbcgG%2BoI%3D
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Druhý případ: žádost o přístupový token s certifikátem

Žádosti o token service to service přístup pomocí certifikátu obsahuje následující parametry:

| Parametr |  | Popis |
| --- | --- | --- |
| Parametr grant_type |povinné | Typ požadavku na token. Požadavek OBO používá přístupový token JWT, takže hodnota musí být **urn: ietf:params:oauth:grant-typu: jwt-nosiče**. |
| kontrolní výraz |povinné | Hodnota tokenu používaného v požadavku. |
| client_id |povinné | ID aplikace přiřazené k volání služby během registrace ve službě Azure AD. Pokud chcete najít ID aplikace na webu Azure Portal, vyberte **služby Active Directory**, vyberte adresář a potom vyberte název aplikace. |
| client_assertion_type |povinné |Hodnota musí být `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |povinné | Webového tokenu JSON, které vytvoříte a podepsat pomocí certifikátu můžete zaregistrovat jako přihlašovací údaje pro vaši aplikaci. Zobrazit [certifikát přihlašovacích údajů](active-directory-certificate-credentials.md) Další informace o formátu kontrolní výraz a o tom, jak zaregistrovat certifikát.|
| prostředek |povinné | Identifikátor URI ID aplikace přijímající služby (zabezpečeným prostředkům). Chcete-li najít identifikátor URI ID aplikace na webu Azure Portal, vyberte **služby Active Directory** a vyberte adresář. Vyberte název aplikace, zvolte **všechna nastavení**a pak vyberte **vlastnosti**. |
| requested_token_use |povinné | Určuje, jak by měl být požadavek zpracovat. Tok On-Behalf-Of, musí být hodnota **on_behalf_of**. |
| scope |povinné | Mezerou oddělený seznam oborů pro žádosti o token. Pro OpenID Connect, oboru **openid** musí být zadán.|

Tyto parametry jsou téměř stejné jako u žádosti sdílený tajný klíč, s výjimkou, že `client_secret parameter` nahrazuje dva parametry: `client_assertion_type` a `client_assertion`.

#### <a name="example"></a>Příklad:

Vyžádá přístupový token pro následující HTTP POST https://graph.windows.net webové rozhraní API s certifikátem. `client_id` Se identifikuje služba, která požaduje přístupový token.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

## <a name="service-to-service-access-token-response"></a>Služba služba přístupu k odpovědi tokenu

Úspěšná odpověď je odpověď JSON OAuth 2.0 s následujícími parametry:

| Parametr | Popis |
| --- | --- |
| token_type |Určuje hodnotu pro typ tokenu. Jediný typ, který podporuje Azure AD je **nosiče**. Další informace o nosné tokeny, najdete v článku [Framework autorizace OAuth 2.0: použití nosného tokenu (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Obor přístupu v tokenu. |
| expires_in |Dlouhá doba přístupový token je platný (v sekundách). |
| expires_on |Čas, kdy vyprší platnost přístupového tokenu. Datum je vyjádřena jako počet sekund od 1970-01-01T0:0:0Z UTC až do okamžiku vypršení platnosti. Tato hodnota se používá k určení doby života tokenů v mezipaměti. |
| prostředek |Identifikátor URI ID aplikace přijímající služby (zabezpečeným prostředkům). |
| access_token |Požadovaný přístupový token. Volání služby můžete použít tento token k ověření přijímající služby. |
| id_token |Požadovaný token ID. Volání služby můžete použít tento token pro ověření identity uživatele a zahájit relaci s uživatelem. |
| refresh_token |Obnovovací token pro požadované přístupový token. Volání služby můžete tento token po vypršení platnosti přístupového tokenu aktuální vyžádat dalšího přístupového tokenu. |

### <a name="success-response-example"></a>Příklad odpovědi úspěch

Následující příklad ukazuje úspěšná odpověď na žádost o přístupový token pro https://graph.windows.net webové rozhraní API.

```
{
    "token_type":"Bearer",
    "scope":"User.Read",
    "expires_in":"43482",
    "ext_expires_in":"302683",
    "expires_on":"1493466951",
    "not_before":"1493423168",
    "resource":"https://graph.windows.net",
    "access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ",
    "refresh_token":"AQABAAAAAABnfiG-mA6NTae7CdWW7QfdjKGu9-t1scy_TDEmLi4eLQMjJGt_nAoVu6A4oSu1KsRiz8XyQIPKQxSGfbf2FoSK-hm2K8TYzbJuswYusQpJaHUQnSqEvdaCeFuqXHBv84wjFhuanzF9dQZB_Ng5za9xKlUENrNtlq9XuLNVKzxEyeUM7JyxzdY7JiEphWImwgOYf6II316d0Z6-H3oYsFezf4Xsjz-MOBYEov0P64UaB5nJMvDyApV-NWpgklLASfNoSPGb67Bc02aFRZrm4kLk-xTl6eKE6hSo0XU2z2t70stFJDxvNQobnvNHrAmBaHWPAcC3FGwFnBOojpZB2tzG1gLEbmdROVDp8kHEYAwnRK947Py12fJNKExUdN0njmXrKxNZ_fEM33LHW1Tf4kMX_GvNmbWHtBnIyG0w5emb-b54ef5AwV5_tGUeivTCCysgucEc-S7G8Cz0xNJ_BOiM_4bAv9iFmrm9STkltpz0-Tftg8WKmaJiC0xXj6uTf4ZkX79mJJIuuM7XP4ARIcLpkktyg2Iym9jcZqymRkGH2Rm9sxBwC4eeZXM7M5a7TJ-5CqOdfuE3sBPq40RdEWMFLcrAzFvP0VDR8NKHIrPR1AcUruat9DETmTNJukdlJN3O41nWdZOVoJM-uKN3uz2wQ2Ld1z0Mb9_6YfMox9KTJNzRzcL52r4V_y3kB6ekaOZ9wQ3HxGBQ4zFt-2U0mSszIAA",
    "id_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC8yNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IvIiwiaWF0IjoxNDkzNDIzMTY4LCJuYmYiOjE0OTM0MjMxNjgsImV4cCI6MTQ5MzQ2Njk1MSwiYW1yIjpbInB3ZCJdLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXRpIjoieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsInZlciI6IjEuMCJ9."
}
```

### <a name="error-response-example"></a>Ukázka chyby odpovědi

Koncový bod tokenu Azure AD vrací reakce na chybu při pokusu o získání přístupového tokenu pro příjem dat rozhraní API, která je nastavená pomocí zásad podmíněného přístupu (například ověřování službou Multi-Factor Authentication). Střední vrstvy služby by měl plochu tuto chybu do klientské aplikace tak, aby klientská aplikace může poskytnout interakce uživatele splňovat zásady podmíněného přístupu.

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

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Použití přístupového tokenu pro přístup k zabezpečeným prostředkům

Služby střední vrstvy můžete použít pořízené přístupový token aby ověřených požadavků pro podřízené webové rozhraní API tak, že nastavíte tokenu `Authorization` záhlaví.

### <a name="example"></a>Příklad:

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>Kontrolní výrazy SAML získali díky tok, který OBO OAuth 2.0

Některé OAuth webových služeb využívajících potřebují přístup k jiné služby webového rozhraní API, která přijímají kontrolní výrazy SAML v neinteraktivním přístupu toky. Azure Active Directory může poskytovat kontrolní výraz SAML v reakci tok On-Behalf-Of, který používá založené na SAML webové služby jako cílový prostředek.

>[!NOTE]
>Toto je nestandardní rozšíření OAuth 2.0 On-Behalf-Of toku, který umožňuje aplikaci na základě OAuth2 pro přístup k rozhraní API koncových bodů webové služby, které používají tokeny SAML.

> [!TIP]
> Při volání SAML chráněné webové služby z front-endové webové aplikace, volání rozhraní API a zahájení toku běžné interaktivní ověřování s existující relace uživatele. Stačí použít tok, který OBO při volání služba služba vyžaduje token SAML poskytnout uživatelský kontext.

### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>Získat SAML token s použitím požadavek OBO se sdílený tajný klíč

Žádost o služby služby pro kontrolní výraz SAML obsahuje následující parametry:

| Parametr |  | Popis |
| --- | --- | --- |
| Parametr grant_type |povinné | Typ požadavku na token. Pro žádosti, která používá token JWT, hodnota musí být **urn: ietf:params:oauth:grant-typu: jwt-nosiče**. |
| kontrolní výraz |povinné | Hodnota přístupový token, který je použitý v požadavku.|
| client_id |povinné | ID aplikace přiřazené k volání služby během registrace ve službě Azure AD. Pokud chcete najít ID aplikace na webu Azure Portal, vyberte **služby Active Directory**, vyberte adresář a potom vyberte název aplikace. |
| Hodnota client_secret |povinné | Klíč zaregistrovaný pro volání služby ve službě Azure AD. Tato hodnota by bylo zaznamenáno v době registrace. |
| prostředek |povinné | Identifikátor URI ID aplikace přijímající služby (zabezpečeným prostředkům). Toto je prostředek, který bude cílová skupina tokenu SAML. Chcete-li najít identifikátor URI ID aplikace na webu Azure Portal, vyberte **služby Active Directory** a vyberte adresář. Vyberte název aplikace, zvolte **všechna nastavení**a pak vyberte **vlastnosti**. |
| requested_token_use |povinné | Určuje, jak by měl být požadavek zpracovat. Tok On-Behalf-Of, musí být hodnota **on_behalf_of**. |
| requested_token_type | povinné | Určuje typ tokenu požadováno. Hodnota může být **urn: ietf:params:oauth:token-typu: saml2** nebo **urn: ietf:params:oauth:token-typu: saml1** v závislosti na požadavcích využívaných prostředků. |

Odpověď obsahuje token SAML kódovaný v UTF8 a Base64url.

- **SubjectConfirmationData pro kontrolní výraz SAML zdrojem je volání rozhraní OBO**: Pokud je cílová aplikace vyžaduje hodnotu příjemce v **SubjectConfirmationData**, musí být hodnota adresy URL odpovědi bez zástupných znaků v Konfigurace prostředků aplikace.
- **Uzel SubjectConfirmationData**: uzel nemůže obsahovat **InResponseTo** atribut, protože není součástí odpověď SAML. Aplikace přijímá SAML token musí být schopná přijmout kontrolní výraz SAML bez **InResponseTo** atribut.

- **Souhlas**: souhlas musí být udělena pro získání tokenu SAML obsahující uživatelská data na tok, který OAuth. Informace o oprávněních a získávání souhlas správce, naleznete v tématu [oprávnění a souhlas v koncovém bodu Azure Active Directory verze 1.0](https://docs.microsoft.com/azure/active-directory/develop/v1-permissions-and-consent).

### <a name="response-with-saml-assertion"></a>Odpověď se kontrolní výraz SAML

| Parametr | Popis |
| --- | --- |
| token_type |Určuje hodnotu pro typ tokenu. Jediný typ, který podporuje Azure AD je **nosiče**. Další informace o nosných tokenů najdete v tématu [Framework autorizace OAuth 2.0: použití nosného tokenu (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Obor přístupu v tokenu. |
| expires_in |Dlouhá doba přístupový token je platný (v sekundách). |
| expires_on |Čas, kdy vyprší platnost přístupového tokenu. Datum je vyjádřena jako počet sekund od 1970-01-01T0:0:0Z UTC až do okamžiku vypršení platnosti. Tato hodnota se používá k určení doby života tokenů v mezipaměti. |
| prostředek |Identifikátor URI ID aplikace přijímající služby (zabezpečeným prostředkům). |
| access_token |Parametr, který vrátí kontrolního výrazu SAML. |
| refresh_token |Token obnovení. Volání služby můžete tento token vyžádat dalšího přístupového tokenu po vypršení platnosti aktuální kontrolní výraz SAML. |

- token_type: nosiče
- expires_in: 3296
- ext_expires_in: 0
- expires_on: 1529627844
- prostředek: `https://api.contoso.com`
- access_token: \<kontrolní výraz SAML\>
- issued_token_type: urn: ietf:params:oauth:token-typu: saml2
- refresh_token: \<obnovovací token\>

## <a name="client-limitations"></a>Omezení klienta

Nelze použít veřejné klienty se zástupnými znaky adresy URL odpovědí `id_token` OBO toků. Ale můžete pořád uplatnit důvěrnému klientovi **přístup** přesměrování tokeny, které získali prostřednictvím implicitního udělení toku i v případě, že veřejným klientem obsahuje zástupný znak URI registrován.

## <a name="next-steps"></a>Další postup

Další informace o protokolu OAuth 2.0 a další způsob, jak ověřování služba služba, která používá přihlašovací údaje pro klienta:

* [Služba k ověření služby pomocí udělení přihlašovacích údajů klienta OAuth 2.0 ve službě Azure AD](v1-oauth2-client-creds-grant-flow.md)
* [OAuth 2.0 ve službě Azure AD](v1-protocols-oauth-code.md)

---
title: Ověřování služba-služba Azure AD pomocí OAuth 2.0 On-Behalf-Of koncept specifikace | Dokumentace Microsoftu
description: Tento článek popisuje, jak používat zprávy HTTP k implementaci služeb ověřování pomocí OAuth 2.0 On-Behalf-Of toku.
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
ms.openlocfilehash: a231b79bebd9684281edea48dfe7cf5f57ccdacb
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986011"
---
# <a name="service-to-service-calls-using-delegated-user-identity-in-the-on-behalf-of-flow"></a>Služby volání mezi službami pomocí delegovaná identita uživatele v tok On-Behalf-Of

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

OAuth 2.0 On-Behalf-Of (OBO) tok slouží případ použití, kde aplikace vyvolá služby/webové rozhraní API, které pak je potřeba volat jiné služby nebo webové rozhraní API. Cílem je rozšíření delegovaný uživatel identit a oprávnění pomocí řetězce požadavku. Pro služby střední vrstvy, aby ověřených požadavků pro příjem dat služby je potřeba zabezpečit přístupového tokenu z Azure Active Directory (Azure AD) jménem uživatele.

> [!IMPORTANT]
> Od května 2018 je `id_token` nelze použít pro tok On-Behalf-Of - musí projít SPA **přístup** token do střední vrstvy důvěrnému klientovi provádět OBO toky. Zobrazit [omezení](#client-limitations) podrobné informace, na kterých mohou klienti provádí volání On-Behalf-Of.

## <a name="on-behalf-of-flow-diagram"></a>On-Behalf-Of vývojový diagram
Předpokládejme, že uživatel byl ověřen na aplikace s využitím [toku udělení autorizačního kódu OAuth 2.0](v1-protocols-oauth-code.md). V tomto okamžiku má aplikace přístupový token (token A) s deklarací identity uživatele a vyjádření souhlasu pro přístup k střední vrstvu webového rozhraní API (A rozhraní API). Nyní rozhraní API A potřebuje provést ověřeného požadavku na podřízené webové rozhraní API (API B).

Jaké kroky musí provést tvoří tok On-Behalf-Of a jsou vysvětleny díky pomoci následující diagram.

![OAuth 2.0 On-Behalf-Of toku](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)


1. Klientská aplikace odešle požadavek rozhraní API A s tokenem A.
2. Rozhraní API A ověřuje u konkrétního koncového bodu vystavení tokenu Azure AD a žádá token pro přístup k rozhraní API služby serveru B.
3. Koncový bod vystavení tokenu Azure AD ověří přihlašovací údaje A rozhraní API pomocí tokenu A a vydá přístupový token pro rozhraní API B (token B).
4. Token B je nastavena v hlavičce autorizace požadavku na rozhraní API B.
5. Vrátí data z zabezpečené prostředku rozhraní API služby serveru B.

>[!NOTE]
>Cílovou skupinu deklarací identity v tokenu přístupu použije k vyžádání tokenu pro podřízené služby musí být id služby, který zadal žádost OBO a token musí být podepsány pomocí Azure Active Directory globální podpisový klíč (což je výchozí nastavení pro zaregistrované aplikace prostřednictvím **registrace aplikací** na portálu)

## <a name="register-the-application-and-service-in-azure-ad"></a>Registrace aplikace a služby v Azure AD
Klientská aplikace a služby střední vrstvy zaregistrujte ve službě Azure AD.
### <a name="register-the-middle-tier-service"></a>Registrace služby střední vrstvy
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na horním panelu, klikněte na tlačítko na vašem účtu a v části **Directory** , zvolte tenanta Active Directory, ve kterém chcete registrace vaší aplikace.
3. Klikněte na **další služby** v levém navigačním podokně a zvolte **Azure Active Directory**.
4. Klikněte na **registrace aplikací** a zvolte **registrace nové aplikace**.
5. Zadejte popisný název pro aplikaci a vyberte typ aplikace. Na základě aplikace typu sady přihlašovací adresu URL nebo přesměrovat adresu URL pro základní adresu URL. Klikněte na **vytvořit** k vytvoření aplikace.
6. Zatímco jste pořád na webu Azure Portal vyberte vaši aplikaci a klikněte na **nastavení**. V nabídce nastavení zvolte **klíče** a přidejte klíč – výběr klíče trvání buď 1 nebo 2 roky. Při uložení této stránky, hodnota klíče se zobrazí, zkopírujte a uložte hodnotu na bezpečném místě – budete potřebovat tento klíč později ke konfiguraci nastavení aplikace ve vaší implementaci - touto hodnotou klíče nesmí být znovu zobrazené ani retrievable jiným způsobem , abyste si poznamenejte co nejdříve je viditelný na webu Azure Portal.

### <a name="register-the-client-application"></a>Registrace aplikace klienta
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na horním panelu, klikněte na tlačítko na vašem účtu a v části **Directory** , zvolte tenanta Active Directory, ve kterém chcete registrace vaší aplikace.
3. Klikněte na **další služby** v levém navigačním podokně a zvolte **Azure Active Directory**.
4. Klikněte na **registrace aplikací** a zvolte **registrace nové aplikace**.
5. Zadejte popisný název pro aplikaci a vyberte typ aplikace. Na základě aplikace typu sady přihlašovací adresu URL nebo přesměrovat adresu URL pro základní adresu URL. Klikněte na **vytvořit** k vytvoření aplikace.
6. Konfigurace oprávnění pro aplikace – v nabídce nastavení, zvolte **požadovaná oprávnění** části, klikněte na **přidat**, pak **vyberte rozhraní API**a zadejte název Služba střední vrstvy do textového pole. Potom klikněte na **oprávnění Select** a vyberte "přístup *název služby*".

### <a name="configure-known-client-applications"></a>Konfigurace známé klientské aplikace
V tomto scénáři má střední vrstvy služby bez zásahu uživatele získat souhlas uživatele pro přístup k rozhraní API pro příjem. Proto možnost udělit přístup k rozhraní API pro příjem dat musí být předkládány předem část souhlasu krok během ověřování.
Za tím účelem podle následujících pokynů explicitně vytvořit vazbu registrace klientskou aplikaci ve službě Azure AD s registrací služby střední vrstvy, která sloučí souhlas potřebný klienta a střední vrstvy do jednoho dialogového okna.
1. Přejít na registrace služby střední vrstvy a klikněte na **Manifest** otevřete editor manifestu.
2. V manifestu, vyhledejte `knownClientApplications` pole vlastností a přidejte klientské ID klientské aplikace jako element.
3. Uložte kliknutím uložit manifest tlačítko.

## <a name="service-to-service-access-token-request"></a>Služby na žádost o přístupový token služby
Požádat o přístupový token, ujistěte se, HTTP POST do konkrétního klienta koncového bodu Azure AD s následujícími parametry.

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```
Existují dva možné případy v závislosti na tom, jestli klientská aplikace rozhodne bylo zabezpečené protokolem sdílený tajný klíč, nebo certifikát.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>První věc: žádost o přístupový token s sdílený tajný klíč
Pokud používáte sdílený tajný klíč, žádosti o token přístupu service to service obsahuje následující parametry:

| Parametr |  | Popis |
| --- | --- | --- |
| Parametr grant_type |povinné | Typ požadavku na token. Protože požadavek OBO používá přístupový token JWT, musí být hodnota **urn: ietf:params:oauth:grant-typu: jwt-nosiče**. |
| kontrolní výraz |povinné | Hodnota přístupový token, který je použitý v požadavku. |
| client_id |povinné | ID aplikace přiřazené k volání služby během registrace ve službě Azure AD. Chcete-li najít ID aplikace v Azure Management Portal, klikněte na tlačítko **služby Active Directory**, klikněte na adresář a potom klikněte na název aplikace. |
| Hodnota client_secret |povinné | Klíč zaregistrovaný pro volání služby ve službě Azure AD. Tato hodnota by bylo zaznamenáno v době registrace. |
| prostředek |povinné | Identifikátor URI ID aplikace přijímající služby (zabezpečeným prostředkům). Chcete-li najít identifikátor ID URI aplikace v Azure Management Portal, klikněte na tlačítko **služby Active Directory**, klikněte na adresář, klikněte na název aplikace, klikněte na tlačítko **všechna nastavení** a potom klikněte na **vlastnosti**. |
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
| Parametr grant_type |povinné | Typ požadavku na token. Protože požadavek OBO používá přístupový token JWT, musí být hodnota **urn: ietf:params:oauth:grant-typu: jwt-nosiče**. |
| kontrolní výraz |povinné | Hodnota tokenu používaného v požadavku. |
| client_id |povinné | ID aplikace přiřazené k volání služby během registrace ve službě Azure AD. Chcete-li najít ID aplikace v Azure Management Portal, klikněte na tlačítko **služby Active Directory**, klikněte na adresář a potom klikněte na název aplikace. |
| client_assertion_type |povinné |Hodnota musí být `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |povinné | (JSON Web Token) kontrolního výrazu, které potřebujete k vytvoření a podepsání certifikátem zaregistrujete jako přihlašovací údaje pro vaši aplikaci. Přečtěte si informace o [certifikát přihlašovacích údajů](active-directory-certificate-credentials.md) informace o registraci vašeho certifikátu a formát kontrolního výrazu.|
| prostředek |povinné | Identifikátor URI ID aplikace přijímající služby (zabezpečeným prostředkům). Chcete-li najít identifikátor ID URI aplikace v Azure Management Portal, klikněte na tlačítko **služby Active Directory**, klikněte na adresář, klikněte na název aplikace, klikněte na tlačítko **všechna nastavení** a potom klikněte na **vlastnosti**. |
| requested_token_use |povinné | Určuje, jak by měl být požadavek zpracovat. Tok On-Behalf-Of, musí být hodnota **on_behalf_of**. |
| scope |povinné | Mezerou oddělený seznam oborů pro žádosti o token. Pro OpenID Connect, oboru **openid** musí být zadán.|

Všimněte si, že parametry jsou téměř stejné jako v případě žádosti sdílený tajný klíč, s tím rozdílem, že parametr hodnota client_secret nahrazuje dva parametry: client_assertion_type a client_assertion.

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

## <a name="service-to-service-access-token-response"></a>Služby k odpovědi tokenu služby access
Úspěšná odpověď je odpověď JSON OAuth 2.0 s následujícími parametry.

| Parametr | Popis |
| --- | --- |
| token_type |Určuje hodnotu pro typ tokenu. Jediný typ, který podporuje Azure AD je **nosiče**. Další informace o nosné tokeny, najdete v článku [Framework autorizace OAuth 2.0: použití nosného tokenu (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Obor přístupu v tokenu. |
| expires_in |Dlouhá doba přístupový token je platný (v sekundách). |
| expires_on |Čas, kdy vyprší platnost přístupového tokenu. Datum je vyjádřena jako počet sekund od 1970-01-01T0:0:0Z UTC až do okamžiku vypršení platnosti. Tato hodnota se používá k určení doby života tokenů v mezipaměti. |
| prostředek |Identifikátor URI ID aplikace přijímající služby (zabezpečeným prostředkům). |
| access_token |Požadovaný přístupový token. Volání služby můžete použít tento token k ověření přijímající služby. |
| id_token |Požadované id tokenu. Volání služby může být využit k ověření identity uživatele a zahájit relaci s uživatelem. |
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
Koncový bod tokenu Azure AD vrátí reakce na chybu při pokusu o získání přístupového tokenu pro rozhraní API pro příjem dat, pokud rozhraní API pro příjem dat má zásady podmíněného přístupu, jako je ověřování službou Multi-Factor Authentication u něho nastavený. Střední vrstvy služby by měl plochu tuto chybu do klientské aplikace tak, aby klientská aplikace může poskytnout interakce uživatele splňovat zásady podmíněného přístupu.

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
Nyní služby střední vrstvy můžete použít token získali výše aby ověřených požadavků pro podřízené webové rozhraní API, tak, že nastavíte tokenu `Authorization` záhlaví.

### <a name="example"></a>Příklad:
```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```
## <a name="service-to-service-calls-using-a-saml-assertion-obtained-with-an-oauth20-on-behalf-of-flow"></a>Volání mezi službami pomocí získaných pomocí OAuth 2.0 on-behalf-of flow kontrolní výraz SAML

Některé OAuth na základě webové služby je potřeba přístup k jiné webové službě rozhraní API, která přijímají kontrolní výrazy SAML v neinteraktivním přístupu toky.  Azure Active Directory může poskytovat kontrolní výraz SAML v reakci na on-behalf-of tok, který službě založené na SAML jako cílový prostředek. 

>[!NOTE] 
>To je nestandardní rozšíření tok OAuth 2.0 on-behalf-of, který umožňuje OAuth2 na základě aplikace pro přístup k rozhraní API koncových bodů webové služby, které používají tokeny SAML.  

>[!TIP]
>Z front-endové webové aplikace při volání webové služby chráněný SAML, můžete jednoduše volání rozhraní API a spustit běžné interaktivní ověřování tok, který budou používat uživatelé existující relaci.  Stačí zvažte použití flow OBO při volání služeb vyžaduje token SAML poskytnout uživatelský kontext.

### <a name="obtain-a-saml-token-using-an-obo-request-with-a-shared-secret"></a>Získat požadavek OBO pomocí sdílený tajný klíč tokenu SAML
Služba služba žádost o získání kontrolní výraz SAML obsahuje následující parametry:

| Parametr |  | Popis |
| --- | --- | --- |
| Parametr grant_type |povinné | Typ požadavku na token. Pro žádost o pomocí token JWT, hodnota musí být **urn: ietf:params:oauth:grant-typu: jwt-nosiče**. |
| kontrolní výraz |povinné | Hodnota přístupový token, který je použitý v požadavku.|
| client_id |povinné | ID aplikace přiřazené k volání služby během registrace ve službě Azure AD. Chcete-li najít ID aplikace v Azure Management Portal, klikněte na tlačítko **služby Active Directory**, klikněte na adresář a potom klikněte na název aplikace. |
| Hodnota client_secret |povinné | Klíč zaregistrovaný pro volání služby ve službě Azure AD. Tato hodnota by bylo zaznamenáno v době registrace. |
| prostředek |povinné | Identifikátor URI ID aplikace přijímající služby (zabezpečeným prostředkům). Toto je prostředek, který bude cílová skupina tokenu SAML.  Chcete-li najít identifikátor ID URI aplikace v Azure Management Portal, klikněte na tlačítko **služby Active Directory**, klikněte na adresář, klikněte na název aplikace, klikněte na tlačítko **všechna nastavení** a potom klikněte na **vlastnosti**. |
| requested_token_use |povinné | Určuje, jak by měl být požadavek zpracovat. Tok On-Behalf-Of, musí být hodnota **on_behalf_of**. |
| requested_token_type | povinné | Určuje typ tokenu požadováno.  Hodnota může být "urn: ietf:params:oauth:token-typu: saml2" nebo "urn: ietf:params:oauth:token-typu: saml1" v závislosti na požadavcích na prostředek, ke kterému přistupujete. |


Odpověď bude obsahovat UTF8 a Base64url kódováním SAML token. 

Zdrojem je volání rozhraní OBO SubjectConfirmationData pro kontrolní výraz SAML: Pokud je cílová aplikace vyžaduje hodnotu příjemce v SubjectConfirmationData, pak musí být nastavena jako adresu URL odpovědi bez zástupných znaků v konfiguraci prostředků aplikace.

SubjectConfirmationData uzel nemůže obsahovat atribut InResponseTo, protože není součástí odpověď SAML.  Aplikace SAML přijímají token musí být schopen přijmout bez atributu InResponseTo kontrolního výrazu SAML.

Souhlas: Aby bylo možné přijímat tokenu SAML, který obsahuje data uživatele na tok OAuth, který souhlasu musí být udělena.  Podrobnosti najdete na https://docs.microsoft.com/azure/active-directory/develop/v1-permissions-and-consent informace o oprávnění a získání souhlas správce.

### <a name="response-with-saml-assertion"></a>Odpověď se kontrolní výraz SAML

| Parametr | Popis |
| --- | --- |
| token_type |Určuje hodnotu pro typ tokenu. Jediný typ, který podporuje Azure AD je **nosiče**. Další informace o nosné tokeny, najdete v článku [Framework autorizace OAuth 2.0: použití nosného tokenu (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Obor přístupu v tokenu. |
| expires_in |Dlouhá doba přístupový token je platný (v sekundách). |
| expires_on |Čas, kdy vyprší platnost přístupového tokenu. Datum je vyjádřena jako počet sekund od 1970-01-01T0:0:0Z UTC až do okamžiku vypršení platnosti. Tato hodnota se používá k určení doby života tokenů v mezipaměti. |
| prostředek |Identifikátor URI ID aplikace přijímající služby (zabezpečeným prostředkům). |
| access_token |Kontrolní výraz SAML se vrátí parametr access_token. |
| refresh_token |Token obnovení. Volání služby můžete tento token vyžádat dalšího přístupového tokenu po vypršení platnosti aktuální kontrolní výraz SAML. |

token_type: nosiče expires_in:3296 ext_expires_in:0 expires_on:1529627844 prostředků:https://api.contoso.com access_token: <Saml assertion> issued_token_type:urn:ietf:params:oauth:token-typu: saml2 refresh_token: <Refresh token>

## <a name="client-limitations"></a>Omezení klienta
Nelze použít veřejné klienty se zástupnými znaky adresy URL odpovědí `id_token` OBO toků. Důvěrnému klientovi může uplatnit však stále přístupové tokeny, které získali prostřednictvím implicitního udělení toku i v případě veřejným klientem zaregistruje identifikátor URI přesměrování zástupný znak.

## <a name="next-steps"></a>Další postup
Další informace o protokolu OAuth 2.0 a další způsob, jak provádět ověřování služba-služba pomocí přihlašovacích údajů klienta.
* [Služba ověřování služby přes udělení přihlašovacích údajů klienta OAuth 2.0 ve službě Azure AD](v1-oauth2-client-creds-grant-flow.md)
* [OAuth 2.0 ve službě Azure AD](v1-protocols-oauth-code.md)
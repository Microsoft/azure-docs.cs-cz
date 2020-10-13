---
title: Microsoft Identity Platform a OAuth 2.0 s tokem za chodu | Azure
titleSuffix: Microsoft identity platform
description: Tento článek popisuje, jak pomocí zpráv HTTP implementovat ověřování Service to Service over pomocí toku OAuth 2.0 za běhu.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/7/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 018d67b3e4e730cd46eb524a8927b3a6d68d74e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88958656"
---
# <a name="microsoft-identity-platform-and-oauth-20-on-behalf-of-flow"></a>Microsoft Identity Platform a OAuth 2,0 s tokem za chodu


OBO (OAuth 2,0 on-of-jménem toku) slouží k použití případu, kdy aplikace vyvolá službu nebo webové rozhraní API, která zase potřebuje volat jiné služby nebo webové rozhraní API. Nápad je rozšířit identitu delegovaného uživatele a oprávnění prostřednictvím řetězce požadavků. Aby služba střední vrstvy prováděla ověřené požadavky na službu pro příjem dat, musí jménem uživatele zabezpečit přístupový token od platformy Microsoft identity.

Tento článek popisuje, jak programovat přímo s protokolem ve vaší aplikaci.  Pokud je to možné, doporučujeme místo toho použít podporované knihovny Microsoft Authentication Library (MSAL) k [získání tokenů a volání zabezpečených webových rozhraní API](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Podívejte se také na [ukázkové aplikace, které používají MSAL](sample-v2-code.md).

> [!NOTE]
> Od května 2018 se v `id_token` OBO toku nedá použít nějaký odvozený implicitní tok. Jednostránkové aplikace (jednostránkové) by měly předat **přístupovému** klientovi střední vrstvy přístupový token, aby se místo toho prováděly OBO toky. Další informace o tom, kteří klienti můžou provádět volání OBO, najdete v tématu [omezení](#client-limitations).

## <a name="protocol-diagram"></a>Diagram protokolu

Předpokládejme, že uživatel byl ověřený v aplikaci pomocí [toku udělení autorizačního kódu OAuth 2,0](v2-oauth2-auth-code-flow.md) nebo jiného toku přihlášení. V tomto okamžiku má aplikace přístupový token *pro rozhraní API a* (token A) s deklaracemi identity uživatele a souhlasem pro přístup k webovému rozhraní API střední vrstvy (API a). Rozhraní API nyní potřebuje ověřený požadavek webového rozhraní API pro příjem dat (API B).

Následující kroky představují tok OBO a jsou vysvětleny pomocí následujícího diagramu.

![Zobrazuje tok za běhu OAuth 2.0.](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. Klientská aplikace vytvoří požadavek na rozhraní API A s tokenem A (s `aud` DEKLARACÍ API a).
1. Rozhraní API A ověřuje koncový bod vystavování tokenu platformy Microsoft identity a požaduje token pro přístup k rozhraní API B.
1. Koncový bod vystavování tokenu platformy Microsoft Identity ověřuje přihlašovací údaje A pověření rozhraní API spolu s tokenem a a vydá přístupový token pro rozhraní API B (token B) k rozhraní API A.
1. Token B je nastaven rozhraním API A v autorizační hlavičce požadavku na rozhraní API B.
1. Data z zabezpečeného prostředku jsou vrácena rozhraním API B do rozhraní API a a odtud na klienta.

> [!NOTE]
> V tomto scénáři nemá služba střední vrstvy žádnou interakci s uživatelem, aby získala souhlas uživatele s přístupem k rozhraní API pro příjem dat. Proto možnost udělení přístupu k rozhraní API pro příjem dat se při ověřování prezentuje jako součást kroku souhlasu. Informace o tom, jak tuto aplikaci nastavit, najdete v tématu [získání souhlasu pro aplikaci střední vrstvy](#gaining-consent-for-the-middle-tier-application).

## <a name="middle-tier-access-token-request"></a>Žádost o přístupový token střední vrstvy

Pokud chcete požádat o přístupový token, vytvořte HTTP POST do koncového bodu tokenu platformy Microsoft Identity Platform-Specific s následujícími parametry.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Existují dva případy, v závislosti na tom, jestli se klientská aplikace rozhodne zabezpečit sdíleným tajným klíčem nebo certifikátem.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>První případ: žádost přístupového tokenu se sdíleným tajným klíčem

Při použití sdíleného tajného klíče obsahuje požadavek na přístupový token služby na službu následující parametry:

| Parametr | Typ | Popis |
| --- | --- | --- |
| `grant_type` | Povinné | Typ žádosti o token Pro požadavek používající token JWT musí být hodnota `urn:ietf:params:oauth:grant-type:jwt-bearer` . |
| `client_id` | Vyžadováno | ID aplikace (klienta), které stránka [Azure Portal-registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) přiřadila k vaší aplikaci. |
| `client_secret` | Vyžadováno | Tajný kód klienta, který jste vygenerovali pro vaši aplikaci na stránce Azure Portal-Registrace aplikací. |
| `assertion` | Vyžadováno | Přístupový token, který se odeslal do rozhraní API střední vrstvy.  Tento token musí mít `aud` deklaraci identity cílové skupiny () aplikace, která tuto žádost OBO (aplikace označuje `client-id` pole). Aplikace nemůžou uplatnit token pro jinou aplikaci (takže pokud klient pošle token API, který je určený pro MS Graph, rozhraní API ho nemůže uplatnit pomocí OBO.  Místo toho by se měl token zamítnout.  |
| `scope` | Vyžadováno | Mezerou oddělený seznam oborů pro požadavek na token. Další informace najdete v tématu [obory](v2-permissions-and-consent.md). |
| `requested_token_use` | Vyžadováno | Určuje, jak se má požadavek zpracovat. V toku OBO musí být hodnota nastavena na `on_behalf_of` . |

#### <a name="example"></a>Příklad

Následující příspěvek HTTP požaduje přístupový token a aktualizuje token s `user.read` oborem pro https://graph.microsoft.com webové rozhraní API.

```HTTP
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com/<tenant>
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyO{a lot of characters here}
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Druhý případ: žádost o přístupový token s certifikátem

Požadavek na přístupový token služby na službu s certifikátem obsahuje následující parametry:

| Parametr | Typ | Popis |
| --- | --- | --- |
| `grant_type` | Povinné | Typ požadavku tokenu Pro požadavek používající token JWT musí být hodnota `urn:ietf:params:oauth:grant-type:jwt-bearer` . |
| `client_id` | Vyžadováno |  ID aplikace (klienta), které stránka [Azure Portal-registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) přiřadila k vaší aplikaci. |
| `client_assertion_type` | Vyžadováno | Hodnota musí být `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` . |
| `client_assertion` | Vyžadováno | Kontrolní výraz (webový token JSON), který potřebujete k vytvoření a podepsání certifikátu, který jste zaregistrovali jako přihlašovací údaje pro vaši aplikaci. Informace o tom, jak zaregistrovat certifikát a formát kontrolního výrazu, najdete v tématu [přihlašovací údaje certifikátu](active-directory-certificate-credentials.md). |
| `assertion` | Vyžadováno |  Přístupový token, který se odeslal do rozhraní API střední vrstvy.  Tento token musí mít `aud` deklaraci identity cílové skupiny () aplikace, která tuto žádost OBO (aplikace označuje `client-id` pole). Aplikace nemůžou uplatnit token pro jinou aplikaci (takže pokud klient pošle token API, který je určený pro MS Graph, rozhraní API ho nemůže uplatnit pomocí OBO.  Místo toho by se měl token zamítnout.  |
| `requested_token_use` | Vyžadováno | Určuje, jak se má požadavek zpracovat. V toku OBO musí být hodnota nastavena na `on_behalf_of` . |
| `scope` | Vyžadováno | Mezerou oddělený seznam oborů pro požadavek na token. Další informace najdete v tématu [obory](v2-permissions-and-consent.md).|

Všimněte si, že parametry jsou skoro stejné jako v případě požadavku pomocí sdíleného tajného kódu s tím rozdílem, že `client_secret` parametr je nahrazen dvěma parametry: `client_assertion_type` a a `client_assertion` .

#### <a name="example"></a>Příklad

Následující příspěvek HTTP požaduje přístupový token s `user.read` oborem pro https://graph.microsoft.com webové rozhraní API s certifikátem.

```HTTP
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com/<tenant>
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiO{a lot of characters here}
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="middle-tier-access-token-response"></a>Odezva přístupového tokenu střední vrstvy

Odpověď na úspěch je odpověď protokolu JSON OAuth 2,0 s následujícími parametry.

| Parametr | Popis |
| --- | --- |
| `token_type` | Určuje hodnotu typu tokenu. Jediným typem, který podporuje platforma Microsoft Identity Platform, je `Bearer` . Další informace o nosných tokenech najdete v části [autorizační rozhraní OAuth 2,0: použití nosných tokenů (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| `scope` | Rozsah přístupu uděleného v tokenu. |
| `expires_in` | Doba, po kterou je přístupový token platný (v sekundách). |
| `access_token` | Požadovaný přístupový token Volající služba může tento token použít k ověření pro přijímající službu. |
| `refresh_token` | Obnovovací token požadovaného přístupového tokenu. Volající služba může tento token použít k vyžádání jiného přístupového tokenu po vypršení platnosti aktuálního přístupového tokenu. Aktualizační token je poskytován pouze v případě, že `offline_access` byl požadován obor. |

### <a name="success-response-example"></a>Příklad odpovědi na úspěch

Následující příklad ukazuje odpověď na úspěch na žádost o přístupový token pro https://graph.microsoft.com webové rozhraní API.

```json
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4An{a lot of characters here}"
}
```

> [!NOTE]
> Výše přístupový token je ve formátu Microsoft Graph v 1.0. Důvodem je to, že formát tokenu je založený na **prostředku** , ke kterému se přistupoval a který nesouvisí s koncovými body použitými k vyžádání. Microsoft Graph je instalační program, který přijímá tokeny v 1.0, takže platforma Microsoft Identity Platform generuje přístupové tokeny v 1.0, když klient požaduje tokeny pro Microsoft Graph. Jiné aplikace mohou značit, že mají tokeny formátu v 2.0, tokeny formátu v 1.0 nebo dokonce speciální nebo šifrované formáty tokenů.  Koncové body v 1.0 a v 2.0 můžou emitovat buď formát tokenu – tímto způsobem může prostředek vždycky získat správný formát tokenu bez ohledu na to, jak nebo kde byl token vyžádán klientem. 
>
> V tokenech přístupu by se měly zobrazit jenom aplikace. Klienti je **nesmí** kontrolovat. Kontrola přístupových tokenů pro jiné aplikace ve vašem kódu způsobí neočekávané porušení vaší aplikace, když se změní formát svých tokenů nebo začne jejich šifrování. 

### <a name="error-response-example"></a>Příklad chybové odpovědi

Koncový bod tokenu vrátí chybovou odpověď při pokusu o získání přístupového tokenu pro rozhraní API pro příjem dat, pokud pro podřízené rozhraní API existuje zásada podmíněného přístupu (například [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)) nastavená na něm. Služba střední vrstvy by tuto chybu měla obit klientské aplikaci, aby mohla klientská aplikace poskytnout interakci uživatele, aby splnila zásady podmíněného přístupu.

```json
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

Služba střední vrstvy teď může použít token získaný výše k provádění ověřených požadavků webového rozhraní API pro příjem dat, a to nastavením tokenu v `Authorization` záhlaví.

### <a name="example"></a>Příklad

```HTTP
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>Kontrolní výrazy SAML získané pomocí toku OBO OAuth 2.0

Některé webové služby založené na protokolu OAuth potřebují přístup k dalším rozhraním API webových služeb, která přijímají kontrolní výrazy SAML v neinteraktivních tocích. Azure Active Directory může poskytnout kontrolní výraz SAML v reakci na tok, který používá webovou službu založenou na SAML jako cílový prostředek.

>[!NOTE]
>Toto je nestandardní rozšíření pro tok OAuth 2,0, který umožňuje aplikaci založené na OAuth2 přistupovat k koncovým bodům rozhraní API webové služby, které využívají tokeny SAML.

> [!TIP]
> Když zavoláte webovou službu chráněnou SAML z front-endové webové aplikace, můžete jednoduše zavolat rozhraní API a zahájit normální tok interaktivního ověřování s existující relací uživatele. Tok OBO je potřeba použít jenom v případě, že volání služba-služba vyžaduje, aby token SAML poskytoval kontext uživatele.

## <a name="gaining-consent-for-the-middle-tier-application"></a>Získání souhlasu pro aplikaci střední vrstvy

V závislosti na architektuře nebo využití vaší aplikace můžete zvážit různé strategie pro zajištění úspěšnosti OBO toku. Ve všech případech je konečným cílem zajistit, aby klientská aplikace mohla zavolat aplikaci střední vrstvy a aplikace střední vrstvy má oprávnění k volání prostředku back-endu.

> [!NOTE]
> Dřív účet Microsoft systém (osobní účty) nepodporoval pole známá klientská aplikace, ani by nemohl zobrazit kombinovaný souhlas.  Přidaná a všechny aplikace na platformě Microsoft Identity můžou k získání souhlasu s voláním OBO používat známý klientský přístup k aplikacím.

### <a name="default-and-combined-consent"></a>/.Default a kombinovaný souhlas

Aplikace střední vrstvy přidá klienta do seznamu známých klientských aplikací ve svém manifestu a klient může aktivovat kombinovaný postup souhlasu pro sebe i pro aplikaci střední vrstvy. Na koncovém bodu Microsoft Identity Platform se to dělá pomocí [ `/.default` oboru](v2-permissions-and-consent.md#the-default-scope). Při aktivaci obrazovky pro vyjádření souhlasu pomocí známých klientských aplikací a `/.default` na obrazovce pro vyjádření souhlasu se zobrazí oprávnění **both** pro klienta pro rozhraní API střední vrstvy a také si vyžádají jakékoli oprávnění, které jsou vyžadovány rozhraním API střední vrstvy. Uživatel poskytne souhlas obou aplikací a OBO tok funguje.

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
* [Použití `/.default` oboru](v2-permissions-and-consent.md#the-default-scope)

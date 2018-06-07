---
title: Azure AD do služby ověřování pomocí OAuth2.0 On-Behalf-Of koncept specifikace | Microsoft Docs
description: Tento článek popisuje, jak používat zprávy protokolu HTTP k implementaci služeb ověřování pomocí OAuth2.0 tok On-Behalf-Of.
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
ms.reviewer: hirsin; nacanuma
ms.custom: aaddev
ms.openlocfilehash: 2bb0d10fee04c4ee48344695769fa7768b0f3a85
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823862"
---
# <a name="service-to-service-calls-using-delegated-user-identity-in-the-on-behalf-of-flow"></a>Služby volání služby s využitím delegované identity uživatele v tok On-Behalf-Of
OAuth 2.0 On-Behalf-Of (OBO) toku slouží případ použití, kde aplikace volá služby nebo webové rozhraní API, který se pak musí volat jiné služby nebo webové rozhraní API. Cílem je potřebný k šíření identity delegované uživatele a oprávnění pomocí řetězce požadavků. Pro službu střední vrstvy provést ověřené žádosti o připojení ke službě podřízené potřebuje přístupový token zabezpečení ze služby Azure Active Directory (Azure AD) jménem uživatele.

> [!IMPORTANT]
> Veřejné klienty, kteří používají [implicitní grant OAuth 2.0](active-directory-dev-understanding-oauth2-implicit-grant.md) nelze použít OBO toku. Tito klienti musí předat střední vrstvy důvěrné klienta k provedení OBO toky jejich přístupový token. Další informace o tom, které klienty můžete provádět volání OBO najdete v tématu [omezení klienta](#client-limitations).

## <a name="on-behalf-of-flow-diagram"></a>On-Behalf-Of vývojový diagram
Předpokládejme, že má uživatel ověřen na aplikace pomocí [tok poskytování autorizačních kódů OAuth 2.0](active-directory-protocols-oauth-code.md). Aplikace v tomto okamžiku má přístupový token (token A) s deklarací identity uživatele a souhlasu pro přístup k střední vrstvu webového rozhraní API (rozhraní API A). Rozhraní API A potřebuje teď, aby požadavek na ověřeného k podřízené webové rozhraní API (API B).

Kroky, které následují tvoří tok On-Behalf-Of a jsou vysvětleny za pomoci následující diagram.

![OAuth2.0 tok On-Behalf-Of](media/active-directory-protocols-oauth-on-behalf-of/active-directory-protocols-oauth-on-behalf-of-flow.png)


1. Klientská aplikace odešle požadavek na rozhraní API s tokenem A.
2. Rozhraní API A přihlásí k vystavování tokenů koncového bodu Azure AD a požadavky token pro přístup k rozhraní API B.
3. Koncový bod vystavování tokenů Azure AD ověřuje přihlašovací údaje A rozhraní API se token a vydá přístupový token pro rozhraní API B (tokenu B).
4. Token B je nastaveny v hlavičce autorizace požadavku rozhraní API b.
5. Vrátí data z zabezpečeným prostředkům rozhraní API B.

## <a name="register-the-application-and-service-in-azure-ad"></a>Registrace aplikace a služby ve službě Azure AD
Klientská aplikace a služby střední vrstvy zaregistrujte ve službě Azure AD.
### <a name="register-the-middle-tier-service"></a>Registrace služby střední vrstvy
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na horním panelu klikněte na tlačítko na vašem účtu a v části **Directory** vyberte klienta služby Active Directory, kam chcete registrace vaší aplikace.
3. Klikněte na **více služeb** v navigaci vlevo a zvolte **Azure Active Directory**.
4. Klikněte na **registrace aplikace** a zvolte **nové registrace aplikace**.
5. Zadejte popisný název aplikace a vyberte typ aplikace. Založené na sadě typu aplikace přihlašovací adresa URL nebo přesměrovat adresu URL na základní adresu URL. Klikněte na **vytvořit** k vytvoření aplikace.
6. Při stále na portálu Azure, vyberte aplikaci a klikněte na **nastavení**. Z nabídky nastavení zvolte **klíče** a přidejte klíč – vyberte dobu trvání klíče 1 rok nebo 2 roky. Při ukládání této stránky, hodnota klíče se zobrazí, zkopírujte a uložte do bezpečného umístění hodnota – budete potřebovat tento klíč později ke konfiguraci nastavení aplikace v implementaci - touto hodnotou klíče nebude znovu zobrazit, ani se dá načíst jiným způsobem , takže si poznamenejte hned, jak je vidět na portálu Azure.

### <a name="register-the-client-application"></a>Registrace aplikace klienta
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na horním panelu klikněte na tlačítko na vašem účtu a v části **Directory** vyberte klienta služby Active Directory, kam chcete registrace vaší aplikace.
3. Klikněte na **více služeb** v navigaci vlevo a zvolte **Azure Active Directory**.
4. Klikněte na **registrace aplikace** a zvolte **nové registrace aplikace**.
5. Zadejte popisný název aplikace a vyberte typ aplikace. Založené na sadě typu aplikace přihlašovací adresa URL nebo přesměrovat adresu URL na základní adresu URL. Klikněte na **vytvořit** k vytvoření aplikace.
6. Konfigurace oprávnění pro vaši aplikaci – v nabídce nastavení, vyberte **požadovaná oprávnění** části, klikněte na **přidat**, pak **vybrat rozhraní API**a zadejte název služby střední vrstvy do textového pole. Potom klikněte na **vyberte oprávnění** a vyberte možnost ' přístup *název služby*'.

### <a name="configure-known-client-applications"></a>Konfigurace známé klientské aplikace
V tomto scénáři má služby střední vrstvy získat souhlas uživatele pro přístup k rozhraní API pro příjem dat žádná interakce s uživatelem. Možnost udělit přístup k rozhraní API pro příjem dat proto musí být uveden předem, jako součást souhlasu krok během ověřování.
Jak toho docílit, použijte následující postup pro explicitně vazbu klientskou aplikaci registrace ve službě Azure AD s registrací služby střední vrstvy, která sloučí souhlas potřebný klientem a střední vrstvy do jednoho dialogové okno.
1. Přejděte na registraci střední vrstvy služby a klikněte na **Manifest** k otevření editoru manifestu.
2. V manifestu, vyhledejte `knownClientApplications` pole vlastnosti, a přidejte ID klienta aplikace klienta jako element.
3. Uložte kliknutím uložit manifest tlačítko.

## <a name="service-to-service-access-token-request"></a>Služba service žádosti o token přístupu
Chcete-li požádat o token přístupu, provést HTTP POST konkrétního klienta koncového bodu Azure AD s následujícími parametry.

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```
Jsou dva případy, v závislosti na tom, zda klientská aplikace rozhodne zabezpečeny sdílený tajný klíč nebo certifikát.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Nejprve případ: žádosti o token přístupu s sdílený tajný klíč
Pokud používáte sdílený tajný klíč, žádosti o token přístupu service-to-service obsahuje následující parametry:

| Parametr |  | Popis |
| --- | --- | --- |
| grant_type |povinné | Typ požadavku na token. Pro žádost o pomocí token JWT, hodnota musí být **urn: ietf:params:oauth:grant – typ: jwt-nosiče**. |
| Kontrolní výraz |povinné | Hodnota tokenu používaného v požadavku. |
| client_id |povinné | ID aplikace přiřazené ke službě volání během registrace s Azure AD. Chcete-li najít ID aplikace v portálu pro správu Azure, klikněte na tlačítko **služby Active Directory**, klikněte na adresář a potom klikněte na název aplikace. |
| tajný klíč client_secret |povinné | Klíč registrován pro volání služby ve službě Azure AD. Tato hodnota by měla mít poznamenat v době registrace. |
| prostředek |povinné | Identifikátor ID URI aplikace služby přijímající (zabezpečené prostředků). Chcete-li najít identifikátor ID URI aplikace v portálu pro správu Azure, klikněte na tlačítko **služby Active Directory**, klikněte na adresář, klikněte na název aplikace, klikněte na **všechna nastavení** a pak klikněte na tlačítko **vlastnosti**. |
| requested_token_use |povinné | Určuje, jak by měl být požadavek zpracovat. Hodnota tok On-Behalf-Of, musí být **on_behalf_of**. |
| scope |povinné | Mezeru oddělený seznam obory pro požadavek tokenu. Pro OpenID Connect, oboru **openid** musí být zadán.|

#### <a name="example"></a>Příklad:
Přístupový token pro požadavky následující HTTP POST https://graph.windows.net webové rozhraní API. `client_id` Identifikuje služba, která požaduje přístupový token.

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

### <a name="second-case-access-token-request-with-a-certificate"></a>Druhé případ: tokenu žádosti o přístup pomocí certifikátu
Žádosti o token service-to-service přístup pomocí certifikátu obsahuje následující parametry:

| Parametr |  | Popis |
| --- | --- | --- |
| grant_type |povinné | Typ požadavku na token. Pro žádost o pomocí token JWT, hodnota musí být **urn: ietf:params:oauth:grant – typ: jwt-nosiče**. |
| Kontrolní výraz |povinné | Hodnota tokenu používaného v požadavku. |
| client_id |povinné | ID aplikace přiřazené ke službě volání během registrace s Azure AD. Chcete-li najít ID aplikace v portálu pro správu Azure, klikněte na tlačítko **služby Active Directory**, klikněte na adresář a potom klikněte na název aplikace. |
| client_assertion_type |povinné |Hodnota musí být `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |povinné | (Webového tokenu JSON) kontrolní výraz, který potřebujete k vytvoření a podepsání s certifikátem, můžete zaregistrovat jako přihlašovací údaje pro vaši aplikaci. Přečtěte si informace o [certifikát přihlašovacích údajů](active-directory-certificate-credentials.md) Další informace o registraci vašeho certifikátu a formát kontrolní výraz.|
| prostředek |povinné | Identifikátor ID URI aplikace služby přijímající (zabezpečené prostředků). Chcete-li najít identifikátor ID URI aplikace v portálu pro správu Azure, klikněte na tlačítko **služby Active Directory**, klikněte na adresář, klikněte na název aplikace, klikněte na **všechna nastavení** a pak klikněte na tlačítko **vlastnosti**. |
| requested_token_use |povinné | Určuje, jak by měl být požadavek zpracovat. Hodnota tok On-Behalf-Of, musí být **on_behalf_of**. |
| scope |povinné | Mezeru oddělený seznam obory pro požadavek tokenu. Pro OpenID Connect, oboru **openid** musí být zadán.|

Všimněte si, že parametry jsou téměř stejné jako v případě požadavku pomocí sdílený tajný klíč, s tím rozdílem, že parametr tajný klíč client_secret je nahrazena dva parametry: client_assertion_type a client_assertion.

#### <a name="example"></a>Příklad:
Přístupový token pro požadavky následující HTTP POST https://graph.windows.net webové rozhraní API s certifikátem. `client_id` Identifikuje služba, která požaduje přístupový token.

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

## <a name="service-to-service-access-token-response"></a>Služba odpovědi tokenu přístupu služby
Úspěšná odpověď je odpověď JSON OAuth 2.0 s následujícími parametry.

| Parametr | Popis |
| --- | --- |
| token_type |Určuje hodnotu typ tokenu. Pouze typ, který podporuje Azure AD je **nosiče**. Další informace o nosné tokeny, najdete v článku [Framework autorizace OAuth 2.0: použití tokenů nosiče (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Rozsah udělení v tokenu přístupu. |
| expires_in |Doba přístupový token je platný (v sekundách). |
| expires_on |Čas, kdy vyprší platnost přístupového tokenu. Datum je reprezentován jako počet sekund od pod hodnotou 1970-01-01T0:0:0Z UTC až do okamžiku vypršení platnosti. Tato hodnota se používá k určení doby platnosti tokenů v mezipaměti. |
| prostředek |Identifikátor ID URI aplikace služby přijímající (zabezpečené prostředků). |
| access_token |Požadovaný přístupový token. Volání služby můžete použít tento token k ověření přijímající služby. |
| id_token |Požadovaný id token. Volání služby můžete použít k ověření identity uživatele a zahájit relaci s uživatelem. |
| refresh_token |Token obnovení pro požadovaný přístupový token. Volání služby můžete tento token vyžádat dalšího přístupového tokenu po vypršení platnosti aktuální přístupový token. |

### <a name="success-response-example"></a>Příklad úspěšné odpovědi
Následující příklad ukazuje tokenu pro úspěšná odpověď na žádost o přístup https://graph.windows.net webové rozhraní API.

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

### <a name="error-response-example"></a>Příklad chybové odpovědi
Koncový bod tokenu Azure AD vrátí odpovědi na chybu při pokusu o získání přístupového tokenu pro rozhraní API pro příjem dat, pokud rozhraní API pro příjem dat má zásady podmíněného přístupu, jako je vícefaktorové ověřování u něho nastavený. Střední vrstvy služby by měl surface tato chyba do klientské aplikace, tak, aby klientská aplikace může poskytnout zásahu uživatele, aby pokryl zásady podmíněného přístupu.

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

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Použití tokenu přístupu pro přístup k zabezpečeným prostředku
Nyní střední vrstvy služby pomocí tokenu získali výše provádět požadavky na ověření do podřízené webové rozhraní API, nastavením token v `Authorization` záhlaví.

### <a name="example"></a>Příklad:
```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```
## <a name="client-limitations"></a>Omezení klienta
Veřejné klientů se zástupnými znaky adresy URL odpovědí nelze použít `id_token` pro OBO toky. Důvěrné klienta však lze uplatnit stále přístupové tokeny získaných z toku implicitní grant i v případě, že veřejný klient má registrována identifikátor URI přesměrování zástupný znak.

## <a name="next-steps"></a>Další postup
Další informace o protokolu OAuth 2.0 a jiný způsob, jak provádět ověřování služeb pomocí pověření klienta.
* [Služba ověřování služby pomocí udělení pověření klienta OAuth 2.0 ve službě Azure AD](active-directory-protocols-oauth-service-to-service.md)
* [OAuth 2.0 ve službě Azure AD](active-directory-protocols-oauth-code.md)

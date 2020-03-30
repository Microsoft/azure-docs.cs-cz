---
title: Ověřování mezi službami pomocí oauth2.0 jménem toku | Dokumenty společnosti Microsoft
description: Tento článek popisuje použití zpráv HTTP k implementaci ověřování služby služby s tokem OAuth2.0 On-Behalf-Of.
services: active-directory
documentationcenter: .net
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: hirsin, nacanuma
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: a301029f30a77f4e62ad3529aac488a81c12566e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154521"
---
# <a name="service-to-service-calls-that-use-delegated-user-identity-in-the-on-behalf-of-flow"></a>Volání služby, která používají identitu delegovaného uživatele v toku on-behalf-of

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Tok OAuth 2.0 On-Behalf-Of (OBO) umožňuje aplikaci, která vyvolá službu nebo webové rozhraní API, předat ověření uživatele jiné službě nebo webovému rozhraní API. Tok obo šíří delegovanou identitu uživatele a oprávnění prostřednictvím řetězce požadavků. Aby služba střední vrstvy podávala ověřené požadavky na příjem dat, musí jménem uživatele zabezpečit přístupový token ze služby Azure Active Directory (Azure AD).

> [!IMPORTANT]
> Od května 2018 `id_token` nelze použít pro tok jménem.  Jednostránkové aplikace (SPA) musí předat přístupový token střednímu důvěrnému klientovi k provádění toků OBO. Další podrobnosti o klientech, kteří mohou provádět volání jménem, naleznete v [tématu omezení](#client-limitations).

## <a name="on-behalf-of-flow-diagram"></a>Vývojový diagram podle jeho jménem

Tok OBO se spustí po ověření uživatele v aplikaci, která používá [tok udělení autorizačního kódu OAuth 2.0](v1-protocols-oauth-code.md). V tomto okamžiku aplikace odešle přístupový token (token A) do webového rozhraní API střední vrstvy (API A), které obsahuje deklarace identity uživatele a souhlas s přístupem k rozhraní API A. Dále rozhraní API A provede ověřený požadavek na rozhraní API pro příjem dat (ROZHRANÍ B).

Tyto kroky představují tok on-behalf-of: ![Zobrazuje kroky v OAuth2.0 On-Behalf-Of toku](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)

1. Klientská aplikace provede požadavek na rozhraní API A s tokenem A.
1. ROZHRANÍ API A se ověřuje do koncového bodu vystavování tokenů Azure AD a požaduje token pro přístup k rozhraní API B.
1. Koncový bod vystavování tokenu Azure A ověřuje přihlašovací údaje rozhraní API A pomocí tokenu A a vydává přístupový token pro rozhraní API B (token B).
1. Požadavek na rozhraní API B obsahuje token B v hlavičce autorizace.
1. Rozhraní API B vrací data ze zabezpečeného prostředku.

>[!NOTE]
>Deklarace cílové skupiny v přístupovém tokenu, který se používá k vyžádání tokenu pro službu navazujícího vysílání, musí být ID služby, která provádí požadavek obo. Token musí být také podepsaný pomocí globálního podpisového klíče služby Azure Active Directory (což je výchozí hodnota pro aplikace registrované prostřednictvím **registrací aplikací** na portálu).

## <a name="register-the-application-and-service-in-azure-ad"></a>Registrace aplikace a služby ve službě Azure AD

Zaregistrujte službu střední vrstvy i klientskou aplikaci ve službě Azure AD.

### <a name="register-the-middle-tier-service"></a>Registrace služby střední vrstvy

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Na horním panelu vyberte svůj účet a v seznamu **Adresářvyberte** klienta služby Active Directory pro vaši aplikaci.
1. V levém podokně vyberte **Další služby** a zvolte **Azure Active Directory**.
1. Vyberte **Registrace aplikací** a potom **Nová registrace**.
1. Zadejte popisný název aplikace a vyberte typ aplikace.
1. V části **Podporované typy účtů**vyberte Účty ve všech **organizačních adresářích a osobních účtech Microsoft**.
1. Nastavte identifikátor URI přesměrování na základní adresu URL.
1. Výběrem možnosti **Registrovat** aplikaci vytvořte.
1. Před ukončením portálu Azure vygenerujte tajný klíč klienta.
1. Na webu Azure Portal zvolte aplikaci a vyberte **certifikáty & tajných kódů**.
1. Vyberte **Nový tajný klíč klienta** a přidejte tajný klíč s dobou trvání jeden rok nebo dva roky.
1. Když uložíte tuto stránku, portál Azure zobrazí tajnou hodnotu. Zkopírujte a uložte tajnou hodnotu na bezpečném místě.

> [!IMPORTANT]
> Tajný klíč potřebujete ke konfiguraci nastavení aplikace v implementaci. Tato tajná hodnota není znovu zobrazena a nelze ji získat žádným jiným způsobem. Zaznamenejte to, jakmile je viditelná na webu Azure Portal.

### <a name="register-the-client-application"></a>Registrace klientské aplikace

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Na horním panelu vyberte svůj účet a v seznamu **Adresářvyberte** klienta služby Active Directory pro vaši aplikaci.
1. V levém podokně vyberte **Další služby** a zvolte **Azure Active Directory**.
1. Vyberte **Registrace aplikací** a potom **Nová registrace**.
1. Zadejte popisný název aplikace a vyberte typ aplikace.
1. V části **Podporované typy účtů**vyberte Účty ve všech **organizačních adresářích a osobních účtech Microsoft**.
1. Nastavte identifikátor URI přesměrování na základní adresu URL.
1. Výběrem možnosti **Registrovat** aplikaci vytvořte.
1. Nakonfigurujte oprávnění pro vaši aplikaci. V **části Oprávnění rozhraní API**vyberte Přidat **oprávnění** a potom moje **rozhraní API**.
1. Do textového pole zadejte název služby střední vrstvy.
1. Zvolte **Vybrat oprávnění** a pak vyberte **přístup k \<názvu služby>**.

### <a name="configure-known-client-applications"></a>Konfigurace známých klientských aplikací

V tomto scénáři služba střední vrstvy potřebuje získat souhlas uživatele pro přístup k rozhraní API pro příjem dat bez interakce s uživatelem. Možnost udělit přístup k rozhraní API pro příjem dat musí být během ověřování uvedena předem jako součást kroku souhlasu.

Podle následujících kroků explicitně svázat registraci klientské aplikace ve službě Azure AD s registrací služby střední vrstvy. Tato operace sloučí souhlas vyžadované klientem a střední vrstvy do jednoho dialogu.

1. Přejděte na registraci služby střední vrstvy a vyberte **Manifest,** chcete-li otevřít editor manifestu.
1. Vyhledejte `knownClientApplications` vlastnost pole a přidejte ID klienta klientské aplikace jako prvek.
1. Uložte manifest výběrem **možnosti Uložit**.

## <a name="service-to-service-access-token-request"></a>Požadavek na přístupový token služby

Chcete-li požádat o přístupový token, vytvořte HTTP POST do koncového bodu Azure AD specifického pro klienta s následujícími parametry:

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```

Klientská aplikace je zabezpečena buď sdíleným tajným tajemstvím, nebo certifikátem.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>První případ: Žádost o přístupový token se sdíleným tajným tajemstvím

Při použití sdíleného tajného klíče obsahuje požadavek přístupového tokenu služby ke službě následující parametry:

| Parametr |  | Popis |
| --- | --- | --- |
| grant_type |Požadovaný | Typ požadavku tokenu. Požadavek OBO používá webový token JSON (JWT), takže hodnota musí být **urn:ietf:params:oauth:grant-type:jwt-bearer**. |
| Tvrzení |Požadovaný | Hodnota přístupového tokenu použitého v požadavku. |
| client_id |Požadovaný | ID aplikace přiřazené službě volání během registrace ve službě Azure AD. Pokud chcete Najít ID aplikace na webu Azure Portal, vyberte **Službu Active Directory**, vyberte adresář a pak vyberte název aplikace. |
| client_secret |Požadovaný | Klíč registrovaný pro službu volání ve službě Azure AD. Tato hodnota měla být zaznamenána v době registrace. |
| prostředek |Požadovaný | Identifikátor URI ID aplikace přijímající služby (zabezpečený prostředek). Pokud chcete najít identifikátor URI aplikace na webu Azure Portal, vyberte **Službu Active Directory** a zvolte adresář. Vyberte název aplikace, zvolte **Všechna nastavení**a pak vyberte **Vlastnosti**. |
| requested_token_use |Požadovaný | Určuje způsob zpracování požadavku. V toku on-behalf-of hodnota musí být **on_behalf_of**. |
| scope |Požadovaný | Mezera oddělený seznam oborů pro požadavek tokenu. Pro OpenID Connect musí být zadán **obor openid.**|

#### <a name="example"></a>Příklad

Následující funkce HTTP POST požaduje přístupový token pro https://graph.microsoft.com webové rozhraní API. Identifikuje `client_id` službu, která požaduje přístupový token.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_secret=0Y1W%2BY3yYb3d9N8vSjvm8WrGzVZaAaHbHHcGbcgG%2BoI%3D
&resource=https%3A%2F%2Fgraph.microsoft.com
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.ewogICJhdWQiOiAiaHR0cHM6Ly9ncmFwaC5taWNyb3NvZnQuY29tIiwKICAiaXNzIjogImh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLAogICJpYXQiOiAxNDkzNDIzMTY4LAogICJuYmYiOiAxNDkzNDIzMTY4LAogICJleHAiOiAxNDkzNDY2OTUxLAogICJhY3IiOiAiMSIsCiAgImFpbyI6ICJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsCiAgImFtciI6IFsKICAgICJwd2QiCiAgXSwKICAiYXBwaWQiOiAiNjI1MzkxYWYtYzY3NS00M2U1LThlNDQtZWRkM2UzMGNlYjE1IiwKICAiYXBwaWRhY3IiOiAiMSIsCiAgImVfZXhwIjogMzAyNjgzLAogICJmYW1pbHlfbmFtZSI6ICJUZXN0IiwKICAiZ2l2ZW5fbmFtZSI6ICJOYXZ5YSIsCiAgImlwYWRkciI6ICIxNjcuMjIwLjEuMTc3IiwKICAibmFtZSI6ICJOYXZ5YSBUZXN0IiwKICAib2lkIjogIjFjZDRiY2FjLWI4MDgtNDIzYS05ZTJmLTgyN2ZiYjFiYjczOSIsCiAgInBsYXRmIjogIjMiLAogICJwdWlkIjogIjEwMDMzRkZGQTEyRUQ3RkUiLAogICJzY3AiOiAiVXNlci5SZWFkIiwKICAic3ViIjogIjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLAogICJ0aWQiOiAiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwKICAidW5pcXVlX25hbWUiOiAibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLAogICJ1cG4iOiAibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLAogICJ1dGkiOiAieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsCiAgInZlciI6ICIxLjAiCn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
&requested_token_use=on_behalf_of
&scope=openid
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Druhý případ: Žádost o přístupový token s certifikátem

Požadavek na přístupový token služby k službě s certifikátem obsahuje následující parametry:

| Parametr |  | Popis |
| --- | --- | --- |
| grant_type |Požadovaný | Typ požadavku tokenu. Požadavek OBO používá přístupový token JWT, takže hodnota musí být **urn:ietf:params:oauth:grant-type:jwt-bearer**. |
| Tvrzení |Požadovaný | Hodnota tokenu použitého v požadavku. |
| client_id |Požadovaný | ID aplikace přiřazené službě volání během registrace ve službě Azure AD. Pokud chcete Najít ID aplikace na webu Azure Portal, vyberte **Službu Active Directory**, vyberte adresář a pak vyberte název aplikace. |
| client_assertion_type |Požadovaný |Hodnota musí být`urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |Požadovaný | Webový token JSON, který vytvoříte a podepíšete pomocí certifikátu, který jste zaregistrovali jako pověření pro vaši aplikaci. Informace o formátu kontrolního výrazu a registraci certifikátu najdete v [tématu pověření certifikátu.](../develop/active-directory-certificate-credentials.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)|
| prostředek |Požadovaný | Identifikátor URI ID aplikace přijímající služby (zabezpečený prostředek). Pokud chcete najít identifikátor URI aplikace na webu Azure Portal, vyberte **Službu Active Directory** a zvolte adresář. Vyberte název aplikace, zvolte **Všechna nastavení**a pak vyberte **Vlastnosti**. |
| requested_token_use |Požadovaný | Určuje způsob zpracování požadavku. V toku on-behalf-of hodnota musí být **on_behalf_of**. |
| scope |Požadovaný | Mezera oddělený seznam oborů pro požadavek tokenu. Pro OpenID Connect musí být zadán **obor openid.**|

Tyto parametry jsou téměř stejné jako u požadavku `client_secret parameter` sdíleným tajným tajemstvím `client_assertion_type` `client_assertion`s tím rozdílem, že je nahrazen dvěma parametry: a .

#### <a name="example"></a>Příklad

Následující funkce HTTP POST požaduje přístupový token pro https://graph.microsoft.com webové rozhraní API s certifikátem. Identifikuje `client_id` službu, která požaduje přístupový token.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&resource=https%3A%2F%2Fgraph.microsoft.com
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

## <a name="service-to-service-access-token-response"></a>Odpověď tokenu přístupu služby ke službě

Úspěšnou odpovědí je odpověď JSON OAuth 2.0 s následujícími parametry:

| Parametr | Popis |
| --- | --- |
| token_type |Označuje hodnotu typu tokenu. Jediný typ, který podporuje Azure AD je **Nosič**. Další informace o žetonech na doručitele naleznete v [rámci autorizace OAuth 2.0: Použití žetonu nosiče (RFC 6750).](https://www.rfc-editor.org/rfc/rfc6750.txt) |
| scope |Rozsah přístupu uděleného v tokenu. |
| expires_in |Doba platnosti přístupového tokenu (v sekundách). |
| expires_on |Čas, kdy vyprší platnost přístupového tokenu. Datum je reprezentováno jako počet sekund od 1970-01-01T0:0:0Z UTC až do doby vypršení platnosti. Tato hodnota se používá k určení životnosti tokenů uložených v mezipaměti. |
| prostředek |Identifikátor URI ID aplikace přijímající služby (zabezpečený prostředek). |
| access_token |Požadovaný přístupový token. Volající služba můžete použít tento token k ověření přijímající služby. |
| id_token |Požadovaný token ID. Volající služba můžete použít tento token k ověření identity uživatele a zahájit relaci s uživatelem. |
| refresh_token |Obnovovací token pro požadovaný přístupový token. Volající služba můžete použít tento token požádat o jiný přístupový token po vypršení platnosti aktuálního přístupového tokenu. |

### <a name="success-response-example"></a>Příklad odpovědi na úspěch

Následující příklad ukazuje odpověď na úspěch na žádost https://graph.microsoft.com o přístupový token pro webové rozhraní API.

```json
{
    "token_type":"Bearer",
    "scope":"User.Read",
    "expires_in":"43482",
    "ext_expires_in":"302683",
    "expires_on":"1493466951",
    "not_before":"1493423168",
    "resource":"https://graph.microsoft.com",
    "access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ",
    "refresh_token":"AQABAAAAAABnfiG-mA6NTae7CdWW7QfdjKGu9-t1scy_TDEmLi4eLQMjJGt_nAoVu6A4oSu1KsRiz8XyQIPKQxSGfbf2FoSK-hm2K8TYzbJuswYusQpJaHUQnSqEvdaCeFuqXHBv84wjFhuanzF9dQZB_Ng5za9xKlUENrNtlq9XuLNVKzxEyeUM7JyxzdY7JiEphWImwgOYf6II316d0Z6-H3oYsFezf4Xsjz-MOBYEov0P64UaB5nJMvDyApV-NWpgklLASfNoSPGb67Bc02aFRZrm4kLk-xTl6eKE6hSo0XU2z2t70stFJDxvNQobnvNHrAmBaHWPAcC3FGwFnBOojpZB2tzG1gLEbmdROVDp8kHEYAwnRK947Py12fJNKExUdN0njmXrKxNZ_fEM33LHW1Tf4kMX_GvNmbWHtBnIyG0w5emb-b54ef5AwV5_tGUeivTCCysgucEc-S7G8Cz0xNJ_BOiM_4bAv9iFmrm9STkltpz0-Tftg8WKmaJiC0xXj6uTf4ZkX79mJJIuuM7XP4ARIcLpkktyg2Iym9jcZqymRkGH2Rm9sxBwC4eeZXM7M5a7TJ-5CqOdfuE3sBPq40RdEWMFLcrAzFvP0VDR8NKHIrPR1AcUruat9DETmTNJukdlJN3O41nWdZOVoJM-uKN3uz2wQ2Ld1z0Mb9_6YfMox9KTJNzRzcL52r4V_y3kB6ekaOZ9wQ3HxGBQ4zFt-2U0mSszIAA",
    "id_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC8yNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IvIiwiaWF0IjoxNDkzNDIzMTY4LCJuYmYiOjE0OTM0MjMxNjgsImV4cCI6MTQ5MzQ2Njk1MSwiYW1yIjpbInB3ZCJdLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXRpIjoieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsInZlciI6IjEuMCJ9."
}
```

### <a name="error-response-example"></a>Příklad odpovědi na chybu

Koncový bod tokenu Azure AD vrátí odpověď na chybu při pokusu o získání přístupového tokenu pro rozhraní API pro příjem dat, který je nastaven pomocí zásady podmíněného přístupu (například vícefaktorové ověřování). Služba střední vrstvy by měla tuto chybu předložit klientské aplikaci, aby klientská aplikace mohla poskytnout interakci s uživatelem, aby splňovala zásady podmíněného přístupu.

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

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Použití přístupového tokenu pro přístup k zabezpečenému prostředku

Služba střední vrstvy můžete použít získaný přístupový token k vytváření ověřených `Authorization` požadavků na rozhraní API pro příjem sítě nastavením tokenu v záhlaví.

### <a name="example"></a>Příklad

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>Tvrzení SAML získaná s tokem OAuth2.0 OBO

Některé webové služby založené na OAuth potřebují přístup k jiným webovým službám API, které přijímají kontrolní výrazy SAML v neinteraktivních tocích. Azure Active Directory může poskytnout kontrolní výraz SAML v reakci na tok na účet, který používá webovou službu založenou na SAML jako cílový prostředek.

>[!NOTE]
>Toto je nestandardní rozšíření o OAuth 2.0 On-Behalf-Of toku, který umožňuje aplikace založené na OAuth2 pro přístup ke koncovým bodům rozhraní API webové služby, které spotřebovávají tokeny SAML.

> [!TIP]
> Při volání webové služby chráněné SAML z front-endwebové aplikace, můžete jednoduše volat rozhraní API a zahájit normální interaktivní ověřování toku s existující relace uživatele. Tok objektu OBO je třeba použít pouze v případě, že volání služby ke službě vyžaduje token SAML k poskytnutí kontextu uživatele.

### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>Získání tokenu SAML pomocí požadavku OBO se sdíleným tajným tajemstvím

Požadavek služby na službu pro kontrolní výraz SAML obsahuje následující parametry:

| Parametr |  | Popis |
| --- | --- | --- |
| grant_type |Požadovaný | Typ požadavku tokenu. Pro požadavek, který používá JWT, musí být hodnota **urn:ietf:params:oauth:grant-type:jwt-bearer**. |
| Tvrzení |Požadovaný | Hodnota přístupového tokenu použitého v požadavku.|
| client_id |Požadovaný | ID aplikace přiřazené službě volání během registrace ve službě Azure AD. Pokud chcete Najít ID aplikace na webu Azure Portal, vyberte **Službu Active Directory**, vyberte adresář a pak vyberte název aplikace. |
| client_secret |Požadovaný | Klíč registrovaný pro službu volání ve službě Azure AD. Tato hodnota měla být zaznamenána v době registrace. |
| prostředek |Požadovaný | Identifikátor URI ID aplikace přijímající služby (zabezpečený prostředek). Toto je prostředek, který bude cílovou skupinou tokenu SAML. Pokud chcete najít identifikátor URI aplikace na webu Azure Portal, vyberte **Službu Active Directory** a zvolte adresář. Vyberte název aplikace, zvolte **Všechna nastavení**a pak vyberte **Vlastnosti**. |
| requested_token_use |Požadovaný | Určuje způsob zpracování požadavku. V toku on-behalf-of hodnota musí být **on_behalf_of**. |
| requested_token_type | Požadovaný | Určuje typ požadovaného tokenu. Hodnota může být **urn:ietf:params:oauth:token-type:saml2** nebo **urn:ietf:params:oauth:token-type:saml1** v závislosti na požadavcích přístupného prostředku. |

Odpověď obsahuje token SAML kódovaný v UTF8 a Base64url.

- **SubjectConfirmationData pro kontrolní výraz SAML pocházející z volání OBO**: Pokud cílová aplikace vyžaduje hodnotu příjemce v **SubjectConfirmationData**, musí být hodnota v konfiguraci aplikace prostředků adresa URL bez zástupné odpovědi.
- **Uzel SubjectConfirmationData**: Uzel nemůže obsahovat atribut **InResponseTo,** protože není součástí odpovědi SAML. Aplikace přijímající token SAML musí být schopna přijmout kontrolní výraz SAML bez atributu **InResponseTo.**

- **Souhlas**: Souhlas musí být udělen pro příjem tokenu SAML obsahujícího uživatelská data na toku OAuth. Informace o oprávněních a získání souhlasu správce najdete [v tématu Oprávnění a souhlas v koncovém bodě Služby Azure Active Directory v1.0](https://docs.microsoft.com/azure/active-directory/azuread-dev/v1-permissions-consent).

### <a name="response-with-saml-assertion"></a>Odpověď s kontrolním výrazem SAML

| Parametr | Popis |
| --- | --- |
| token_type |Označuje hodnotu typu tokenu. Jediný typ, který podporuje Azure AD je **Nosič**. Další informace o žetonech nosičů naleznete [v tématu OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Rozsah přístupu uděleného v tokenu. |
| expires_in |Doba platnosti přístupového tokenu (v sekundách). |
| expires_on |Čas, kdy vyprší platnost přístupového tokenu. Datum je reprezentováno jako počet sekund od 1970-01-01T0:0:0Z UTC až do doby vypršení platnosti. Tato hodnota se používá k určení životnosti tokenů uložených v mezipaměti. |
| prostředek |Identifikátor URI ID aplikace přijímající služby (zabezpečený prostředek). |
| access_token |Parametr, který vrací kontrolní výraz SAML. |
| refresh_token |Obnovovací token. Volající služba můžete použít tento token požádat o jiný přístupový token po vypršení platnosti aktuální saml kontrolní výraz. |

- token_type: Nosič
- expires_in: 3296
- ext_expires_in: 0
- expires_on: 1529627844
- Zdrojů:`https://api.contoso.com`
- access_token: \<Výraz SAML\>
- issued_token_type: urn:ietf:params:oauth:token-type:saml2
- refresh_token: \<Obnovovat token\>

## <a name="client-limitations"></a>Omezení klientů

Veřejní klienti s adresami URL zástupných `id_token` symbolů nelze použít pro toky OBO. Důvěrný klient však může stále uplatnit **přístupové** tokeny získané prostřednictvím implicitního toku grantů i v případě, že veřejný klient má identifikátor URI přesměrování se zástupnými kódy se zástupnými kódy.

## <a name="next-steps"></a>Další kroky

Další informace o protokolu OAuth 2.0 a další způsob, jak provádět ověřování služby, které používá pověření klienta:

* [Služba pro ověřování služeb pomocí udělení pověření klienta OAuth 2.0 ve službě Azure AD](v1-oauth2-client-creds-grant-flow.md)
* [OAuth 2.0 ve službě Azure AD](v1-protocols-oauth-code.md)

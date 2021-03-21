---
title: Ověřování služba-služba s protokolem OAuth 2.0 za běhu | Microsoft Docs
description: Tento článek popisuje, jak pomocí zpráv HTTP implementovat ověřování typu služba-služba s protokolem OAuth 2.0 za běhu.
services: active-directory
documentationcenter: .net
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 08/5/2020
ms.author: ryanwi
ms.reviewer: hirsin, nacanuma
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: f746cc654934464d907c6ad669eb7470e4dcaeeb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88117732"
---
# <a name="service-to-service-calls-that-use-delegated-user-identity-in-the-on-behalf-of-flow"></a>Volání služeb pro službu, která používají delegovanou identitu uživatele v toku za jménem

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Tok OBO (OAuth) na účet OAuth 2,0 umožňuje aplikaci, která vyvolá službu nebo webové rozhraní API, aby bylo možné předat ověření uživatele pro jinou službu nebo webové rozhraní API. Tok OBO šíří identitu delegovaného uživatele a oprávnění prostřednictvím řetězce požadavků. Aby služba střední vrstvy prováděla ověřené požadavky na službu pro příjem dat, musí jménem uživatele zabezpečit přístupový token z Azure Active Directory (Azure AD).

> [!IMPORTANT]
> Od května 2018 se `id_token` nedá použít pro tok za běhu.  Jednostránkové aplikace (jednostránkové) musí předat přístup k tajnému klientovi střední vrstvy za účelem provádění toků OBO. Další podrobnosti o klientech, které mohou provádět na základě hovorů, najdete v tématu [omezení](#client-limitations).

## <a name="on-behalf-of-flow-diagram"></a>Diagram toku za běhu

Tok OBO se spustí po ověření uživatele v aplikaci, která používá [tok udělení autorizačního kódu OAuth 2,0](v1-protocols-oauth-code.md). V tomto okamžiku aplikace pošle přístupový token (token A) do webového rozhraní API (API a) střední vrstvy, které obsahuje deklarace identity uživatele a souhlas s přístupem k rozhraní API a. V dalším kroku rozhraní API A provede ověřený požadavek webového rozhraní API pro příjem dat (API B).

Tyto kroky tvoří Průběh toku: ![ zobrazuje kroky v toku OAuth 2.0 za vás.](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)

1. Klientská aplikace vytvoří požadavek na rozhraní API A s tokenem A.
1. Rozhraní API A ověřuje koncový bod vystavování tokenu Azure AD a požaduje token pro přístup k rozhraní API B.
1. Koncový bod vystavení tokenu Azure AD ověří přihlašovací údaje A přihlašovací údaje k rozhraní API s tokenem a a vydá přístupový token pro rozhraní API B (token B).
1. Požadavek na rozhraní API B obsahuje token B v autorizační hlavičce.
1. Rozhraní API B vrátí data z zabezpečeného prostředku.

>[!NOTE]
>Deklarace identity cílové skupiny v přístupovém tokenu, který se používá k vyžádání tokenu pro službu pro příjem dat, musí být ID služby, která vyžaduje požadavek OBO. Token musí být také podepsán pomocí Azure Active Directory globálního podpisového klíče (což je výchozí nastavení pro aplikace zaregistrované prostřednictvím **Registrace aplikací** na portálu).

## <a name="register-the-application-and-service-in-azure-ad"></a>Registrace aplikace a služby ve službě Azure AD

Zaregistrujte jak službu střední vrstvy, tak klientskou aplikaci ve službě Azure AD.

### <a name="register-the-middle-tier-service"></a>Registrace služby střední vrstvy

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V horním panelu vyberte svůj účet a v seznamu **adresář** vyberte klienta Active Directory pro vaši aplikaci.
1. V levém podokně vyberte **Další služby** a zvolte **Azure Active Directory**.
1. Vyberte **Registrace aplikací** a pak **novou registraci**.
1. Zadejte popisný název aplikace a vyberte typ aplikace.
1. V části **podporované typy účtů** vyberte **účty v libovolném organizačním adresáři a osobní účty Microsoft**.
1. Nastavte identifikátor URI přesměrování na základní adresu URL.
1. Výběrem možnosti **Registrovat** aplikaci vytvořte.
1. V Azure Portal zvolte aplikaci a vyberte **certifikáty & tajné klíče**.
1. Vyberte **nový tajný klíč klienta** a přidejte tajný klíč s dobou trvání jeden rok nebo dva roky.
1. Když tuto stránku uložíte, Azure Portal zobrazí tajnou hodnotu. Zkopírujte a uložte tajnou hodnotu v bezpečném umístění.
1. Vytvořte v aplikaci obor na stránce **vystavení rozhraní API** pro vaši aplikaci a klikněte na Přidat obor.  Portál může vyžadovat, abyste vytvořili taky identifikátor URI ID aplikace. 

> [!IMPORTANT]
> Ke konfiguraci nastavení aplikace v implementaci potřebujete tajný klíč. Tato hodnota tajného klíče se znovu nezobrazí a nedá se získat jakýmkoli jiným způsobem. Nahrajte ho hned tak, jak je vidět v Azure Portal.

### <a name="register-the-client-application"></a>Registrace klientské aplikace

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V horním panelu vyberte svůj účet a v seznamu **adresář** vyberte klienta Active Directory pro vaši aplikaci.
1. V levém podokně vyberte **Další služby** a zvolte **Azure Active Directory**.
1. Vyberte **Registrace aplikací** a pak **novou registraci**.
1. Zadejte popisný název aplikace a vyberte typ aplikace.
1. V části **podporované typy účtů** vyberte **účty v libovolném organizačním adresáři a osobní účty Microsoft**.
1. Nastavte identifikátor URI přesměrování na základní adresu URL.
1. Výběrem možnosti **Registrovat** aplikaci vytvořte.
1. Nakonfigurujte oprávnění pro vaši aplikaci. V okně **oprávnění rozhraní API** vyberte **Přidat oprávnění** a pak **Moje rozhraní API**.
1. Do textového pole zadejte název služby střední vrstvy.
1. Zvolte **vybrat oprávnění** a pak vyberte rozsah, který jste vytvořili v posledním kroku registrace střední vrstvy.

### <a name="configure-known-client-applications"></a>Konfigurovat známé klientské aplikace

V tomto scénáři potřebuje služba střední vrstvy získat souhlas uživatele s přístupem k rozhraní API pro příjem dat bez zásahu uživatele. Možnost udělení přístupu k rozhraní API pro příjem dat se musí předkládat jako součást kroku souhlasu během ověřování.

Podle následujících pokynů explicitně navažte registraci klientské aplikace v Azure AD pomocí registrace služby střední vrstvy. Tato operace sloučí souhlas vyžadovaný oběma klienty a střední vrstvou do jediného dialogového okna.

1. Chcete-li otevřít editor manifestu, klikněte na položku registrace služby střední vrstvy a vyberte možnost **manifest** .
1. Vyhledejte `knownClientApplications` vlastnost Array a jako prvek přidejte ID klienta klientské aplikace.
1. Uložte manifest výběrem možnosti **Uložit**.

## <a name="service-to-service-access-token-request"></a>Žádost o přístupový token služby na službu

Pokud chcete požádat o přístupový token, vytvořte příspěvek HTTP do koncového bodu Azure AD specifického pro tenanta s následujícími parametry:

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```

Klientská aplikace je zabezpečená buď sdíleným tajným klíčem, nebo certifikátem.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>První případ: žádost přístupového tokenu se sdíleným tajným klíčem

Při použití sdíleného tajného klíče obsahuje požadavek na přístupový token služby na službu následující parametry:

| Parametr | Typ | Description |
| --- | --- | --- |
| grant_type |vyžadováno | Typ požadavku tokenu Požadavek OBO používá JSON Web Token (JWT), takže hodnota musí být **urn: IETF: params: OAuth: Grant-Type: JWT-nosič**. |
| Neplatný |vyžadováno | Hodnota přístupového tokenu použitého v žádosti |
| client_id |vyžadováno | ID aplikace přiřazené volající službě během registrace ve službě Azure AD. Chcete-li najít ID aplikace v Azure Portal, vyberte možnost **Active Directory**, zvolte adresář a pak vyberte název aplikace. |
| client_secret |vyžadováno | Klíč zaregistrovaný pro volající službu ve službě Azure AD. Tato hodnota by se měla poznamenat v době registrace. |
| prostředek |vyžadováno | Identifikátor URI ID aplikace přijímající služby (zabezpečeného prostředku) Identifikátor URI ID aplikace v Azure Portal najdete tak, že vyberete **Active Directory** a zvolíte adresář. Vyberte název aplikace, zvolte **všechna nastavení** a pak vyberte **vlastnosti**. |
| requested_token_use |vyžadováno | Určuje, jak se má požadavek zpracovat. V toku musí být hodnota **on_behalf_of**. |
| scope |vyžadováno | Mezerou oddělený seznam oborů pro požadavek na token. Pro OpenID Connect se musí zadat obor **OpenID** .|

#### <a name="example"></a>Příklad

Následující příspěvek HTTP požaduje přístupový token pro https://graph.microsoft.com webové rozhraní API. `client_id`Identifikuje službu, která žádá o přístupový token.

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

### <a name="second-case-access-token-request-with-a-certificate"></a>Druhý případ: žádost o přístupový token s certifikátem

Požadavek na přístupový token služby na službu s certifikátem obsahuje následující parametry:

| Parametr | Typ | Description |
| --- | --- | --- |
| grant_type |vyžadováno | Typ požadavku tokenu Požadavek OBO používá přístupový token JWT, takže hodnota musí být **urn: IETF: params: OAuth: Grant-Type: JWT-nosič**. |
| Neplatný |vyžadováno | Hodnota tokenu použitého v požadavku. |
| client_id |vyžadováno | ID aplikace přiřazené volající službě během registrace ve službě Azure AD. Chcete-li najít ID aplikace v Azure Portal, vyberte možnost **Active Directory**, zvolte adresář a pak vyberte název aplikace. |
| client_assertion_type |vyžadováno |Hodnota musí být `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |vyžadováno | JSON Web Token, kterou vytvoříte a podepíšete pomocí certifikátu, který jste zaregistrovali jako přihlašovací údaje pro vaši aplikaci. Informace o formátu kontrolního výrazu a o tom, jak svůj certifikát zaregistrovat, najdete v tématu  [přihlašovací údaje k certifikátu](../develop/active-directory-certificate-credentials.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) .|
| prostředek |vyžadováno | Identifikátor URI ID aplikace přijímající služby (zabezpečeného prostředku) Identifikátor URI ID aplikace v Azure Portal najdete tak, že vyberete **Active Directory** a zvolíte adresář. Vyberte název aplikace, zvolte **všechna nastavení** a pak vyberte **vlastnosti**. |
| requested_token_use |vyžadováno | Určuje, jak se má požadavek zpracovat. V toku musí být hodnota **on_behalf_of**. |
| scope |vyžadováno | Mezerou oddělený seznam oborů pro požadavek na token. Pro OpenID Connect se musí zadat obor **OpenID** .|

Tyto parametry jsou skoro stejné jako u žádosti pomocí sdíleného tajného klíče s tím rozdílem, že `client_secret parameter` je nahrazen dvěma parametry: `client_assertion_type` a `client_assertion` .

#### <a name="example"></a>Příklad

Následující příspěvek HTTP požaduje přístupový token pro https://graph.microsoft.com webové rozhraní API s certifikátem. `client_id`Identifikuje službu, která žádá o přístupový token.

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

## <a name="service-to-service-access-token-response"></a>Odpověď na přístupový token služby na službu

Odpověď na úspěch je odpověď protokolu JSON OAuth 2,0 s následujícími parametry:

| Parametr | Popis |
| --- | --- |
| token_type |Určuje hodnotu typu tokenu. Jediným typem, který podporuje Azure AD, je **nosič**. Další informace o nosných tokenech najdete v části [autorizační rozhraní OAuth 2,0: použití nosných tokenů (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Rozsah přístupu uděleného v tokenu. |
| expires_in |Délka doby platnosti přístupového tokenu (v sekundách). |
| expires_on |Čas vypršení platnosti přístupového tokenu. Datum se reprezentuje jako počet sekund od roku 1970-01-01T0:0: 0Z UTC až do doby vypršení platnosti. Tato hodnota se používá k určení doby života tokenů uložených v mezipaměti. |
| prostředek |Identifikátor URI ID aplikace přijímající služby (zabezpečeného prostředku) |
| access_token |Požadovaný přístupový token Volající služba může tento token použít k ověření pro přijímající službu. |
| id_token |Požadovaný token ID. Volající služba může pomocí tohoto tokenu ověřit identitu uživatele a zahájit relaci s uživatelem. |
| refresh_token |Obnovovací token požadovaného přístupového tokenu. Volající služba může tento token použít k vyžádání jiného přístupového tokenu po vypršení platnosti aktuálního přístupového tokenu. |

### <a name="success-response-example"></a>Příklad odpovědi na úspěch

Následující příklad ukazuje odpověď na úspěch na žádost o přístupový token pro https://graph.microsoft.com webové rozhraní API.

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

### <a name="error-response-example"></a>Příklad chybové odpovědi

Koncový bod tokenu Azure AD vrátí chybovou odpověď, když se pokusí získat přístupový token pro rozhraní API pro příjem dat, které je nastavené pomocí zásad podmíněného přístupu (například Multi-Factor Authentication). Služba střední vrstvy by tuto chybu měla obit klientské aplikaci, aby mohla klientská aplikace poskytnout interakci uživatele, aby splnila zásady podmíněného přístupu.

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

Služba střední vrstvy může pomocí získaného přístupového tokenu provádět ověřené požadavky webového rozhraní API pro příjem dat, a to nastavením tokenu v `Authorization` záhlaví.

### <a name="example"></a>Příklad

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>Kontrolní výrazy SAML získané pomocí toku OBO OAuth 2.0

Některé webové služby založené na protokolu OAuth potřebují přístup k dalším rozhraním API webových služeb, která přijímají kontrolní výrazy SAML v neinteraktivních tocích. Azure Active Directory může poskytnout kontrolní výraz SAML v reakci na tok, který používá webovou službu založenou na SAML jako cílový prostředek.

>[!NOTE]
>Toto je nestandardní rozšíření pro tok OAuth 2,0, který umožňuje aplikaci založené na OAuth2 přistupovat k koncovým bodům rozhraní API webové služby, které využívají tokeny SAML.

> [!TIP]
> Když zavoláte webovou službu chráněnou SAML z front-endové webové aplikace, můžete jednoduše zavolat rozhraní API a zahájit normální tok interaktivního ověřování s existující relací uživatele. Tok OBO je potřeba použít jenom v případě, že volání služba-služba vyžaduje, aby token SAML poskytoval kontext uživatele.

### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>Získání tokenu SAML pomocí žádosti OBO se sdíleným tajným klíčem

Požadavek služby na službu pro kontrolní výraz SAML obsahuje následující parametry:

| Parametr | Typ | Description |
| --- | --- | --- |
| grant_type |vyžadováno | Typ požadavku tokenu Pro požadavek, který používá JWT, musí být hodnota **urn: IETF: params: OAuth: Grant-Type: JWT-nosič**. |
| Neplatný |vyžadováno | Hodnota přístupového tokenu použitého v žádosti|
| client_id |vyžadováno | ID aplikace přiřazené volající službě během registrace ve službě Azure AD. Chcete-li najít ID aplikace v Azure Portal, vyberte možnost **Active Directory**, zvolte adresář a pak vyberte název aplikace. |
| client_secret |vyžadováno | Klíč zaregistrovaný pro volající službu ve službě Azure AD. Tato hodnota by se měla poznamenat v době registrace. |
| prostředek |vyžadováno | Identifikátor URI ID aplikace přijímající služby (zabezpečeného prostředku) Toto je prostředek, který bude cílovou skupinou tokenu SAML. Identifikátor URI ID aplikace v Azure Portal najdete tak, že vyberete **Active Directory** a zvolíte adresář. Vyberte název aplikace, zvolte **všechna nastavení** a pak vyberte **vlastnosti**. |
| requested_token_use |vyžadováno | Určuje, jak se má požadavek zpracovat. V toku musí být hodnota **on_behalf_of**. |
| requested_token_type | vyžadováno | Určuje typ požadovaného tokenu. Hodnota může být **urn: IETF: params: OAuth: token-Type: typu Saml2** nebo **urn: IETF: parametr: OAuth: token-Type: saml1** v závislosti na požadavcích na prostředek, který je k dispozici. |

Odpověď obsahuje token SAML kódovaný v UTF8 a Base64url.

- **SubjectConfirmationData pro vyhodnocení výrazu SAML ze volání OBO**: Pokud cílová aplikace vyžaduje hodnotu příjemce v **SubjectConfirmationData**, musí být v konfiguraci aplikace prostředků adresa URL odpovědi bez zástupných znaků.
- **Uzel SubjectConfirmationData**: uzel nemůže obsahovat atribut **InResponseTo** , protože není součástí odpovědi SAML. Aplikace, která přijímá token SAML, musí být schopna přijmout kontrolní výraz SAML bez atributu **InResponseTo** .

- **Souhlas**: souhlas se musí udělit pro příjem tokenu SAML obsahujícího uživatelská data v toku OAuth. Informace o oprávněních a získání souhlasu správce najdete [v tématu oprávnění a souhlas v koncovém bodu Azure Active Directory v 1.0](./v1-permissions-consent.md).

### <a name="response-with-saml-assertion"></a>Odpověď s kontrolním výrazem SAML

| Parametr | Popis |
| --- | --- |
| token_type |Určuje hodnotu typu tokenu. Jediným typem, který podporuje Azure AD, je **nosič**. Další informace o tokenech nosiče najdete v tématu [autorizační rozhraní OAuth 2,0: použití nosných tokenů (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Rozsah přístupu uděleného v tokenu. |
| expires_in |Délka doby platnosti přístupového tokenu (v sekundách). |
| expires_on |Čas vypršení platnosti přístupového tokenu. Datum se reprezentuje jako počet sekund od roku 1970-01-01T0:0: 0Z UTC až do doby vypršení platnosti. Tato hodnota se používá k určení doby života tokenů uložených v mezipaměti. |
| prostředek |Identifikátor URI ID aplikace přijímající služby (zabezpečeného prostředku) |
| access_token |Parametr, který vrací kontrolní výraz SAML. |
| refresh_token |Obnovovací token Volající služba může tento token použít k vyžádání jiného přístupového tokenu po vypršení platnosti aktuálního kontrolního výrazu SAML. |

- token_type: nosič
- expires_in: 3296
- ext_expires_in: 0
- expires_on: 1529627844
- partner `https://api.contoso.com`
- access_token: \<SAML assertion\>
- issued_token_type: urn: IETF: param: OAuth: typ tokenu: typu Saml2
- refresh_token: \<Refresh token\>

## <a name="client-limitations"></a>Omezení klienta

Veřejné klienty s adresami URL odpovědí se zástupnými znaky nemůžou používat `id_token` OBO toky. Důvěrného klienta však stále můžou uplatnit **přístupové** tokeny získané prostřednictvím implicitního toku udělení, i když má veřejný klient registrovaný identifikátor URI pro přesměrování zástupného znaku.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o protokolu OAuth 2,0 a další způsob, jak provádět ověřování služby a služby, které používá pověření klienta:

* [Ověřování pomocí přihlašovacích údajů klienta OAuth 2,0 ve službě Azure AD prostřednictvím ověření služby](v1-oauth2-client-creds-grant-flow.md)
* [OAuth 2,0 ve službě Azure AD](v1-protocols-oauth-code.md)
---
title: Služba Azure AD v2.0 OAuth2.0 tok On-Behalf-Of | Microsoft Docs
description: Tento článek popisuje, jak používat zprávy protokolu HTTP k implementaci služeb ověřování pomocí OAuth2.0 tok On-Behalf-Of.
services: active-directory
documentationcenter: ''
author: hpsin
manager: mtillman
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: hirsin
ms.custom: aaddev
ms.openlocfilehash: ccec8df0741870f3dd3ed21be43f96aa8ba90927
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="azure-active-directory-v20-and-oauth-20-on-behalf-of-flow"></a>Azure Active Directory v2.0 a toku OAuth 2.0 On-Behalf-Of
On-Behalf-Of OAuth 2.0, které toku slouží případ použití, kde aplikace volá služby nebo webové rozhraní API, který se pak musí volat jiné služby nebo webové rozhraní API. Cílem je potřebný k šíření identity delegované uživatele a oprávnění pomocí řetězce požadavků. Pro službu střední vrstvy provést ověřené žádosti o připojení ke službě podřízené potřebuje přístupový token zabezpečení ze služby Azure Active Directory (Azure AD) jménem uživatele.

> [!NOTE]
> Koncový bod v2.0 nepodporuje všechny scénáře Azure Active Directory a funkce. Pokud chcete zjistit, zda byste měli používat koncový bod v2.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).
>
>

## <a name="protocol-diagram"></a>Diagram protokolu
Předpokládejme, že má uživatel ověřen na aplikace pomocí [tok poskytování autorizačních kódů OAuth 2.0](active-directory-v2-protocols-oauth-code.md).  V tomto okamžiku aplikace má přístupový token *pro rozhraní API A* (token A) s deklarací identity uživatele a souhlasu pro přístup k střední vrstvě webové rozhraní API (rozhraní API A). Rozhraní API A potřebuje teď, aby požadavek na ověřeného k podřízené webové rozhraní API (API B).

> [!IMPORTANT]
> Tokeny získaných pomocí [implicitní grant](active-directory-v2-protocols-implicit.md) nelze použít pro tok On-Behalf-Of.  Klient v implcit toky není ověřen (prostřednictvím například tajný klíč klienta) a proto by nemělo být povoleno bootstrap do druhého, které by mohly mít výkonnější tokenu.

Kroky, které následují tvoří tok On-Behalf-Of a jsou vysvětleny za pomoci následující diagram.

![OAuth2.0 tok On-Behalf-Of](media/active-directory-protocols-oauth-on-behalf-of/active-directory-protocols-oauth-on-behalf-of-flow.png)


1. Klientská aplikace odešle požadavek na rozhraní API s tokenem A (s `aud` deklarace identity a rozhraní API).
2. Rozhraní API A přihlásí k vystavování tokenů koncového bodu Azure AD a požadavky token pro přístup k rozhraní API B.
3. Koncový bod vystavování tokenů Azure AD ověřuje přihlašovací údaje A rozhraní API se token a vydá přístupový token pro rozhraní API B (tokenu B).
4. Token B je nastaveny v hlavičce autorizace požadavku rozhraní API b.
5. Vrátí data z zabezpečeným prostředkům rozhraní API B.

> [!NOTE]
> V tomto scénáři má služby střední vrstvy získat souhlas uživatele pro přístup k rozhraní API pro příjem dat žádná interakce s uživatelem. Možnost udělit přístup k rozhraní API pro příjem dat, se proto zobrazí předem, jako součást souhlasu krok při ověřování.
>

## <a name="service-to-service-access-token-request"></a>Služba service žádosti o token přístupu
Chcete-li požádat o token přístupu, provést HTTP POST konkrétního klienta koncového bodu v2.0 Azure AD s následujícími parametry.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

Jsou dva případy, v závislosti na tom, zda klientská aplikace rozhodne zabezpečeny sdílený tajný klíč nebo certifikát.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Nejprve případ: žádosti o token přístupu s sdílený tajný klíč
Pokud používáte sdílený tajný klíč, žádosti o token přístupu service-to-service obsahuje následující parametry:

| Parametr |  | Popis |
| --- | --- | --- |
| grant_type |Požadované | Typ požadavku na token. Pro žádost o pomocí token JWT, hodnota musí být **urn: ietf:params:oauth:grant – typ: jwt-nosiče**. |
| client_id |Požadované | ID aplikace, která [portálu pro registraci aplikace](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) přiřazené vaší aplikaci. |
| tajný klíč client_secret |Požadované | Tajný klíč aplikace generovaný pro aplikaci v portálu pro registraci aplikace. |
| Kontrolní výraz |Požadované | Hodnota tokenu používaného v požadavku. |
| scope |Požadované | Mezeru oddělený seznam obory pro požadavek tokenu. Další informace najdete v tématu [obory](active-directory-v2-scopes.md).|
| requested_token_use |Požadované | Určuje, jak by měl být požadavek zpracovat. Hodnota tok On-Behalf-Of, musí být **on_behalf_of**. |

#### <a name="example"></a>Příklad:
Následující HTTP POST požadavky přístupový token a obnovovací token s `user.read` pro obor https://graph.microsoft.com webové rozhraní API.

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

### <a name="second-case-access-token-request-with-a-certificate"></a>Druhé případ: tokenu žádosti o přístup pomocí certifikátu
Žádosti o token service-to-service přístup pomocí certifikátu obsahuje následující parametry:

| Parametr |  | Popis |
| --- | --- | --- |
| grant_type |Požadované | Typ požadavku na token. Pro žádost o pomocí token JWT, hodnota musí být **urn: ietf:params:oauth:grant – typ: jwt-nosiče**. |
| client_id |Požadované | ID aplikace, která [portálu pro registraci aplikace](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) přiřazené vaší aplikaci. |
| client_assertion_type |Požadované |Hodnota musí být `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |Požadované | (Webového tokenu JSON) kontrolní výraz, který potřebujete k vytvoření a podepsání s certifikátem, můžete zaregistrovat jako přihlašovací údaje pro vaši aplikaci.  Přečtěte si informace o [certifikát přihlašovacích údajů](active-directory-certificate-credentials.md) Další informace o registraci vašeho certifikátu a formát kontrolní výraz.|
| Kontrolní výraz |Požadované | Hodnota tokenu používaného v požadavku. |
| requested_token_use |Požadované | Určuje, jak by měl být požadavek zpracovat. Hodnota tok On-Behalf-Of, musí být **on_behalf_of**. |
| scope |Požadované | Mezeru oddělený seznam obory pro požadavek tokenu. Další informace najdete v tématu [obory](active-directory-v2-scopes.md).|

Všimněte si, že parametry jsou téměř stejné jako v případě požadavku pomocí sdílený tajný klíč, s tím rozdílem, že parametr tajný klíč client_secret je nahrazena dva parametry: client_assertion_type a client_assertion.

#### <a name="example"></a>Příklad:
V následujícím příspěvku HTTP požadavků přístupový token s `user.read` obor pro https://graph.microsoft.com webové rozhraní API s certifikátem.

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

## <a name="service-to-service-access-token-response"></a>Služba odpovědi tokenu přístupu služby
Úspěšná odpověď je odpověď JSON OAuth 2.0 s následujícími parametry.

| Parametr | Popis |
| --- | --- |
| token_type |Určuje hodnotu typ tokenu. Pouze typ, který podporuje Azure AD je **nosiče**. Další informace o nosné tokeny, najdete v článku [Framework autorizace OAuth 2.0: použití tokenů nosiče (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Rozsah udělení v tokenu přístupu. |
| expires_in |Doba přístupový token je platný (v sekundách). |
| access_token |Požadovaný přístupový token. Volání služby můžete použít tento token k ověření přijímající služby. |
| refresh_token |Token obnovení pro požadovaný přístupový token. Volání služby můžete tento token vyžádat dalšího přístupového tokenu po vypršení platnosti aktuální přístupový token. Token obnovení je zadáváno, pouze pokud `offline_access` požadoval oboru.|

### <a name="success-response-example"></a>Příklad úspěšné odpovědi
Následující příklad ukazuje tokenu pro úspěšná odpověď na žádost o přístup https://graph.microsoft.com webové rozhraní API.

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
> Všimněte si, že výše přístupový token je token formátu V1.  Je to proto, že token je k dispozici podle prostředků přistupuje.  Microsoft Graph požaduje V1 tokeny, takže Azure AD vytváří V1 přístupové tokeny, když klient požádá o tokeny pro Microsoft Graph.  Jenom aplikace by měla vypadat v přístupové tokeny – klienti obvykle není nutné je zkontrolovat. 


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
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkSzdNN0RyNXlvUUdLNmFEc19vdDF3cEQyZjNqRkxiNlVrcm9PcXA2cXBJclAxZVV0QktzMHEza29HN3RzXzJpSkYtQjY1UV8zVGgzSnktUHZsMjkxaFNBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMDE2LCJuYmYiOjE0OTM5MzAwMTYsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQUlzQjN5ZUljNkZ1aEhkd1YxckoxS1dlbzJPckZOUUQwN2FENTVjUVRtems9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJzUVlVekYxdUVVS0NQS0dRTVFVRkFBIiwidmVyIjoiMS4wIn0.Hrn__RGi-HMAzYRyCqX3kBGb6OS7z7y49XPVPpwK_7rJ6nik9E4s6PNY4XkIamJYn7tphpmsHdfM9lQ1gqeeFvFGhweIACsNBWhJ9Nx4dvQnGRkqZ17KnF_wf_QLcyOrOWpUxdSD_oPKcPS-Qr5AFkjw0t7GOKLY-Xw3QLJhzeKmYuuOkmMDJDAl0eNDbH0HiCh3g189a176BfyaR0MgK8wrXI_6MTnFSVfBePqklQeLhcr50YTBfWg3Svgl6MuK_g1hOuaO-XpjUxpdv5dZ0SvI47fAuVDdpCE48igCX5VMj4KUVytDIf6T78aIXMkYHGgW3-xAmuSyYH_Fr0yVAQ
```

## <a name="next-steps"></a>Další postup
Další informace o protokolu OAuth 2.0 a jiný způsob, jak provádět ověřování služeb pomocí pověření klienta.
* [Udělení pověření klienta OAuth 2.0 v Azure AD v2.0](active-directory-v2-protocols-oauth-client-creds.md)
* [OAuth 2.0 v Azure AD v2.0](active-directory-v2-protocols-oauth-code.md)

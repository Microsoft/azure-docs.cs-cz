---
title: Konfigurovat přihlašovací údaje toku heslo vlastníka prostředku v Azure Active Directory B2C | Microsoft Docs
description: Naučte se konfigurovat přihlašovací údaje toku heslo vlastníka prostředku v Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/24/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 073af4a57d55eb8b2f3608482159b57c7b408f3b
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102234"
---
# <a name="configure-the-resource-owner-password-credentials-flow-in-azure-ad-b2c"></a>Konfigurovat přihlašovací údaje toku heslo vlastníka prostředku v Azure AD B2C

Tok přihlašovací údaje (ROPC) heslo vlastníka prostředku je tok standardní ověřování OAuth, kde aplikace, také známé jako přijímající strany výměny platné přihlašovací údaje, jako je například ID uživatele a heslo pro ID token, token přístupu a obnovovací token. 

> [!NOTE]
> Tato funkce je ve verzi Preview.

V Azure Active Directory (Azure AD) B2C jsou podporovány následující možnosti:

- **Nativní klient**: interakce s uživatelem během ověřování se stane, když spuštění kódu na straně pro uživatele zařízení. Zařízení může být mobilní aplikace, která běží v nativním operačního systému, například Android, nebo v prohlížeči, jako je JavaScript.
- **Tok veřejné klienta**: se odesílají pouze uživatelské přihlašovací údaje shromážděné aplikace, volání rozhraní API. Přihlašovací údaje aplikace se neodesílají.
- **Přidání nových deklarací identity**: ID tokenu obsah můžete změnit tak, aby přidání nových deklarací identity. 

Následující toky nejsou podporovány:

- **Na serveru**: systém ochrany identit musí shromážděná z volající (Nativní klient) jako součást interakce spolehlivé IP adresu. Volání rozhraní API na straně serveru je použít jenom IP adresy serveru. Pokud je překročena dynamické mezní hodnota neúspěšné ověřování, může systém ochrany identit Identifikujte opakovaných IP adresu jako útočník.
- **Tok důvěrné klienta**: ověření ID klienta aplikace, ale není ověřený tajný klíč aplikace.

##  <a name="create-a-resource-owner-policy"></a>Vytvoření vlastníka prostředků

1. Přihlaste se k portálu Azure jako globální správce tenanta Azure AD B2C.
2. Přejděte ke klientovi Azure AD B2C, vyberte adresář B2C v pravém horním rohu portálu.
3. V části **zásady**, vyberte **vlastník prostředku zásady**.
4. Zadejte název zásady, jako třeba *ROPC_Auth*a potom vyberte **deklarace identity aplikace**.
5. Vyberte aplikaci deklarací identity, které potřebujete pro vaši aplikaci, například *zobrazovaný název*, *e-mailovou adresu*, a *zprostředkovatele Identity*.
6. Vyberte **OK** a potom vyberte **Vytvořit**.

   Zobrazí koncový bod například Tento příklad:

   `https://login.microsoftonline.com/yourtenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1A_ROPC_Auth`


## <a name="register-an-application"></a>Registrace aplikace

1. V nastavení B2C vyberte **aplikace**a potom vyberte **přidat**.
2. Zadejte název aplikace, jako například *ROPC_Auth_app*.
3. Vyberte **ne** pro **webové aplikaci nebo webové rozhraní API**a potom vyberte **Ano** pro **nativního klienta**.
4. Nechte všechny ostatní hodnoty tak, jak jsou a potom vyberte **vytvořit**.
5. Vyberte novou aplikaci a poznamenejte si ID aplikace pro pozdější použití.

## <a name="test-the-policy"></a>Testování zásad

Generovat volání rozhraní API pomocí Oblíbené vývoj aplikace API a zkontrolovat odpověď na ladění vaše zásady. Vytvořte podobné volání pomocí informací v následující tabulce jako text požadavku POST:
- Nahraďte  *\<yourtenant.onmicrosoft.com >* s názvem svého klienta B2C.
- Nahraďte  *\<B2C_1A_ROPC_Auth >* s úplný název zásad pověření heslo vlastníka prostředku.
- Nahraďte  *\<bef2222d56-552f-4a5b-b90a-1988a7d634c3 >* s ID aplikace z registrace.

`https://login.microsoftonline.com/<yourtenant.onmicrosoft.com>/<B2C_1A_ROPC_Auth>/oauth2/v2.0/token`

| Klíč | Hodnota |
| --- | ----- |
| uživatelské jméno | leadiocl@outlook.com |
| heslo | Passxword1 |
| grant_type | heslo |
| scope | openid \<bef2222d56-552f-4a5b-b90a-1988a7d634c3 > offline_access |
| client_id | \<bef2222d56-552f-4a5b-b90a-1988a7d634c3 > |
| response_type | Token požadavku id_token |

*Client_id* je hodnota, kterou jste dříve si poznamenali jako ID aplikace. *Offline_access* je nepovinná, pokud chcete dostávat token obnovení. 

Skutečný požadavek POST vypadá takto:

```
POST /yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

username=leadiocl%40trashmail.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```


Úspěšná odpověď s offline přístup vypadá jako v následujícím příkladu:

```
{ 
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJpc3MiOiJodHRwczovL3RlLmNwaW0ud2luZG93cy5uZXQvZjA2YzJmZTgtNzA5Zi00MDMwLTg1ZGMtMzhhNGJmZDllODJkL3YyLjAvIiwiZXhwIjoxNTEzMTMwMDc4LCJuYmYiOjE1MTMxMjY0NzgsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsIm9pZCI6IjNjM2I5NjljLThjNDktNGUxMS1hNGVmLWZkYjJmMzkyZjA0OSIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF6cCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsInZlciI6IjEuMCIsImlhdCI6MTUxMzEyNjQ3OH0.MSEThYZxCS4SevBw3-3ecnVLUkucFkehH-gH-P7SFcJ-MhsBeQEpMF1Rzu_R9kUqV3qEWKAPYCNdZ3_P4Dd3a63iG6m9TnO1Vt5SKTETuhVx3Xl5LYeA1i3Slt9Y7LIicn59hGKRZ8ddrQzkqj69j723ooy01amrXvF6zNOudh0acseszt7fbzzofyagKPerxaeTH0NgyOinLwXu0eNj_6RtF9gBfgwVidRy9OzXUJnqm1GdrS61XUqiIUtv4H04jYxDem7ek6E4jsH809uSXT0iD5_4C5bDHrpO1N6pXSasmVR9GM1XgfXA_IRLFU4Nd26CzGl1NjbhLnvli2qY4A", 
    "token_type": "Bearer", 
    "expires_in": "3600", 
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6IjEuMCJ9.aJ_2UW14dh4saWTQ0jLJ7ByQs5JzIeW_AU9Q_RVFgrrnYiPhikEc68ilvWWo8B20KTRB_s7oy_Eoh5LACsqU6Oz0Mjnh0-DxgrMblUOTAQ9dbfAT5WoLZiCBJIz4YT5OUA_RAGjhBUkqGwdWEumDExQnXIjRSeaUBmWCQHPPguV1_5wSj8aW2zIzYIMbofvpjwIATlbIZwJ7ufnLypRuq_MDbZhJkegDw10KI4MHJlJ40Ip8mCOe0XeJIDpfefiJ6WQpUq4zl06NO7j8kvDoVq9WALJIao7LYk_x9UIT-3d0W0eDBHGSRcNgtMYpymaN9ltx6djcEesXNn4CFnWG3g.y6KKeA9EcsW9zW-g.TrTSgn4WBt18gezegxihBla9SLSTC3YfDROQsL9K4yX4400FKlTlf-2l9CnpGTEdWXVi7sIMHCl8S4oUiXd-rvY2mn_NfDrbbVJfgKp1j7Nnq9FFyeJEFcP_FtUXgsNTG9iwfzWox04B1d845qNRWiS9N8BhAAAIdz5N0ChHuOxsVOC0Y_Ly3DNe-JQyXcq964M6-jp3cgi4UqMxT837L6pLY5Ih_iPsSfyHzstsFeqyUIktnzt1MpTlyW-_GDyFK1S-SyV8PPQ7phgFouw2jho1iboHX70RlDGYyVmP1CfQzKE_zWxj3rgaCZvYMWN8fUenoiatzhvWkUM7dhqKGjofPeL8rOMkhl6afLLjObzhUg3PZFcMR6guLjQdEwQFufWxGjfpvaHycZSKeWu6-7dF8Hy_nyMLLdBpUkdrXPob_5gRiaH72KvncSIFvJLqhY3NgXO05Fy87PORjggXwYkhWh4FgQZBIYD6h0CSk2nfFjR9uD9EKiBBWSBZj814S_Jdw6HESFtn91thpvU3hi3qNOi1m41gg1vt5Kh35A5AyDY1J7a9i_lN4B7e_pknXlVX6Z-Z2BYZvwAU7KLKsy5a99p9FX0lg6QweDzhukXrB4wgfKvVRTo.mjk92wMk-zUSrzuuuXPVeg", 
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJleHAiOjE1MTMxMzAwNzgsIm5iZiI6MTUxMzEyNjQ3OCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly90ZS5jcGltLndpbmRvd3MubmV0L2YwNmMyZmU4LTcwOWYtNDAzMC04NWRjLTM4YTRiZmQ5ZTgyZC92Mi4wLyIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsImFjciI6ImIyY18xYV9yZXNvdXJjZW93bmVydjIiLCJpYXQiOjE1MTMxMjY0NzgsImF1dGhfdGltZSI6MTUxMzEyNjQ3OCwib2lkIjoiM2MzYjk2OWMtOGM0OS00ZTExLWE0ZWYtZmRiMmYzOTJmMDQ5IiwiYXRfaGFzaCI6Ikd6QUNCTVJtcklwYm9OdkFtNHhMWEEifQ.iAJg13cgySsdH3cmoEPGZB_g-4O8KWvGr6W5VzRXtkrlLoKB1pl4hL6f_0xOrxnQwj2sUgW-wjsCVzMc_dkHSwd9QFZ4EYJEJbi1LMGk2lW-PgjsbwHPDU1mz-SR1PeqqJgvOqrzXo0YHXr-e07M4v4Tko-i_OYcrdJzj4Bkv7ZZilsSj62lNig4HkxTIWi5Ec2gD79bPKzgCtIww1KRnwmrlnCOrMFYNj-0T3lTDcXAQog63MOacf7OuRVUC5k_IdseigeMSscrYrNrH28s3r0JoqDhNUTewuw1jx0X6gdqQWZKOLJ7OF_EJMP-BkRTixBGK5eW2YeUUEVQxsFlUg" 
} 
```

## <a name="redeem-a-refresh-token"></a>Uplatnit obnovovací token

Vytvoření volání POST zde zobrazený pomocí informací v následující tabulce jako text žádosti:

`https://login.microsoftonline.com/<yourtenant.onmicrosoft.com>/<B2C_1A_ROPC_Auth>/oauth2/v2.0/token`

| Klíč | Hodnota |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | \<bef2222d56-552f-4a5b-b90a-1988a7d634c3 > |
| prostředek | \<bef2222d56-552f-4a5b-b90a-1988a7d634c3 > |
| refresh_token | eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3... |

*Client_id* a *prostředků* jsou hodnoty v předchozí části jako ID aplikace. *Refresh_token* je token, který jste obdrželi v ověřovacího hovoru již bylo zmíněno dříve.

## <a name="implement-with-your-preferred-native-sdk-or-use-app-auth"></a>Implementace s vaší upřednostňované nativní SDK nebo použít ověřování aplikace

Implementace Azure AD B2C splňuje standardy OAuth 2.0 pro oprávnění hesla vlastníka prostředku veřejné klienta a musí být kompatibilní s většině klientem sady SDK. Tento tok mít hojně, testování v produkčním prostředí s AppAuth pro iOS a AppAuth pro Android. Nejnovější informace najdete v tématu [nativní App SDK pro OAuth 2.0 a OpenID Connect implementace moderní osvědčené postupy](https://appauth.io/).

Stáhněte si ukázky pracovní, které byly nakonfigurovány pro použití se službou Azure AD B2C z Githubu, [pro Android](https://aka.ms/aadb2cappauthropc) a [pro iOS](https://aka.ms/aadb2ciosappauthropc).





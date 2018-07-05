---
title: Nakonfigurujte tok přihlašovacího hesla vlastníka prostředku v Azure Active Directory B2C | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat tok přihlašovacího hesla vlastníka prostředku v Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5d68f8fe28b7f029d19a0ed0c03e5324c32f29c0
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446805"
---
# <a name="configure-the-resource-owner-password-credentials-flow-in-azure-ad-b2c"></a>Nakonfigurujte tok přihlašovacího hesla vlastníka prostředku v Azure AD B2C

Tok přihlašovacích údajů (ROPC) hesla vlastníka prostředku je tok, který standardní ověřování OAuth, kde aplikace, označované také jako přijímající strany vymění platné přihlašovací údaje, jako je například ID uživatele a heslo pro ID token, přístupový token a aktualizační token. 

> [!NOTE]
> Tato funkce je ve verzi Preview.

V Azure Active Directory (Azure AD) B2C jsou podporovány následující možnosti:

- **Nativní klient systému**: interakce s uživatelem během ověřování se stane, když kód běží na uživatele na straně zařízení. Zařízení může být mobilní aplikace, která běží v nativním operačním systému, jako je například Android, nebo v prohlížeči, třeba JavaScript.
- **Tok veřejným klientem**: pouze přihlašovací údaje uživatele, shromážděné aplikaci, která se odesílají v volání rozhraní API. Přihlašovací údaje aplikace neodešlou.
- **Přidání nových deklarací identity**: ID tokenu obsah lze změnit pro přidání nových deklarací identity. 

Nejsou podporovány v následujících tocích:

- **Na serveru**: systému identity protection potřebuje shromážděných z volající (Nativní klient) jako součást interakce spolehlivé IP adresu. Ve volání rozhraní API na straně serveru je použít jenom IP adresa serveru. Pokud dojde k překročení dynamická prahová hodnota neúspěšné ověřování, může systém ochrany identit identifikovat opakované IP adresu jako útočník.
- **Tok důvěrnému klientovi**: ověření ID klienta aplikace, ale neověří tajný klíč aplikace.

##  <a name="create-a-resource-owner-policy"></a>Vytvoření zásady vlastníka prostředku

1. Přihlaste se k webu Azure portal jako globální správce tenanta Azure AD B2C.
2. Pokud chcete přepnout na svého tenanta Azure AD B2C, vyberte adresář B2C v pravém horním rohu portálu.
3. V části **zásady**vyberte **zásady vlastníka prostředku**.
4. Zadejte název zásady, jako třeba *ROPC_Auth*a pak vyberte **deklarace identit aplikace**.
5. Vyberte deklarace identit aplikace, které potřebujete pro vaši aplikaci, jako například *zobrazovaný název*, *e-mailovou adresu*, a *zprostředkovatele Identity*.
6. Vyberte **OK** a potom vyberte **Vytvořit**.

   Zobrazí koncový bod jako je například v tomto příkladu:

   `https://login.microsoftonline.com/yourtenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1A_ROPC_Auth`


## <a name="register-an-application"></a>Registrace aplikace

1. V nastavení B2C vyberte **aplikací**a pak vyberte **přidat**.
2. Zadejte název aplikace, jako například *ROPC_Auth_app*.
3. Vyberte **ne** pro **webová aplikace/webové rozhraní API**a pak vyberte **Ano** pro **nativního klienta**.
4. Nechte ostatní hodnoty, jak jsou a pak vyberte **vytvořit**.
5. Vyberte novou aplikaci a poznamenejte si ID aplikace pro pozdější použití.

## <a name="test-the-policy"></a>Testování zásad

Generovat volání rozhraní API pomocí aplikace pro vývoj oblíbených rozhraní API a zkontrolovat odpověď, chcete-li ladit vaše zásady. Sestavit podobné volání pomocí informací v následující tabulce jako text požadavku POST:
- Nahraďte  *\<yourtenant.onmicrosoft.com >* s názvem vašeho tenanta B2C.
- Nahraďte  *\<B2C_1A_ROPC_Auth >* úplným názvem zásady pověření heslo vlastníka prostředku.
- Nahraďte  *\<bef2222d56-552f-4a5b-b90a-1988a7d634c3 >* s ID aplikace z vaší registrace.

`https://login.microsoftonline.com/<yourtenant.onmicrosoft.com>/<B2C_1A_ROPC_Auth>/oauth2/v2.0/token`

| Klíč | Hodnota |
| --- | ----- |
| uživatelské jméno | leadiocl@outlook.com |
| heslo | Passxword1 |
| Parametr grant_type | heslo |
| scope | openid \<bef2222d56-552f-4a5b-b90a-1988a7d634c3 > offline_access |
| client_id | \<bef2222d56 552f-4a5b-b90a-1988a7d634c3 > |
| response_type | Token požadavku id_token |

*Client_id* je hodnota, kterou jste si předtím poznamenali jako ID aplikace. *Offline_access* je volitelný, pokud chcete dostávat obnovovací token. 

Skutečné požadavek POST vypadá takto:

```
POST /yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

username=leadiocl%40trashmail.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```


Úspěšná odpověď s offline přístupem bude vypadat jako v následujícím příkladu:

```
{ 
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJpc3MiOiJodHRwczovL3RlLmNwaW0ud2luZG93cy5uZXQvZjA2YzJmZTgtNzA5Zi00MDMwLTg1ZGMtMzhhNGJmZDllODJkL3YyLjAvIiwiZXhwIjoxNTEzMTMwMDc4LCJuYmYiOjE1MTMxMjY0NzgsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsIm9pZCI6IjNjM2I5NjljLThjNDktNGUxMS1hNGVmLWZkYjJmMzkyZjA0OSIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF6cCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsInZlciI6IjEuMCIsImlhdCI6MTUxMzEyNjQ3OH0.MSEThYZxCS4SevBw3-3ecnVLUkucFkehH-gH-P7SFcJ-MhsBeQEpMF1Rzu_R9kUqV3qEWKAPYCNdZ3_P4Dd3a63iG6m9TnO1Vt5SKTETuhVx3Xl5LYeA1i3Slt9Y7LIicn59hGKRZ8ddrQzkqj69j723ooy01amrXvF6zNOudh0acseszt7fbzzofyagKPerxaeTH0NgyOinLwXu0eNj_6RtF9gBfgwVidRy9OzXUJnqm1GdrS61XUqiIUtv4H04jYxDem7ek6E4jsH809uSXT0iD5_4C5bDHrpO1N6pXSasmVR9GM1XgfXA_IRLFU4Nd26CzGl1NjbhLnvli2qY4A", 
    "token_type": "Bearer", 
    "expires_in": "3600", 
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6IjEuMCJ9.aJ_2UW14dh4saWTQ0jLJ7ByQs5JzIeW_AU9Q_RVFgrrnYiPhikEc68ilvWWo8B20KTRB_s7oy_Eoh5LACsqU6Oz0Mjnh0-DxgrMblUOTAQ9dbfAT5WoLZiCBJIz4YT5OUA_RAGjhBUkqGwdWEumDExQnXIjRSeaUBmWCQHPPguV1_5wSj8aW2zIzYIMbofvpjwIATlbIZwJ7ufnLypRuq_MDbZhJkegDw10KI4MHJlJ40Ip8mCOe0XeJIDpfefiJ6WQpUq4zl06NO7j8kvDoVq9WALJIao7LYk_x9UIT-3d0W0eDBHGSRcNgtMYpymaN9ltx6djcEesXNn4CFnWG3g.y6KKeA9EcsW9zW-g.TrTSgn4WBt18gezegxihBla9SLSTC3YfDROQsL9K4yX4400FKlTlf-2l9CnpGTEdWXVi7sIMHCl8S4oUiXd-rvY2mn_NfDrbbVJfgKp1j7Nnq9FFyeJEFcP_FtUXgsNTG9iwfzWox04B1d845qNRWiS9N8BhAAAIdz5N0ChHuOxsVOC0Y_Ly3DNe-JQyXcq964M6-jp3cgi4UqMxT837L6pLY5Ih_iPsSfyHzstsFeqyUIktnzt1MpTlyW-_GDyFK1S-SyV8PPQ7phgFouw2jho1iboHX70RlDGYyVmP1CfQzKE_zWxj3rgaCZvYMWN8fUenoiatzhvWkUM7dhqKGjofPeL8rOMkhl6afLLjObzhUg3PZFcMR6guLjQdEwQFufWxGjfpvaHycZSKeWu6-7dF8Hy_nyMLLdBpUkdrXPob_5gRiaH72KvncSIFvJLqhY3NgXO05Fy87PORjggXwYkhWh4FgQZBIYD6h0CSk2nfFjR9uD9EKiBBWSBZj814S_Jdw6HESFtn91thpvU3hi3qNOi1m41gg1vt5Kh35A5AyDY1J7a9i_lN4B7e_pknXlVX6Z-Z2BYZvwAU7KLKsy5a99p9FX0lg6QweDzhukXrB4wgfKvVRTo.mjk92wMk-zUSrzuuuXPVeg", 
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJleHAiOjE1MTMxMzAwNzgsIm5iZiI6MTUxMzEyNjQ3OCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly90ZS5jcGltLndpbmRvd3MubmV0L2YwNmMyZmU4LTcwOWYtNDAzMC04NWRjLTM4YTRiZmQ5ZTgyZC92Mi4wLyIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsImFjciI6ImIyY18xYV9yZXNvdXJjZW93bmVydjIiLCJpYXQiOjE1MTMxMjY0NzgsImF1dGhfdGltZSI6MTUxMzEyNjQ3OCwib2lkIjoiM2MzYjk2OWMtOGM0OS00ZTExLWE0ZWYtZmRiMmYzOTJmMDQ5IiwiYXRfaGFzaCI6Ikd6QUNCTVJtcklwYm9OdkFtNHhMWEEifQ.iAJg13cgySsdH3cmoEPGZB_g-4O8KWvGr6W5VzRXtkrlLoKB1pl4hL6f_0xOrxnQwj2sUgW-wjsCVzMc_dkHSwd9QFZ4EYJEJbi1LMGk2lW-PgjsbwHPDU1mz-SR1PeqqJgvOqrzXo0YHXr-e07M4v4Tko-i_OYcrdJzj4Bkv7ZZilsSj62lNig4HkxTIWi5Ec2gD79bPKzgCtIww1KRnwmrlnCOrMFYNj-0T3lTDcXAQog63MOacf7OuRVUC5k_IdseigeMSscrYrNrH28s3r0JoqDhNUTewuw1jx0X6gdqQWZKOLJ7OF_EJMP-BkRTixBGK5eW2YeUUEVQxsFlUg" 
} 
```

## <a name="redeem-a-refresh-token"></a>Uplatnění obnovovací token

Vytvoření volání POST podobný tomu vidíte tady pomocí informací v následující tabulce jako text žádosti:

`https://login.microsoftonline.com/<yourtenant.onmicrosoft.com>/<B2C_1A_ROPC_Auth>/oauth2/v2.0/token`

| Klíč | Hodnota |
| --- | ----- |
| Parametr grant_type | refresh_token |
| response_type | id_token |
| client_id | \<bef2222d56 552f-4a5b-b90a-1988a7d634c3 > |
| prostředek | \<bef2222d56 552f-4a5b-b90a-1988a7d634c3 > |
| refresh_token | eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3... |

*Client_id* a *prostředků* jsou hodnoty, které jste si předtím poznamenali jako ID aplikace. *Refresh_token* je token, který jste obdrželi v ověřovacím hovoru, již bylo zmíněno dříve.

## <a name="implement-with-your-preferred-native-sdk-or-use-app-auth"></a>Implementace s upřednostňované nativní sadou SDK nebo použít ověřování aplikace

Azure AD B2C implementace splňuje standardy veřejným klientem přihlašovacího hesla vlastníka prostředku OAuth 2.0 a by měl být kompatibilní s většina klientské sady SDK. Tento tok mají často, testování v produkčním prostředí s AppAuth pro iOS a AppAuth pro Android. Nejnovější informace najdete v tématu [Native App SDK pro OAuth 2.0 a OpenID Connect, implementace moderní osvědčené postupy](https://appauth.io/).

Stáhnout pracovní ukázky, které byly nakonfigurovány pro použití se službou Azure AD B2C z Githubu, [pro Android](https://aka.ms/aadb2cappauthropc) a [pro iOS](https://aka.ms/aadb2ciosappauthropc).





---
title: Tok kódu zařízení OAuth 2,0 | Azure
titleSuffix: Microsoft identity platform
description: Přihlaste se uživatelům bez prohlížeče. Pomocí udělení autorizace pro zařízení Sestavte integrované postupy ověřování a bez prohlížeče.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 8c757f3e067aeac5d8145ca47b2eac145daba574
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88272446"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Microsoft Identity Platform a tok udělení autorizace zařízení OAuth 2,0

Platforma Microsoft identity podporuje [udělení autorizací zařízení](https://tools.ietf.org/html/rfc8628), které umožňuje uživatelům přihlásit se ke vstupním zařízením s omezeným použitím, jako je například inteligentní TV, zařízení IoT nebo tiskárna.  Pokud chcete tento tok povolit, zařízení uživateli navštíví webovou stránku v prohlížeči na jiném zařízení, abyste se přihlásili.  Jakmile se uživatel přihlásí, zařízení může získat přístupové tokeny a aktualizovat tokeny podle potřeby.

Tento článek popisuje, jak programovat přímo s protokolem ve vaší aplikaci.  Pokud je to možné, doporučujeme místo toho použít podporované knihovny Microsoft Authentication Library (MSAL) k [získání tokenů a volání zabezpečených webových rozhraní API](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Podívejte se také na [ukázkové aplikace, které používají MSAL](sample-v2-code.md).

## <a name="protocol-diagram"></a>Diagram protokolu

Celý tok kódu zařízení vypadá podobně jako u dalšího diagramu. Každý z těchto kroků popisujeme dále v tomto článku.

![Tok kódu zařízení](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Žádost o autorizaci zařízení

Klient se musí nejdřív ověřit pomocí ověřovacího serveru pro zařízení a uživatelský kód, který se používá k inicializaci ověřování. Klient shromáždí tuto žádost z `/devicecode` koncového bodu. V této žádosti by měl klient také zahrnovat oprávnění, která potřebuje k získání od uživatele. Od chvíle, kdy se tato žádost pošle, se uživateli přihlásí jenom 15 minut (obvyklá hodnota pro `expires_in` ), takže tuto žádost udělejte jenom v případě, že uživatel označil, že jsou připravené na přihlášení.

> [!TIP]
> Zkuste tento požadavek provést v nástroji post!
> [![Pokus o spuštění této žádosti v předzálohovacím](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parametr | Podmínka | Popis |
| --- | --- | --- |
| `tenant` | Povinné | Může být/běžné,/consumers nebo/Organizations.  Může to být také tenant adresáře, ze kterého chcete požádat o oprávnění ve formátu GUID nebo popisného názvu.  |
| `client_id` | Vyžadováno | **ID aplikace (klienta)** , které [Azure Portal – registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) prostředí přiřazené k vaší aplikaci. |
| `scope` | Vyžadováno | Mezerou oddělený seznam [oborů](v2-permissions-and-consent.md) , ke kterým má uživatel udělit souhlas.  |

### <a name="device-authorization-response"></a>Odpověď na autorizaci zařízení

Úspěšná odpověď bude objekt JSON, který obsahuje požadované informace, aby se uživatel mohl přihlásit.

| Parametr | Formát | Description |
| ---              | --- | --- |
|`device_code`     | Řetězec | Dlouhý řetězec, který slouží k ověření relace mezi klientem a autorizačním serverem. Klient používá tento parametr k vyžádání přístupového tokenu z autorizačního serveru. |
|`user_code`       | Řetězec | Krátký řetězec zobrazený uživateli, který se používá k identifikaci relace v sekundárním zařízení.|
|`verification_uri`| Identifikátor URI | Identifikátor URI, který by měl uživatel přejít na, aby se `user_code` mohl přihlásit. |
|`expires_in`      | int | Počet sekund před `device_code` `user_code` vypršením platnosti a. |
|`interval`        | int | Počet sekund, po které má klient čekat mezi požadavky na dotazování. |
| `message`        | Řetězec | Uživatelsky čitelný řetězec s pokyny pro uživatele. To lze lokalizovat zahrnutím **parametru dotazu** do žádosti formuláře `?mkt=xx-XX` a vyplněním příslušného kódu jazykové kultury. |

> [!NOTE]
> V `verification_uri_complete` tuto chvíli není zahrnuté ani podporované pole odpovědi.  Uvádíme to proto, že pokud si přečtete [Standard](https://tools.ietf.org/html/rfc8628) , který `verification_uri_complete` je uvedený jako volitelná součást standardu toku kódu pro zařízení.

## <a name="authenticating-the-user"></a>Ověřování uživatele

Po přijetí `user_code` a se `verification_uri` klient zobrazí uživateli, který jim dává pokyn k přihlášení pomocí svého mobilního telefonu nebo prohlížeče počítače.

Pokud se uživatel ověřuje pomocí osobního účtu (na/běžné nebo/consumers), bude vyzván k opětovnému přihlášení, aby mohl přenést stav ověřování do zařízení.  Budou se taky požádáni o poskytnutí souhlasu, aby se ujistili, že mají informace o udělených oprávněních.  To se nevztahuje na pracovní nebo školní účty používané k ověřování.

Při ověřování uživatele v `verification_uri` systému by měl klient dotazovat `/token` koncový bod požadovaného tokenu pomocí `device_code` .

```HTTP
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| Parametr | Povinné | Popis|
| -------- | -------- | ---------- |
| `tenant`  | Povinné | Stejný tenant nebo alias tenanta použitý v počátečním požadavku. |
| `grant_type` | Vyžadováno | Musí být `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Vyžadováno | Musí odpovídat `client_id` použitému v počátečním požadavku. |
| `device_code`| Vyžadováno | `device_code`Vrácený v žádosti o autorizaci zařízení.  |

### <a name="expected-errors"></a>Očekávané chyby

Tok kódu zařízení je protokol cyklického dotazování, aby klient musel obdržet chyby, než se uživatel dokončí ověřováním.

| Chyba | Popis | Akce klienta |
| ------ | ----------- | -------------|
| `authorization_pending` | Uživatel nedokončil ověřování, ale nezrušil tok. | Požadavek opakujte po nejméně `interval` sekundách. |
| `authorization_declined` | Koncový uživatel zamítl žádost o autorizaci.| Zastaví cyklické dotazování a vrátí se do neověřeného stavu.  |
| `bad_verification_code`| `device_code`Odeslání do `/token` koncového bodu nebylo rozpoznáno. | Ověřte, že klient posílá `device_code` v žádosti správné požadavky. |
| `expired_token` | Nejméně `expires_in` sekund uplynulo a ověřování již není s tímto důvodem možné `device_code` . | Zastaví cyklické dotazování a vrátí se do neověřeného stavu. |

### <a name="successful-authentication-response"></a>Úspěšná ověřovací odpověď

Úspěšná odpověď tokenu bude vypadat takto:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Parametr | Formát | Description |
| --------- | ------ | ----------- |
| `token_type` | Řetězec| Vždy "nosič". |
| `scope` | Řetězce oddělené mezerami | V případě vrácení přístupového tokenu zobrazí seznam oborů, pro které je přístupový token platný. |
| `expires_in`| int | Počet sekund, než je zahrnutý přístupový token platný pro. |
| `access_token`| Neprůhledný řetězec | Vydány pro požadované [obory](v2-permissions-and-consent.md) .  |
| `id_token`   | JWT | Vydáno, pokud původní `scope` parametr zahrnoval `openid` obor.  |
| `refresh_token` | Neprůhledný řetězec | Vydáno, pokud je `scope` zahrnut původní parametr `offline_access` .  |

Obnovovací token můžete použít k získání nových přístupových tokenů a k aktualizaci tokenů pomocí stejného toku, který je popsán v [dokumentaci toku kódu OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

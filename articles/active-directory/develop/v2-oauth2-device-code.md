---
title: Tok kódu zařízení OAuth 2.0 | Azure
titleSuffix: Microsoft identity platform
description: Přihlaste se k uživatelům bez prohlížeče. Vytvářejte vložené toky ověřování bez prohlížeče pomocí udělení autorizace zařízení.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 2a39dbb3676df5ed916203bdcbbc51d5a0da32a4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677838"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Platforma identit Microsoftu a tok autorizace zařízení OAuth 2.0

Platforma identit společnosti Microsoft podporuje [udělení autorizace zařízení](https://tools.ietf.org/html/rfc8628), které umožňuje uživatelům přihlásit se k zařízením s omezenými vstupy, jako je inteligentní televize, zařízení IoT nebo tiskárna.  Chcete-li povolit tento tok, zařízení má uživatel navštívit webovou stránku ve svém prohlížeči na jiném zařízení pro přihlášení.  Jakmile se uživatel přihlásí, zařízení může podle potřeby získat přístupové tokeny a aktualizovat tokeny.

Tento článek popisuje, jak programovat přímo proti protokolu ve vaší aplikaci.  Pokud je to možné, doporučujeme místo toho použít podporované knihovny ověřování společnosti Microsoft (MSAL) k [získání tokenů a volání zabezpečených webových rozhraní API](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Také se podívejte na [ukázkové aplikace, které používají MSAL](sample-v2-code.md).

## <a name="protocol-diagram"></a>Protokolový diagram

Celý tok kódu zařízení vypadá podobně jako další diagram. Popisujeme každý z kroků dále v tomto článku.

![Tok kódu zařízení](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Žádost o autorizaci zařízení

Klient musí nejprve zkontrolovat u ověřovacího serveru pro zařízení a uživatelský kód, který se používá k zahájení ověřování. Klient shromažďuje tento požadavek `/devicecode` z koncového bodu. V tomto požadavku by měl klient také obsahovat oprávnění, která potřebuje získat od uživatele. Od okamžiku odeslání této žádosti má uživatel pouze 15 minut na `expires_in`přihlášení (obvyklá hodnota pro), takže tuto žádost proveďte pouze v případě, že uživatel uvedl, že je připraven k přihlášení.

> [!TIP]
> Zkuste provést tento požadavek v Pošťák!
> [![Zkuste spustit tento požadavek v Pošťáku.](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parametr | Podmínka | Popis |
| --- | --- | --- |
| `tenant` | Požaduje se | Může být /common, /consumers nebo /organizations.  Může to být také klient adresáře, od kterého chcete požádat o oprávnění ve formátu GUID nebo popisný název.  |
| `client_id` | Požaduje se | **ID aplikace (klienta),** které je k vaší aplikaci přiřazené [na portálu Azure – možnosti registrace aplikací.](https://go.microsoft.com/fwlink/?linkid=2083908) |
| `scope` | Doporučené | Mezery oddělený seznam [oborů,](v2-permissions-and-consent.md) které chcete, aby uživatel souhlas.  |

### <a name="device-authorization-response"></a>Odpověď na autorizaci zařízení

Úspěšnou odpovědí bude objekt JSON obsahující požadované informace, které uživateli umožní přihlásit se.

| Parametr | Formát | Popis |
| ---              | --- | --- |
|`device_code`     | Řetězec | Dlouhý řetězec používaný k ověření relace mezi klientem a autorizačním serverem. Klient používá tento parametr k vyžádání přístupového tokenu z autorizačního serveru. |
|`user_code`       | Řetězec | Krátký řetězec zobrazený uživateli, který se používá k identifikaci relace na sekundárním zařízení.|
|`verification_uri`| Identifikátor URI | Identifikátor URI uživatel by měl `user_code` přejít s chcete-li se přihlásit. |
|`expires_in`      | int | Počet sekund před `device_code` a `user_code` vyprší. |
|`interval`        | int | Počet sekund, které by měl klient čekat mezi požadavky na dotazování. |
| `message`        | Řetězec | Řetězec čitelný pro uživatele. To lze lokalizovat zahrnutím **parametru dotazu** do požadavku formuláře `?mkt=xx-XX`a vyplněním příslušného kódu jazykové verze jazyka. |

> [!NOTE]
> Pole `verification_uri_complete` odpovědi není v současné době zahrnuto ani podporováno.  Uvádíme to, protože pokud si `verification_uri_complete` přečtete [standard,](https://tools.ietf.org/html/rfc8628) který je uveden jako volitelná součást standardu toku kódu zařízení.

## <a name="authenticating-the-user"></a>Ověřování uživatele

Po obdržení `user_code` `verification_uri`a , klient zobrazí tyto uživateli, pokyn k přihlášení pomocí svého mobilního telefonu nebo PC prohlížeče.

Pokud se uživatel ověří pomocí osobního účtu (na /common nebo /consumers), bude požádán o opětovné přihlášení, aby bylo možné přenést stav ověřování do zařízení.  Budou také požádáni o poskytnutí souhlasu, aby se ujistili, že jsou si vědomi udělených oprávnění.  To se nevztahuje na pracovní nebo školní účty používané k ověření.

Zatímco uživatel je ověřování na `verification_uri`, klient by `/token` měl být dotazování koncový `device_code`bod pro požadovaný token pomocí .

```
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| Parametr | Požaduje se | Popis|
| -------- | -------- | ---------- |
| `tenant`  | Požaduje se | Stejný alias klienta nebo klienta použitý v počáteční masce požadavku. |
| `grant_type` | Požaduje se | Musí být`urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Požaduje se | Musí odpovídat `client_id` použité v počáteční požadavku. |
| `device_code`| Požaduje se | Vráceno `device_code` v žádosti o autorizaci zařízení.  |

### <a name="expected-errors"></a>Očekávané chyby

Tok kódu zařízení je protokol dotazování, takže váš klient musí očekávat, že obdrží chyby před dokončením ověřování uživatelem.

| Chyba | Popis | Akce klienta |
| ------ | ----------- | -------------|
| `authorization_pending` | Uživatel nedokončil ověřování, ale nezrušil tok. | Opakujte požadavek `interval` po nejméně sekundách. |
| `authorization_declined` | Koncový uživatel zamítl žádost o autorizaci.| Zastavte dotazování a vraťte se do neověřeného stavu.  |
| `bad_verification_code`| Odeslaný `device_code` ke `/token` koncovému bodu nebyl rozpoznán. | Ověřte, zda klient `device_code` odesílá správné v požadavku. |
| `expired_token` | Uplynulo `expires_in` alespoň několik sekund a ověřování již `device_code`není možné s tímto . | Zastavte dotazování a vraťte se do neověřeného stavu. |

### <a name="successful-authentication-response"></a>Úspěšná odpověď na ověření

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

| Parametr | Formát | Popis |
| --------- | ------ | ----------- |
| `token_type` | Řetězec| Vždycky "Nosič. |
| `scope` | Řetězce oddělené mezerami | Pokud byl vrácen přístupový token, zobrazí se seznam oborů, pro které je přístupový token platný. |
| `expires_in`| int | Počet sekund před tím, než je zahrnutý přístupový token platný. |
| `access_token`| Neprůhledný řetězec | Vydáno pro [obory,](v2-permissions-and-consent.md) které byly požadovány.  |
| `id_token`   | JWT | Vydáno, pokud `scope` původní parametr `openid` zahrnoval obor.  |
| `refresh_token` | Neprůhledný řetězec | Vydáno, pokud `scope` je `offline_access`zahrnut původní parametr .  |

Obnovovací token můžete použít k získání nových přístupových tokenů a obnovovacích tokenů pomocí stejného toku zdokumentovaného v [dokumentaci toku kódu OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

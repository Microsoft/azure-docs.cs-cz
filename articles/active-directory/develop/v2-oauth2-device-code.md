---
title: Použití platformy Microsoft identity k přihlašování uživatelů v zařízeních bez prohlížeče | Azure
description: Pomocí udělení autorizace pro zařízení Sestavte integrované postupy ověřování a bez prohlížeče.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c948c59a90e0db17b4704188221cfc3c3d82310
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207602"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Microsoft Identity Platform a tok udělení autorizace zařízení OAuth 2,0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Platforma Microsoft identity podporuje [udělení autorizací zařízení](https://tools.ietf.org/html/rfc8628), které umožňuje uživatelům přihlásit se ke vstupním zařízením s omezeným použitím, jako je například inteligentní TV, zařízení IoT nebo tiskárna.  Pokud chcete tento tok povolit, zařízení uživateli navštíví webovou stránku v prohlížeči na jiném zařízení, abyste se přihlásili.  Jakmile se uživatel přihlásí, zařízení může získat přístupové tokeny a aktualizovat tokeny podle potřeby.  

Tento článek popisuje, jak programovat přímo s protokolem ve vaší aplikaci.  Pokud je to možné, doporučujeme místo toho použít podporované knihovny Microsoft Authentication Library (MSAL) k [získání tokenů a volání zabezpečených webových rozhraní API](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Podívejte se také na [ukázkové aplikace, které používají MSAL](sample-v2-code.md).

> [!NOTE]
> Koncový bod platformy Microsoft Identity Platform nepodporuje všechny Azure Active Directory scénáře a funkce. Pokud chcete zjistit, jestli byste měli použít koncový bod platformy Microsoft identity, přečtěte si informace o [omezeních platformy Microsoft Identity](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Diagram protokolu

Celý tok kódu zařízení vypadá podobně jako u dalšího diagramu. Každý z těchto kroků popisujeme dále v tomto článku.

![Tok kódu zařízení](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Žádost o autorizaci zařízení

Klient se musí nejdřív ověřit pomocí ověřovacího serveru pro zařízení a uživatelský kód, který se používá k inicializaci ověřování. Klient shromáždí tuto žádost z `/devicecode`ho koncového bodu. V této žádosti by měl klient také zahrnovat oprávnění, která potřebuje k získání od uživatele. Od chvíle, kdy se tato žádost pošle, se uživateli přihlásí jenom 15 minut (obvyklá hodnota pro `expires_in`), takže tuto žádost udělejte jenom v případě, že uživatel označil, že jsou připravené na přihlášení.

> [!TIP]
> Zkuste tento požadavek provést v nástroji post!
> [![zkuste tento požadavek spustit v nástroji post.](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parametr | Podmínka | Popis |
| --- | --- | --- |
| `tenant` | Požaduje se | Může být/běžné,/consumers nebo/Organizations.  Může to být také tenant adresáře, ze kterého chcete požádat o oprávnění ve formátu GUID nebo popisného názvu.  |
| `client_id` | Požaduje se | **ID aplikace (klienta)** , které [Azure Portal – registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) prostředí přiřazené k vaší aplikaci. |
| `scope` | Doporučené | Mezerou oddělený seznam [oborů](v2-permissions-and-consent.md) , ke kterým má uživatel udělit souhlas.  |

### <a name="device-authorization-response"></a>Odpověď na autorizaci zařízení

Úspěšná odpověď bude objekt JSON, který obsahuje požadované informace, aby se uživatel mohl přihlásit.  

| Parametr | Formát | Popis |
| ---              | --- | --- |
|`device_code`     | Řetězec | Dlouhý řetězec, který slouží k ověření relace mezi klientem a autorizačním serverem. Klient používá tento parametr k vyžádání přístupového tokenu z autorizačního serveru. |
|`user_code`       | Řetězec | Krátký řetězec zobrazený uživateli, který se používá k identifikaci relace v sekundárním zařízení.|
|`verification_uri`| Identifikátor URI | Identifikátor URI, na který by měl uživatel přejít pomocí `user_code`, aby se mohl přihlásit. |
|`expires_in`      | int | Počet sekund před `device_code` a vypršení platnosti `user_code`. |
|`interval`        | int | Počet sekund, po které má klient čekat mezi požadavky na dotazování. |
| `message`        | Řetězec | Uživatelsky čitelný řetězec s pokyny pro uživatele. To lze lokalizovat zahrnutím **parametru dotazu** do žádosti formuláře `?mkt=xx-XX`a vyplněním příslušného kódu jazykové kultury. |

> [!NOTE]
> V tuto chvíli není zahrnuto nebo není podporováno pole `verification_uri_complete` Response.  Uvádíme to proto, že pokud si přečtete [Standard](https://tools.ietf.org/html/rfc8628) , zjistíte, že `verification_uri_complete` je jako volitelná součást standardu toku kódu pro zařízení.

## <a name="authenticating-the-user"></a>Ověřování uživatele

Po přijetí `user_code` a `verification_uri`je klient zobrazuje uživateli a dává jim pokyn k tomu, aby se přihlásili pomocí svého mobilního telefonu nebo prohlížeče počítačů.

Pokud se uživatel ověřuje pomocí osobního účtu (na/běžné nebo/consumers), bude vyzván k opětovnému přihlášení, aby mohl přenést stav ověřování do zařízení.  Budou se taky požádáni o poskytnutí souhlasu, aby se ujistili, že mají informace o udělených oprávněních.  To se nevztahuje na pracovní nebo školní účty používané k ověřování. 

Při ověřování uživatele v `verification_uri`by měl klient dotazovat koncový bod `/token` pro požadovaný token pomocí `device_code`.

``` 
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| Parametr | Požaduje se | Popis|
| -------- | -------- | ---------- |
| `tenant`  | Požaduje se | Stejný tenant nebo alias tenanta použitý v počátečním požadavku. | 
| `grant_type` | Požaduje se | Musí být `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Požaduje se | Se musí shodovat s `client_id` použitým v počátečním požadavku. |
| `device_code`| Požaduje se | `device_code` vrácených v žádosti o autorizaci zařízení.  |

### <a name="expected-errors"></a>Očekávané chyby

Tok kódu zařízení je protokol cyklického dotazování, aby klient musel obdržet chyby, než se uživatel dokončí ověřováním.  

| Chyba | Popis | Akce klienta |
| ------ | ----------- | -------------|
| `authorization_pending` | Uživatel nedokončil ověřování, ale nezrušil tok. | Požadavek opakujte po nejméně `interval` sekund. |
| `authorization_declined` | Koncový uživatel zamítl žádost o autorizaci.| Zastaví cyklické dotazování a vrátí se do neověřeného stavu.  |
| `bad_verification_code`| `device_code` odeslané na koncový bod `/token` nebyl rozpoznán. | Ověřte, že klient posílá do žádosti správné `device_code`. |
| `expired_token` | Nejméně `expires_in` sekund uplynulo a ověřování již není u této `device_code`možné. | Zastaví cyklické dotazování a vrátí se do neověřeného stavu. |   

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

| Parametr | Formát | Popis |
| --------- | ------ | ----------- |
| `token_type` | Řetězec| Vždy "nosič". |
| `scope` | Řetězce oddělené mezerami | V případě vrácení přístupového tokenu zobrazí seznam oborů, pro které je přístupový token platný. |
| `expires_in`| int | Počet sekund, než je zahrnutý přístupový token platný pro. |
| `access_token`| Neprůhledný řetězec | Vydány pro požadované [obory](v2-permissions-and-consent.md) .  |
| `id_token`   | JWT | Vydáno, pokud původní parametr `scope` zahrnoval obor `openid`.  |
| `refresh_token` | Neprůhledný řetězec | Vydáno, pokud je `offline_access`původní parametr `scope`.  |

Obnovovací token můžete použít k získání nových přístupových tokenů a k aktualizaci tokenů pomocí stejného toku, který je popsán v [dokumentaci toku kódu OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  

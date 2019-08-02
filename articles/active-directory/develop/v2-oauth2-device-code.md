---
title: Použití platformy Microsoft identity k přihlašování uživatelů v zařízeních bez prohlížeče | Azure
description: Pomocí udělení kódu zařízení Sestavte toky pro ověřování Embedded a bez prohlížeče.
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
ms.date: 06/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 274c4e89ff3f996cc71cdacdfb7b5b72e813ae4b
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297662"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-code-flow"></a>Microsoft Identity Platform a tok kódu zařízení OAuth 2,0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Platforma Microsoft identity podporuje [udělení kódu zařízení](https://tools.ietf.org/html/draft-ietf-oauth-device-flow-12), které umožňuje uživatelům přihlašovat se ke vstupním zařízením omezeným na zařízení, jako je například inteligentní televizní vysílání, zařízení IoT nebo tiskárna.  Pokud chcete tento tok povolit, zařízení uživateli navštíví webovou stránku v prohlížeči na jiném zařízení, abyste se přihlásili.  Jakmile se uživatel přihlásí, zařízení může získat přístupové tokeny a aktualizovat tokeny podle potřeby.  

> [!IMPORTANT]
> V současné době koncový bod platformy Microsoft Identity Platform podporuje jenom tok zařízení pro klienty Azure AD, ale ne osobní účty.  To znamená, že musíte použít koncový bod nastavený jako tenant nebo `organizations` koncový bod.  Tato podpora bude brzy povolená. 
>
> Osobní účty, které jsou pozvány klientovi služby Azure AD, budou moci používat udělení toku zařízení, ale pouze v kontextu tenanta.
>
> V takovém případě není pole odpovědizahrnutoanipodporováno.`verification_uri_complete`  

> [!NOTE]
> Koncový bod platformy Microsoft Identity Platform nepodporuje všechny Azure Active Directory scénáře a funkce. Pokud chcete zjistit, jestli byste měli použít koncový bod platformy Microsoft identity, přečtěte si informace o [omezeních platformy Microsoft Identity](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Diagram protokolu

Celý tok kódu zařízení vypadá podobně jako u dalšího diagramu. Každý z těchto kroků popisujeme dále v tomto článku.

![Tok kódu zařízení](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Žádost o autorizaci zařízení

Klient se musí nejdřív ověřit pomocí ověřovacího serveru pro zařízení a uživatelský kód, který se používá k inicializaci ověřování. Klient shromáždí tuto žádost z `/devicecode` koncového bodu. V této žádosti by měl klient také zahrnovat oprávnění, která potřebuje k získání od uživatele. Od chvíle, kdy se tato žádost pošle, se uživateli přihlásí jenom 15 minut (obvyklá hodnota pro `expires_in`), takže tuto žádost udělejte jenom v případě, že uživatel označil, že jsou připravené na přihlášení.

> [!TIP]
> Zkuste tento požadavek provést v nástroji post!
> [![Pokus o spuštění této žádosti v předzálohovacím](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parametr | Podmínka | Popis |
| --- | --- | --- |
| `tenant` | Požadováno |Tenant adresáře, ze kterého chcete požádat o oprávnění. Může se jednat o formát GUID nebo popisný název.  |
| `client_id` | Požadováno | **ID aplikace (klienta)** , které [Azure Portal – registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) prostředí přiřazené k vaší aplikaci. |
| `scope` | Doporučené | Mezerou oddělený seznam [oborů](v2-permissions-and-consent.md) , ke kterým má uživatel udělit souhlas.  |

### <a name="device-authorization-response"></a>Odpověď na autorizaci zařízení

Úspěšná odpověď bude objekt JSON, který obsahuje požadované informace, aby se uživatel mohl přihlásit.  

| Parametr | Formát | Popis |
| ---              | --- | --- |
|`device_code`     | Řetězec | Dlouhý řetězec, který slouží k ověření relace mezi klientem a autorizačním serverem. Klient používá tento parametr k vyžádání přístupového tokenu z autorizačního serveru. |
|`user_code`       | Řetězec | Krátký řetězec zobrazený uživateli, který se používá k identifikaci relace v sekundárním zařízení.|
|`verification_uri`| Identifikátor URI | Identifikátor URI, který by měl uživatel přejít `user_code` na, aby se mohl přihlásit. |
|`expires_in`      | int | Počet sekund před `device_code` vypršením platnosti a `user_code` . |
|`interval`        | int | Počet sekund, po které má klient čekat mezi požadavky na dotazování. |
| `message`        | Řetězec | Uživatelsky čitelný řetězec s pokyny pro uživatele. To lze lokalizovat zahrnutím **parametru dotazu** do žádosti formuláře `?mkt=xx-XX`a vyplněním příslušného kódu jazykové kultury. |

## <a name="authenticating-the-user"></a>Ověřování uživatele

Po přijetí `user_code` a `verification_uri`se klient zobrazí uživateli, který jim dává pokyn k přihlášení pomocí svého mobilního telefonu nebo prohlížeče počítače.  Kromě toho může klient použít kód QR nebo podobný mechanizmus k zobrazení `verfication_uri_complete`, který provede krok `user_code` zadání pro uživatele.

Při ověřování uživatele v `verification_uri`systému by měl klient dotazovat `/token` koncový bod požadovaného tokenu pomocí `device_code`.

``` 
POST https://login.microsoftonline.com/tenant/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8
```

| Parametr | Požadováno | Popis|
| -------- | -------- | ---------- |
| `grant_type` | Požadováno | Musí být`urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Požadováno | Musí odpovídat `client_id` použitému v počátečním požadavku. |
| `device_code`| Požadováno | `device_code` Vrácený v žádosti o autorizaci zařízení.  |

### <a name="expected-errors"></a>Očekávané chyby

Tok kódu zařízení je protokol cyklického dotazování, aby klient musel obdržet chyby, než se uživatel dokončí ověřováním.  

| Chyba | Popis | Akce klienta |
| ------ | ----------- | -------------|
| `authorization_pending` | Uživatel nedokončil ověřování, ale nezrušil tok. | Požadavek opakujte po nejméně `interval` sekundách. |
| `authorization_declined` | Koncový uživatel zamítl žádost o autorizaci.| Zastaví cyklické dotazování a vrátí se do neověřeného stavu.  |
| `bad_verification_code`| `device_code` Odeslání`/token` do koncového bodu nebylo rozpoznáno. | Ověřte, že klient posílá v žádosti správné `device_code` požadavky. |
| `expired_token` | Nejméně `expires_in` sekund uplynulo a ověřování již není s tímto důvodem `device_code`možné. | Zastaví cyklické dotazování a vrátí se do neověřeného stavu. |

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
| `access_token`| Neprůhledný řetězec | Vydány pro [](v2-permissions-and-consent.md) požadované obory.  |
| `id_token`   | JWT | Vydáno, pokud původní `scope` parametr `openid` zahrnoval obor.  |
| `refresh_token` | Neprůhledný řetězec | Vydáno, pokud je `scope` zahrnut `offline_access`původní parametr.  |

Obnovovací token můžete použít k získání nových přístupových tokenů a k aktualizaci tokenů pomocí stejného toku, který je popsán v [dokumentaci toku kódu OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  

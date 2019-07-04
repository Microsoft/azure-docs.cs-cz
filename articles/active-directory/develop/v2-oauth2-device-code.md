---
title: Platforma identit Microsoft používá k přihlášení uživatelů na zařízeních bez prohlížeče | Azure
description: Vytváření toků vložené a prohlížeče bez ověřování pomocí udělení kódu zařízení.
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
ms.openlocfilehash: e92e4d0e296e83b413cfd2a67041a5749c16699e
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482232"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-code-flow"></a>Platforma identit Microsoft a tok OAuth 2.0 zařízení kódu

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Podporuje Microsoft identity platform [udělení kódu zařízení](https://tools.ietf.org/html/draft-ietf-oauth-device-flow-12), což umožňuje uživatelům umožní přihlásit k omezené vstupní zařízení, jako jsou inteligentní TV, zařízení IoT nebo tiskárny.  Pokud chcete povolit tento tok, má zařízení uživatele navštivte webovou stránku ve svém prohlížeči na jiném zařízení pro přihlášení.  Když se uživatel přihlásí, zařízení je možné získat přístupové tokeny a podle potřeby obnovovacích tokenů.  

> [!IMPORTANT]
> V tuto chvíli podporuje koncový bod Microsoft identity platform tok zařízení pouze pro klienty Azure AD, ale ne osobní účty.  To znamená, že je nutné použít koncový bod nastavit jako tenant nebo `organizations` koncového bodu.  Tato podpora bude brzy povolená. 
>
> Osobní účty, které se pozvat do tenanta služby Azure AD bude moct používat toku udělení zařízení, ale pouze v rámci tenanta.
>
> Jako další Všimněte si `verification_uri_complete` pole odpovědi není zahrnutý nebo v tuto chvíli nepodporuje.  

> [!NOTE]
> Koncový bod Microsoft identity platform nepodporuje všechny scénáře Azure Active Directory a funkce. Pokud chcete zjistit, zda by měl použít koncový bod Microsoft identity platform, přečtěte si informace o [Microsoft identity platform omezení](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Diagram protokolu

Následující diagram podobný tok kódu celého zařízení. Každý z kroků dále v tomto článku popisujeme.

![Tok kódu při zařízení](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Žádost o ověření zařízení

Nejdřív musíte zkontrolovat klienta se serverem ověřování pro zařízení a uživatelský kód, který je použitý k provedení ověřování. Shromažďuje této žádosti z klienta `/devicecode` koncového bodu. V této žádosti klienta by měly také zahrnovat oprávnění, která je potřeba získat od uživatele. Odesláním požadavku v okamžiku, že uživatel má pouze 15 minut pro přihlášení (obvykle hodnota `expires_in`), takže pouze tuto žádost vytvořit, když uživatel uvedl, budou připravené k přihlášení.

> [!TIP]
> Pokuste se spustit tuto žádost do Postman!
> [![Zkuste spustit tento požadavek v nástroji Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parametr | Podmínka | Popis |
| --- | --- | --- |
| `tenant` | Požaduje se |Tenantu Active directory, kterou chcete požádat o oprávnění. To může být ve formátu popisný název nebo identifikátor GUID.  |
| `client_id` | Požaduje se | **ID aplikace (klient)** , který [webu Azure portal – registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) prostředí přiřazené vaší aplikaci. |
| `scope` | Doporučené | Místo oddělený seznam [obory](v2-permissions-and-consent.md) , že má uživatel vyjádřit souhlas.  |

### <a name="device-authorization-response"></a>Zařízení odpovědi ověřování.

Úspěšná odpověď bude objekt JSON obsahující požadované informace umožňující uživateli umožní přihlásit.  

| Parametr | Formát | Popis |
| ---              | --- | --- |
|`device_code`     | String | Dlouhý řetězec používaného k ověřování relací mezi klientem a serverem ověřování. Klient použije tento parametr k žádosti o přístupový token od autorizačního serveru. |
|`user_code`       | String | Krátký řetězec, který se zobrazí uživateli, který se používá k identifikaci relaci na sekundární zařízení.|
|`verification_uri`| Identifikátor URI | Identifikátor URI by měl uživatel přejde na s `user_code` abyste se mohli přihlásit. |
|`expires_in`      | int | Počet sekund před `device_code` a `user_code` vypršení platnosti. |
|`interval`        | int | Počet sekund, po které klient by měl čekat mezi požadavků dotazování. |
| `message`        | String | Čitelný řetězec s pokyny pro uživatele. To může být lokalizována zahrnutím **parametr dotazu** v požadavku ve formátu `?mkt=xx-XX`, plnění v příslušné jazykové jazykovou verzi kódu. |

## <a name="authenticating-the-user"></a>Ověření uživatele

Po přijetí `user_code` a `verification_uri`, klient se zobrazí tyto uživateli, pokynem k přihlášení pomocí jejich mobilní telefon nebo PC prohlížeče.  Kromě toho může klient použít kód QR nebo mechanismus podobný zobrazíte `verfication_uri_complete`, což bude trvat krok zadávání `user_code` pro uživatele.

Když se uživatel ověřuje na `verification_uri`, klient by měl být cyklického dotazování `/token` koncový bod pro požadovaný token pomocí `device_code`.

``` 
POST https://login.microsoftonline.com/tenant/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8
```

| Parametr | Požaduje se | Popis|
| -------- | -------- | ---------- |
| `grant_type` | Požaduje se | musí být `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Požaduje se | Musí odpovídat `client_id` použít v prvotní žádosti. |
| `device_code`| Požaduje se | `device_code` Vráceny v žádosti o ověření zařízení.  |

### <a name="expected-errors"></a>Očekávaný chyby

Tok kódu zařízení je protokol dotazování, takže váš klient musí by se měl zobrazit chyby předtím, než uživatel dokončí ověřování.  

| Chyba | Popis | Akce klienta |
| ------ | ----------- | -------------|
| `authorization_pending` | Uživatel nebyl dokončen, ověřování, ale nebyla zrušena toku. | Opakovat žádost po nejméně `interval` sekund. |
| `authorization_declined` | Koncový uživatel zamítl žádost o autorizaci.| Zastavit cyklického dotazování a vrátit se do neověřené stavu.  |
| `bad_verification_code`| `device_code` Odesílat `/token` koncový bod nebyl rozpoznán. | Ověřte, že klient zasílá správné `device_code` v požadavku. |
| `expired_token` | Alespoň `expires_in` prošly sekund a ověřování se už nedají pomocí to `device_code`. | Zastavit cyklického dotazování a vrátit se do neověřené stavu. |

### <a name="successful-authentication-response"></a>Ověření úspěšné odpovědi

Úspěšné odpovědi tokenu bude vypadat takto:

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
| `token_type` | String| Vždy "nosiče. |
| `scope` | Oddělené mezerou řetězce | Pokud byl vrácen přístupového tokenu, vypíšou se přístupový token je platný pro obory. |
| `expires_in`| int | Počet sekund před zahrnuté přístupový token je platný pro. |
| `access_token`| Neprůhledný řetězec | Vydaný pro [obory](v2-permissions-and-consent.md) , které bylo vyžádáno.  |
| `id_token`   | JWT | Vydaný if původní `scope` parametr zahrnuté `openid` oboru.  |
| `refresh_token` | Neprůhledný řetězec | Pokud vydaných původní `scope` parametr `offline_access`.  |

Token obnovení můžete použít k získání nových přístupových tokenů a obnovovacích tokenů pomocí stejný tok dokumentovány v článku [dokumentace ke službě flow kód OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  

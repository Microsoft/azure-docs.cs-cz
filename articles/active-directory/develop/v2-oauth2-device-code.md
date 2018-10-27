---
title: Použití služby Azure AD v2.0 pro přihlášení uživatele v zařízení prohlížeč bez | Dokumentace Microsoftu
description: Vytváření toků vložené a prohlížeče bez ověřování pomocí udělení kódu zařízení.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 780eec4d-7ee1-48b7-b29f-cd0b8cb41ed3
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: ceafee3ac3453590d1687f662c7f3c9a8d8b4e0c
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50156625"
---
# <a name="azure-active-directory-v20-and-the-oauth-20-device-code-flow"></a>Azure Active Directory v2.0 a tok OAuth 2.0 zařízení kódu

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Azure AD podporuje [udělení kódu zařízení](https://tools.ietf.org/html/draft-ietf-oauth-device-flow-12), což umožňuje uživatelům umožní přihlásit k omezené vstupní zařízení, jako jsou inteligentní TV, zařízení IoT nebo tiskárny.  Pokud chcete povolit tento tok, má zařízení uživatele navštivte webovou stránku ve svém prohlížeči na jiném zařízení pro přihlášení.  Když se uživatel přihlásí, zařízení je možné získat přístupové tokeny a podle potřeby obnovovacích tokenů.  

> [!Important] 
> V současné době podporuje koncový bod v2.0 tok zařízení pouze pro klienty Azure AD, ale ne osobní účty.  To znamená, že je třeba použít koncový bod bezproblémovým nebo koncový bod organizace.  
>
> Osobní účty, které se pozvat do tenanta služby Azure AD bude moct používat toku udělení zařízení, ale pouze v rámci tenanta.

> [!NOTE]
> Koncový bod v2.0 nepodporuje všechny scénáře Azure Active Directory a funkce. Pokud chcete zjistit, zda by měl použít koncový bod verze 2.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).
>

## <a name="protocol-diagram"></a>Diagram protokolu

Následující diagram podobný tok kódu celého zařízení. Každý z kroků dále v tomto článku popisujeme.

![Tok kódu při zařízení](media/v2-oauth2-device-flow/v2-oauth-device-flow.png)

## <a name="device-authorization-request"></a>Žádost o ověření zařízení

Nejdřív musíte zkontrolovat klienta se serverem ověřování pro zařízení a uživatelský kód použitý k provedení ověřování.  Shromažďuje této žádosti z klienta `/devicecode` koncového bodu. V této žádosti klienta by měly také zahrnovat oprávnění, která je potřeba získat od uživatele.  Odesláním požadavku v okamžiku, že uživatel má pouze 15 minut pro přihlášení (obvykle hodnota `expires_in`), takže pouze tuto žádost vytvořit, když uživatel uvedl, budou připravené k přihlášení.

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parametr | Podmínka | Popis |
| --- | --- | --- |
| tenant |Požaduje se |Tenantu Active directory, kterou chcete požádat o oprávnění. To může být ve formátu popisný název nebo identifikátor GUID.  |
| client_id |Požaduje se |ID aplikace, které [portál pro registraci aplikací](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) přiřazené vaší aplikaci. |
| scope | Doporučené | Místo oddělený seznam [obory](v2-permissions-and-consent.md) , že má uživatel vyjádřit souhlas.  |

### <a name="device-authorization-response"></a>Zařízení odpovědi ověřování.

Úspěšná odpověď bude objekt JSON obsahující požadované informace umožňující uživateli umožní přihlásit.  

| Parametr | Formát | Popis |
| ---              | --- | --- |
|`device_code`     |Řetězec| Dlouhý řetězec používaného k ověřování relací mezi klientem a serverem ověřování.  Používá se pomocí klienta k žádosti o přístupový token od autorizačního serveru. |
|`user_code`       |Řetězec| Krátký řetězec, který se zobrazí uživateli, používá k identifikaci relaci na sekundární zařízení.|
|`verification_uri`|URI| Identifikátor URI by měl uživatel přejde na s `user_code` abyste se mohli přihlásit. |
|`verification_uri_complete`|URI| Kombinace identifikátoru URI `user_code` a `verification_uri`, která se používá pro jiné textové přenos na uživatele (například přes Bluetooth na zařízení nebo kódu QR).  |
|`expires_in`      |int| Počet sekund před `device_code` a `user_code` vypršení platnosti. |
|`interval`        |int| Počet sekund, po které klient by měl čekat mezi požadavků dotazování. |
| `message`        |Řetězec| Čitelný řetězec s pokyny pro uživatele.  To může být lokalizována zahrnutím **parametr dotazu** v požadavku ve formátu `?mkt=xx-XX`, plnění v příslušné jazykové jazykovou verzi kódu. |

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

|Parametr | Požaduje se | Popis|
| -------- | -------- | ---------- |
|`grant_type` | Požaduje se| musí být `urn:ietf:params:oauth:grant-type:device_code`|
|`client_id`  | Požaduje se| Musí odpovídat `client_id` použít v prvotní žádosti. |
|`device_code`| Požaduje se| `device_code` Vráceny v žádosti o ověření zařízení.  |

### <a name="expected-errors"></a>Očekávaný chyby

Vzhledem k tomu, že tok kódu zařízení je protokol pro dotazování, klient musí by se měl zobrazit chyby předtím, než uživatel dokončí ověřování.  

| Chyba | Popis | Akce klienta |
|------ | ----------- | -------------|
| `authorization_pending` |  Uživatel ještě nebyla dokončena ověřování, ale nebyla zrušena toku. | Opakovat žádost po nejméně `interval` sekund. |
| `authorization_declined`|  Koncový uživatel zamítl žádost o autorizaci.| Zastavit cyklického dotazování a vrátit se do neověřené stavu.  |
| `bad_verification_code`|`device_code` Odesílat `/token` koncový bod nebyl rozpoznán. | Ověřte, že klient zasílá správné `device_code` v požadavku. |
| `expired_token`|  Alespoň `expires_in` prošly sekund a ověřování se už nedají pomocí to `device_code`. | Zastavit cyklického dotazování a vrátit se do neověřené stavu. |


### <a name="succesful-authentication-response"></a>Odpověď ověřování úspěšné.

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
|`token_type` | Řetězec| Vždy "nosiče. |
|`scope` | Oddělené mezerou řetězce | Pokud byl vrácen přístupového tokenu, vypíšou se přístupový token je platný pro obory. |
|`expires_in`| int | Počet sekund před zahrnuté přístupový token je platný pro. |
|`access_token`| Neprůhledný řetězec | Vydaný pro [obory](v2-permissions-and-consent.md) , které bylo vyžádáno.  |
|`id_token`   | JWT | Vydaný if původní `scope` parametr zahrnuté `openid` oboru.  |
|`refresh_token` | Neprůhledný řetězec | Pokud vydaných původní `scope` parametr `offline_access`.  |

Token obnovení lze použít k získání nových přístupových tokenů a obnovovacích tokenů stejný tok podrobně popisuje použití [dokumentace ke službě flow kód OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  
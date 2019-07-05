---
title: Platforma identit Microsoft používá k přihlášení uživatelů pomocí udělení přihlašovacích údajů (ROPC) hesla vlastníka prostředku | Azure
description: Podpora prohlížeče bez ověřování toky pomocí udělení přihlašovacích údajů heslo vlastníka prostředku.
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
ms.date: 04/20/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: da111311de7b873be6453862ffcbd56fe546ea7f
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482388"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-resource-owner-password-credential"></a>Platforma identit Microsoft a přístupové heslo vlastníka prostředku OAuth 2.0

Microsoft identity platform podporuje [udělit pověření heslo vlastníka prostředku (ROPC)](https://tools.ietf.org/html/rfc6749#section-4.3), který umožňuje aplikaci přihlásit uživatele pomocí přímo zpracování své heslo. Tok ROPC vyžaduje vysoký stupeň důvěryhodnosti a uživatel vystavení a tento tok měli používat jenom v případě, že toky, které bezpečnější, nelze použít.

> [!IMPORTANT]
>
> * Koncový bod Microsoft identity platform podporuje pouze ROPC pro klienty Azure AD, ne osobní účty. To znamená, že je nutné použít koncový bod specifickým pro tenanta (`https://login.microsoftonline.com/{TenantId_or_Name}`) nebo `organizations` koncového bodu.
> * Osobní účty, které se pozvat do tenanta služby Azure AD nemůže použít ROPC.
> * Účty, které nemají hesla prostřednictvím ROPC přihlásit. V tomto scénáři doporučujeme místo toho použít odlišný tok pro vaši aplikaci.
> * Pokud uživatelé muset použít vícefaktorové ověřování (MFA) pro přihlášení k aplikaci, že budou Blokovaní místo.

## <a name="protocol-diagram"></a>Diagram protokolu

Následující diagram znázorňuje tok ROPC.

![Diagram znázorňující tok přihlašovacích údajů heslo vlastníka prostředku](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Žádost o autorizaci

Tok ROPC je jeden požadavek&mdash;odešle klientovi identifikace a přihlašovací údaje uživatele pro zprostředkovatele identity a potom na oplátku přijímá tokeny. Klient musí požádat e-mailovou adresu uživatele (UPN) a heslo, než to uděláte. Ihned po úspěšné žádosti by měl klient zabezpečenému vydávání přihlašovacích údajů uživatele z paměti. To je nikdy uložte.

> [!TIP]
> Pokuste se spustit tuto žádost do Postman!
> [![Zkuste spustit tento požadavek v nástroji Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```
// Line breaks and spaces are for legibility only.

POST {tenant}/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| Parametr | Podmínka | Popis |
| --- | --- | --- |
| `tenant` | Požaduje se | Tenantu Active directory, kterou chcete uživatele do protokolu. To může být ve formátu popisný název nebo identifikátor GUID. Tento parametr nelze nastavit na `common` nebo `consumers`, ale může být nastavená na `organizations`. |
| `grant_type` | Požaduje se | Musí být nastaveno na `password`. |
| `username` | Požaduje se | E-mailovou adresu uživatele. |
| `password` | Požaduje se | Heslo uživatele. |
| `scope` | Doporučené | Místo oddělený seznam [obory](v2-permissions-and-consent.md), nebo oprávnění, které aplikace vyžaduje. Ve interaktivního toku správce nebo uživatel musí vyjádřit souhlas. do těchto oborů předem. |

### <a name="successful-authentication-response"></a>Ověření úspěšné odpovědi

Následující příklad ukazuje úspěšné odpovědi tokenu:

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
| `token_type` | String | Vždy nastaven na hodnotu `Bearer`. |
| `scope` | Oddělené mezerou řetězce | Pokud byl vrácen přístupového tokenu, tento parametr obsahuje obory, které je přístupový token platný pro. |
| `expires_in`| int | Počet sekund, po které je součástí přístupový token platný pro. |
| `access_token`| Neprůhledný řetězec | Vydaný pro [obory](v2-permissions-and-consent.md) , které bylo vyžádáno. |
| `id_token` | JWT | Vydaný if původní `scope` parametr zahrnuté `openid` oboru. |
| `refresh_token` | Neprůhledný řetězec | Pokud vydaných původní `scope` parametr `offline_access`. |

Token obnovení můžete použít k získání nových přístupových tokenů a obnovovacích tokenů pomocí stejný tok podle [dokumentace ke službě flow kód OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Odpověď na chybu

Pokud uživatel není k dispozici správné uživatelské jméno nebo heslo, nebo že nedostal klienta požadovanou souhlas, ověření se nezdaří.

| Chyba | Popis | Akce klienta |
|------ | ----------- | -------------|
| `invalid_grant` | Ověřování se nezdařilo. | Přihlašovací údaje byly nesprávná nebo klient nemá souhlas pro požadované obory. Pokud nejsou obory, `consent_required` se vrátí chyba. Pokud k tomu dojde, klient by měl uživatele poslat na výzvu k interaktivní pomocí webview nebo prohlížeče. |
| `invalid_request` | Požadavek byl nesprávně vytvořen. | Typ udělení oprávnění nepodporuje `/common` nebo `/consumers` kontextu ověřování.  Místo nich se používá `/organizations`. |
| `invalid_client` | Aplikace je nesprávně nastavený | K tomu může dojít, pokud `allowPublicClient` vlastnost není nastavena na hodnotu true v [manifest aplikace](reference-app-manifest.md). `allowPublicClient` Vlastnost je potřeba, proto ROPC udělení nemá identifikátor URI přesměrování. Azure AD nemůže určit, pokud je aplikace veřejným klientem aplikace nebo aplikace důvěrnému klientovi, pokud je nastavena. ROPC je podporována pouze pro veřejné klientské aplikace. |

## <a name="learn-more"></a>Víc se uč

* Vyzkoušejte si ROPC sami pomocí [ukázková Konzolová aplikace](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* Pokud chcete zjistit, zda by měl použít koncový bod verze 2.0, přečtěte si informace o [Microsoft identity platform omezení](active-directory-v2-limitations.md).

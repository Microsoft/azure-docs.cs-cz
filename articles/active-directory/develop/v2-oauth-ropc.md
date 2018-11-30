---
title: Pomocí služby Azure AD v2.0 přihlásit uživatele pomocí ROPC | Dokumentace Microsoftu
description: Podpora prohlížeče bez ověřování toky pomocí udělení přihlašovacích údajů heslo vlastníka prostředku.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 3beda2dc9214900a11bdd4422637f7dec3ce4924
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52500265"
---
# <a name="azure-active-directory-v20-and-the-oauth-20-resource-owner-password-credential"></a>Azure Active Directory v2.0 a přístupové heslo vlastníka prostředku OAuth 2.0

Podporuje Azure Active Directory (Azure AD) [udělit pověření heslo vlastníka prostředku (ROPC)](https://tools.ietf.org/html/rfc6749#section-4.3), který umožňuje aplikaci přihlásit uživatele pomocí přímo zpracování své heslo. Tok ROPC vyžaduje vysoký stupeň důvěryhodnosti a uživatel vystavení a vývojáři měli používat jenom tento tok při toky, které bezpečnější, nemohou být použity.

> [!Important]
> * Koncový bod Azure AD v2.0 podporuje pouze ROPC pro klienty Azure AD, ne osobní účty. To znamená, že je nutné použít koncový bod specifickým pro tenanta (`https://login.microsoftonline.com/{TenantId_or_Name}`) nebo `organizations` koncového bodu.
> * Osobní účty, které se pozvat do tenanta služby Azure AD nemůže použít ROPC.
> * Účty, které nemají hesla prostřednictvím ROPC přihlásit. V tomto scénáři doporučujeme místo toho použít odlišný tok pro vaši aplikaci.
> * Pokud uživatelé muset použít vícefaktorové ověřování (MFA) pro přihlášení k aplikaci, že budou Blokovaní místo.

## <a name="protocol-diagram"></a>Diagram protokolu

Následující diagram znázorňuje tok ROPC.

![ROPC toku](media/v2-oauth2-ropc/v2-oauth-ropc.png)

## <a name="authorization-request"></a>Žádost o autorizaci

Tok ROPC je jeden požadavek&mdash;odešle klientovi identifikace a přihlašovací údaje uživatele pro zprostředkovatele identity a potom na oplátku přijímá tokeny. Klient musí požádat e-mailovou adresu uživatele (UPN) a heslo, než to uděláte. Ihned po úspěšné žádosti by měl klient zabezpečenému vydávání přihlašovacích údajů uživatele z paměti. To je nikdy uložte.

```
// Line breaks and spaces are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&client_secret=wkubdywbc2894u
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
| `scope` | Doporučené | Místo oddělený seznam [obory](v2-permissions-and-consent.md), nebo oprávnění, které aplikace vyžaduje. Tyto rozsahy musí vyjádřit souhlas předem správcem nebo uživatelem v interaktivní tok. |

### <a name="successful-authentication-response"></a>Ověření úspěšné odpovědi

Následuje příklad úspěšné odpovědi tokenu:

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
| `token_type` | Řetězec | Vždy nastaven na hodnotu `Bearer`. |
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
| `invalid_grant` | Ověřování se nezdařilo. | Přihlašovací údaje byly nesprávná nebo klient nemá souhlas pro požadované obory. Pokud nejsou obory, `consent_required` suberror bude vrácen. Pokud k tomu dojde, klient by měl uživatele poslat na výzvu k interaktivní pomocí webview nebo prohlížeče. |
| `invalid_request` | Požadavek byl nesprávně vytvořen. | Typ udělení oprávnění nepodporuje `/common` nebo `/consumers` kontextu ověřování.  Použití `/organizations` místo. |
| `invalid_client` | Aplikace je nesprávně nastavený | K tomu může dojít, pokud `allowPublicClient` vlastnost není nastavena na hodnotu true v [manifest aplikace](reference-app-manifest.md). `allowPublicClient` Vlastnost je potřeba, proto ROPC udělení nemá identifikátor URI přesměrování. Azure AD nemůže určit, pokud je aplikace veřejným klientem aplikace nebo aplikace důvěrnému klientovi, pokud je nastavena. Všimněte si, že ROPC je podporována pouze pro veřejné klientské aplikace. |

## <a name="learn-more"></a>Další informace

* Vyzkoušejte si ROPC sami pomocí [ukázková Konzolová aplikace](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* Pokud chcete zjistit, zda by měl použít koncový bod verze 2.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).

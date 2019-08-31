---
title: Použijte Microsoft Identity Platform k přihlašování uživatelů pomocí udělení přihlašovacích údajů pro heslo vlastníka prostředku (ROPC) | Azure
description: Podporuje toky ověřování bez prohlížeče pomocí udělení přihlašovacích údajů pro heslo vlastníka prostředku.
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
ms.date: 08/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d5324aba5202abb76f07d1eaf43fe214e690393
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193202"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-resource-owner-password-credential"></a>Microsoft Identity Platform a přihlašovací údaje pro heslo vlastníka prostředku OAuth 2,0

Platforma Microsoft Identity Platform podporuje [udělení přihlašovacích údajů pro heslo vlastníka prostředku (ROPC)](https://tools.ietf.org/html/rfc6749#section-4.3), které aplikaci umožňuje přihlašovat uživatele přímo při zpracování hesla. Tok ROPC vyžaduje vysoký stupeň důvěry a ohrožení uživatele a tento tok byste měli používat jenom v případě, že se jedná o jiné, bezpečnější toky, které se nedají použít.

> [!IMPORTANT]
>
> * Koncový bod Microsoft Identity Platform podporuje jenom ROPC pro klienty Azure AD, ne pro osobní účty. To znamená, že musíte použít koncový bod (`https://login.microsoftonline.com/{TenantId_or_Name}`) `organizations` nebo koncový bod specifický pro klienta.
> * Osobní účty, které jsou pozvány klientovi služby Azure AD, nemůžou používat ROPC.
> * Účty, které nemají hesla, se nemůžou přihlásit přes ROPC. Pro tento scénář doporučujeme místo toho použít jiný tok pro aplikaci.
> * Pokud uživatelé potřebují k přihlášení k aplikaci použít vícefaktorové ověřování (MFA), místo toho se zablokují.

## <a name="protocol-diagram"></a>Diagram protokolu

V následujícím diagramu je znázorněný ROPC tok.

![Diagram znázorňující tok přihlašovacích údajů k heslům vlastníka prostředku](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Žádost o autorizaci

ROPC flow je jeden požadavek: pošle identifikaci klienta a přihlašovací údaje uživatele do IDP a pak obdrží tokeny při návratu. Klient musí před tím, než to uděláte, požádat o e-mailovou adresu uživatele (UPN) a heslo. Ihned po úspěšné žádosti by měl klient bezpečně uvolnit přihlašovací údaje uživatele z paměti. Nesmí je nikdy Uložit.

> [!TIP]
> Zkuste tento požadavek provést v nástroji post!
> [![Pokus o spuštění této žádosti v předzálohovacím](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```
// Line breaks and spaces are for legibility only.  This is a public client, so no secret is required. 

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
| `tenant` | Požadováno | Tenant adresáře, do kterého chcete uživatele přihlašovat. Může se jednat o formát GUID nebo popisný název. Tento parametr nemůže být nastaven na `common` hodnotu `consumers`nebo, ale může být nastaven `organizations`na hodnotu. |
| `client_id` | Požadováno | ID aplikace (klienta), ke které se stránka [Azure Portal registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) přiřazená vaší aplikaci. | 
| `grant_type` | Požadováno | Musí být nastaveno na `password`. |
| `username` | Požadováno | E-mailová adresa uživatele |
| `password` | Požadováno | Heslo uživatele. |
| `scope` | Doporučené | Mezerou oddělený seznam [oborů](v2-permissions-and-consent.md)nebo oprávnění, které aplikace vyžaduje. V interaktivním toku musí správce nebo uživatel na tyto obory vyjádřit svůj souhlas předem. |
| `client_secret`| Někdy vyžadováno | Pokud je vaše aplikace veřejným klientem, `client_secret` nelze ji zahrnout nebo. `client_assertion`  Pokud je aplikace důvěrného klienta, musí být součástí. | 
| `client_assertion` | Někdy vyžadováno | Jiná forma `client_secret`, generovaná pomocí certifikátu.  Další podrobnosti najdete v tématu [přihlašovací údaje k certifikátu](active-directory-certificate-credentials.md) . | 

### <a name="successful-authentication-response"></a>Úspěšná ověřovací odpověď

Následující příklad ukazuje úspěšnou odpověď tokenu:

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
| `token_type` | Řetězec | Vždy nastavte na `Bearer`. |
| `scope` | Řetězce oddělené mezerami | Pokud byl vrácen přístupový token, tento parametr vypíše obory, pro které je přístupový token platný. |
| `expires_in`| int | Počet sekund, po který je zahrnutý přístupový token platný |
| `access_token`| Neprůhledný řetězec | Vydány pro [](v2-permissions-and-consent.md) požadované obory. |
| `id_token` | JWT | Vydáno, pokud původní `scope` parametr `openid` zahrnoval obor. |
| `refresh_token` | Neprůhledný řetězec | Vydáno, pokud je `scope` zahrnut `offline_access`původní parametr. |

Pomocí obnovovacího tokenu můžete získat nové přístupové tokeny a aktualizovat tokeny pomocí stejného toku popsaného v [dokumentaci ke službě Flow Code OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Chybová odezva

Pokud uživatel nezadal správné uživatelské jméno nebo heslo nebo pokud klient neobdržel požadovaný souhlas, ověřování se nezdaří.

| Chyba | Popis | Akce klienta |
|------ | ----------- | -------------|
| `invalid_grant` | Ověřování se nezdařilo. | Přihlašovací údaje byly nesprávné nebo klient nemá souhlas pro požadované obory. Pokud nejsou obory uděleny, `consent_required` bude vrácena chyba. Pokud k tomu dojde, klient by měl odeslat uživateli interaktivní výzvu pomocí webového zobrazení nebo prohlížeče. |
| `invalid_request` | Požadavek byl nesprávně vytvořen. | Typ grantu není podporován u `/common` kontextů ověřování nebo. `/consumers`  Místo `/organizations` toho použijte nebo ID tenanta. |

## <a name="learn-more"></a>Víc se uč

* Vyzkoušejte si ROPC pro sebe pomocí [ukázkové konzolové aplikace](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* Pokud chcete zjistit, jestli byste měli použít koncový bod v 2.0, přečtěte si o [omezeních platformy Microsoft Identity](active-directory-v2-limitations.md).

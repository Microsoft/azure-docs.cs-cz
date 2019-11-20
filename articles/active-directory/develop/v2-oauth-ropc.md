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
ms.date: 10/11/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 322e0e5f740bd416c7831f32e0d74f9290335fe3
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74195749"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-resource-owner-password-credentials"></a>Přihlašovací údaje pro heslo vlastníka prostředku Microsoft Identity Platform a OAuth 2,0

Platforma Microsoft Identity Platform podporuje [udělení přihlašovacích údajů k heslu (ROPC) OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-4.3), což aplikaci umožňuje přihlašovat uživatele přímo pomocí manipulace s heslem.

> [!WARNING]
> Microsoft doporučuje, abyste tok ROPC _nepoužívali_ . Ve většině scénářů jsou k dispozici a doporučovány bezpečnější alternativy. Tento tok vyžaduje v aplikaci velmi vysoký stupeň důvěry a přináší rizika, která nejsou přítomna v jiných tocích. Tento tok byste měli použít jenom v případě, že se nedají použít jiné bezpečnější toky.

> [!IMPORTANT]
>
> * Koncový bod Microsoft Identity Platform podporuje jenom ROPC pro klienty Azure AD, ne pro osobní účty. To znamená, že musíte použít koncový bod specifický pro klienta (`https://login.microsoftonline.com/{TenantId_or_Name}`) nebo koncový bod `organizations`.
> * Osobní účty, které jsou pozvány klientovi služby Azure AD, nemůžou používat ROPC.
> * Účty, které nemají hesla, se nemůžou přihlásit přes ROPC. Pro tento scénář doporučujeme místo toho použít jiný tok pro aplikaci.
> * Pokud uživatelé potřebují k přihlášení k aplikaci použít vícefaktorové ověřování (MFA), místo toho se zablokují.
> * ROPC se v [hybridních scénářích federace identit](/azure/active-directory/hybrid/whatis-fed) nepodporují (například Azure AD a ADFS používané k ověřování místních účtů). Pokud jsou uživatelé na celé stránce přesměrováni na místní zprostředkovatele identity, Azure AD nemůže testovat uživatelské jméno a heslo proti tomuto zprostředkovateli identity. [Předávací ověřování](/azure/active-directory/hybrid/how-to-connect-pta) je však podporováno v ROPC.

## <a name="protocol-diagram"></a>Diagram protokolu

V následujícím diagramu je znázorněný ROPC tok.

![Diagram znázorňující tok přihlašovacích údajů k heslům vlastníka prostředku](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Žádost o autorizaci

ROPC flow je jeden požadavek: pošle identifikaci klienta a přihlašovací údaje uživatele do IDP a pak obdrží tokeny při návratu. Klient musí před tím, než to uděláte, požádat o e-mailovou adresu uživatele (UPN) a heslo. Ihned po úspěšné žádosti by měl klient bezpečně uvolnit přihlašovací údaje uživatele z paměti. Nesmí je nikdy Uložit.

> [!TIP]
> Zkuste tento požadavek provést v nástroji post!
> [![zkuste tento požadavek spustit v nástroji post.](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


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
| `tenant` | Požaduje se | Tenant adresáře, do kterého chcete uživatele přihlašovat. Může se jednat o formát GUID nebo popisný název. Tento parametr nelze nastavit na `common` nebo `consumers`, ale lze jej nastavit na hodnotu `organizations`. |
| `client_id` | Požaduje se | ID aplikace (klienta), ke které se stránka [Azure Portal registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) přiřazená vaší aplikaci. | 
| `grant_type` | Požaduje se | Musí být nastaveno na `password`. |
| `username` | Požaduje se | E-mailová adresa uživatele |
| `password` | Požaduje se | Heslo uživatele. |
| `scope` | Doporučené | Mezerou oddělený seznam [oborů](v2-permissions-and-consent.md)nebo oprávnění, které aplikace vyžaduje. V interaktivním toku musí správce nebo uživatel na tyto obory vyjádřit svůj souhlas předem. |
| `client_secret`| Někdy vyžadováno | Pokud je vaše aplikace veřejným klientem, nebude možné zahrnout `client_secret` nebo `client_assertion`.  Pokud je aplikace důvěrného klienta, musí být součástí. | 
| `client_assertion` | Někdy vyžadováno | Jiná forma `client_secret`vygenerovaná pomocí certifikátu.  Další podrobnosti najdete v tématu [přihlašovací údaje k certifikátu](active-directory-certificate-credentials.md) . | 

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
| `access_token`| Neprůhledný řetězec | Vydány pro požadované [obory](v2-permissions-and-consent.md) . |
| `id_token` | JWT | Vydáno, pokud původní parametr `scope` zahrnoval obor `openid`. |
| `refresh_token` | Neprůhledný řetězec | Vydáno, pokud je `offline_access`původní parametr `scope`. |

Pomocí obnovovacího tokenu můžete získat nové přístupové tokeny a aktualizovat tokeny pomocí stejného toku popsaného v [dokumentaci ke službě Flow Code OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Chybová odezva

Pokud uživatel nezadal správné uživatelské jméno nebo heslo nebo pokud klient neobdržel požadovaný souhlas, ověřování se nezdaří.

| Chyba | Popis | Akce klienta |
|------ | ----------- | -------------|
| `invalid_grant` | Ověřování se nezdařilo. | Přihlašovací údaje byly nesprávné nebo klient nemá souhlas pro požadované obory. Pokud nejsou obory uděleny, bude vrácena `consent_required`á chyba. Pokud k tomu dojde, klient by měl odeslat uživateli interaktivní výzvu pomocí webového zobrazení nebo prohlížeče. |
| `invalid_request` | Požadavek byl nesprávně vytvořen. | Typ grantu není podporován v `/common` nebo `/consumers` kontextech ověřování.  Místo toho použijte `/organizations` nebo ID tenanta. |

## <a name="learn-more"></a>Víc se uč

* Vyzkoušejte si ROPC pro sebe pomocí [ukázkové konzolové aplikace](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* Pokud chcete zjistit, jestli byste měli použít koncový bod v 2.0, přečtěte si o [omezeních platformy Microsoft Identity](active-directory-v2-limitations.md).

---
title: Přihlaste se pomocí přihlašovacích údajů pro heslo vlastníka prostředku | Azure
titleSuffix: Microsoft identity platform
description: Podpora toků ověřování bez prohlížeče pomocí udělení pověření pro heslo vlastníka prostředku (ROPC).
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 39cd25c2c84e92a0b06bc2ee6c6229ecb2d296d5
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812535"
---
# <a name="microsoft-identity-platform-and-oauth-20-resource-owner-password-credentials"></a>Přihlašovací údaje pro heslo vlastníka prostředku Microsoft Identity Platform a OAuth 2,0

Platforma Microsoft Identity Platform podporuje [udělení přihlašovacích údajů k heslu (ROPC) OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-4.3), což aplikaci umožňuje přihlašovat uživatele přímo pomocí manipulace s heslem.  Tento článek popisuje, jak programovat přímo s protokolem ve vaší aplikaci.  Pokud je to možné, doporučujeme místo toho použít podporované knihovny Microsoft Authentication Library (MSAL) k [získání tokenů a volání zabezpečených webových rozhraní API](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Podívejte se také na [ukázkové aplikace, které používají MSAL](sample-v2-code.md).

> [!WARNING]
> Microsoft doporučuje, abyste tok ROPC _nepoužívali_ . Ve většině scénářů jsou k dispozici a doporučovány bezpečnější alternativy. Tento tok vyžaduje v aplikaci velmi vysoký stupeň důvěry a přináší rizika, která nejsou přítomna v jiných tocích. Tento tok byste měli použít jenom v případě, že se nedají použít jiné bezpečnější toky.

> [!IMPORTANT]
>
> * Koncový bod Microsoft Identity Platform podporuje jenom ROPC pro klienty Azure AD, ne pro osobní účty. To znamená, že musíte použít koncový bod ( `https://login.microsoftonline.com/{TenantId_or_Name}` ) nebo koncový bod specifický pro klienta `organizations` .
> * Osobní účty, které jsou pozvány klientovi služby Azure AD, nemůžou používat ROPC.
> * Účty, které nemají hesla, se nemůžou přihlásit přes ROPC. Pro tento scénář doporučujeme místo toho použít jiný tok pro aplikaci.
> * Pokud uživatelé potřebují k přihlášení k aplikaci použít [vícefaktorové ověřování (MFA)](../authentication/concept-mfa-howitworks.md) , místo toho se zablokují.
> * ROPC se ve scénářích [federace hybridních identit](../hybrid/whatis-fed.md) nepodporují (například Azure AD a ADFS používané k ověřování místních účtů). Pokud jsou uživatelé na celé stránce přesměrováni na místní zprostředkovatele identity, Azure AD nemůže testovat uživatelské jméno a heslo proti tomuto zprostředkovateli identity. [Předávací ověřování](../hybrid/how-to-connect-pta.md) je však podporováno v ROPC.

## <a name="protocol-diagram"></a>Diagram protokolu

V následujícím diagramu je znázorněný ROPC tok.

![Diagram znázorňující tok přihlašovacích údajů k heslům vlastníka prostředku](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Žádost o autorizaci

ROPC flow je jeden požadavek: pošle identifikaci klienta a přihlašovací údaje uživatele do IDP a pak obdrží tokeny při návratu. Klient musí před tím, než to uděláte, požádat o e-mailovou adresu uživatele (UPN) a heslo. Ihned po úspěšné žádosti by měl klient bezpečně uvolnit přihlašovací údaje uživatele z paměti. Nesmí je nikdy Uložit.

> [!TIP]
> Zkuste tento požadavek provést v nástroji post!
> [![Pokus o spuštění této žádosti v předzálohovacím](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```HTTP
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

| Parametr | Stav | Popis |
| --- | --- | --- |
| `tenant` | Povinné | Tenant adresáře, do kterého chcete uživatele přihlašovat. Může se jednat o formát GUID nebo popisný název. Tento parametr nemůže být nastaven na hodnotu `common` nebo `consumers` , ale může být nastaven na hodnotu `organizations` . |
| `client_id` | Požaduje se | ID aplikace (klienta), ke které se stránka [Azure Portal registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) přiřazená vaší aplikaci. |
| `grant_type` | Požaduje se | Musí být nastaven na hodnotu `password` . |
| `username` | Požaduje se | E-mailová adresa uživatele. |
| `password` | Požaduje se | Heslo uživatele. |
| `scope` | Doporučeno | Mezerou oddělený seznam [oborů](v2-permissions-and-consent.md)nebo oprávnění, které aplikace vyžaduje. V interaktivním toku musí správce nebo uživatel na tyto obory vyjádřit svůj souhlas předem. |
| `client_secret`| Někdy vyžadováno | Pokud je vaše aplikace veřejným klientem, nelze ji `client_secret` `client_assertion` zahrnout nebo.  Pokud je aplikace důvěrného klienta, musí být součástí. |
| `client_assertion` | Někdy vyžadováno | Jiná forma `client_secret` , generovaná pomocí certifikátu.  Další podrobnosti najdete v tématu [přihlašovací údaje k certifikátu](active-directory-certificate-credentials.md) . |

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
| `token_type` | Řetězec | Vždy nastavte na `Bearer` . |
| `scope` | Řetězce oddělené mezerami | Pokud byl vrácen přístupový token, tento parametr vypíše obory, pro které je přístupový token platný. |
| `expires_in`| int | Počet sekund, po který je zahrnutý přístupový token platný |
| `access_token`| Neprůhledný řetězec | Vydány pro požadované [obory](v2-permissions-and-consent.md) . |
| `id_token` | TOKEN | Vydáno, pokud původní `scope` parametr zahrnoval `openid` obor. |
| `refresh_token` | Neprůhledný řetězec | Vydáno, pokud je `scope` zahrnut původní parametr `offline_access` . |

Pomocí obnovovacího tokenu můžete získat nové přístupové tokeny a aktualizovat tokeny pomocí stejného toku popsaného v [dokumentaci ke službě Flow Code OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Chybová odezva

Pokud uživatel nezadal správné uživatelské jméno nebo heslo nebo pokud klient neobdržel požadovaný souhlas, ověřování se nezdaří.

| Chyba | Popis | Akce klienta |
|------ | ----------- | -------------|
| `invalid_grant` | Ověřování se nezdařilo. | Přihlašovací údaje byly nesprávné nebo klient nemá souhlas pro požadované obory. Pokud nejsou obory uděleny, `consent_required` bude vrácena chyba. Pokud k tomu dojde, klient by měl odeslat uživateli interaktivní výzvu pomocí webového zobrazení nebo prohlížeče. |
| `invalid_request` | Požadavek byl nesprávně vytvořen. | Typ grantu není podporován u `/common` `/consumers` kontextů ověřování nebo.  `/organizations`Místo toho použijte nebo ID tenanta. |

## <a name="learn-more"></a>Další informace

Příklad použití ROPC naleznete v části ukázka kódu [konzolové aplikace .NET Core](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) na GitHubu.

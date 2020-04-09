---
title: Přihlášení pomocí udělení pověření vlastníka prostředku | Azure
titleSuffix: Microsoft identity platform
description: Podpora toky ověřování bez prohlížeče pomocí přidělení hesla vlastníka prostředku (ROPC).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 173f632a6baed0179b7b4facd2962f9c9764af47
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886326"
---
# <a name="microsoft-identity-platform-and-oauth-20-resource-owner-password-credentials"></a>Platforma identit y Microsoft a pověření vlastníka prostředků OAuth 2.0

Platforma identit společnosti Microsoft podporuje [udělení hesla vlastníka prostředků OAuth 2.0 (ROPC),](https://tools.ietf.org/html/rfc6749#section-4.3)který umožňuje aplikaci přihlásit se k uživateli přímým zpracováním hesla.  Tento článek popisuje, jak programovat přímo proti protokolu ve vaší aplikaci.  Pokud je to možné, doporučujeme místo toho použít podporované knihovny ověřování společnosti Microsoft (MSAL) k [získání tokenů a volání zabezpečených webových rozhraní API](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Také se podívejte na [ukázkové aplikace, které používají MSAL](sample-v2-code.md).

> [!WARNING]
> Společnost Microsoft doporučuje _nepoužívat_ tok ROPC. Ve většině scénářů jsou k dispozici a doporučeny bezpečnější alternativy. Tento tok vyžaduje velmi vysoký stupeň důvěry v aplikaci a nese rizika, která nejsou přítomna v jiných tocích. Tento tok byste měli použít pouze v případě, že nelze použít jiné bezpečnější toky.

> [!IMPORTANT]
>
> * Koncový bod platformy identit Microsoftu podporuje jenom ROPC pro klienty Azure AD, ne osobní účty. To znamená, že je nutné použít`https://login.microsoftonline.com/{TenantId_or_Name}`koncový bod `organizations` specifický pro klienta ( ) nebo koncový bod.
> * Osobní účty, které jsou pozvány do klienta Azure AD nelze použít ROPC.
> * Účty, které nemají hesla, se nemohou přihlásit přes ROPC. V tomto scénáři doporučujeme použít jiný tok pro vaši aplikaci místo.
> * Pokud uživatelé potřebují k přihlášení k aplikaci použít vícefaktorové ověřování (MFA), budou místo toho blokováni.
> * ROPC není podporována ve scénářích [federace hybridní identity](/azure/active-directory/hybrid/whatis-fed) (například Azure AD a ADFS slouží k ověření místníúčty). Pokud jsou uživatelé přesměrováni na místní poskytovatele identit, Azure AD není možné otestovat uživatelské jméno a heslo proti tomuto zprostředkovateli identity. [Předávací ověřování](/azure/active-directory/hybrid/how-to-connect-pta) je však podporováno pomocí ROPC.

## <a name="protocol-diagram"></a>Protokolový diagram

Následující diagram znázorňuje tok ROPC.

![Diagram znázorňující tok pověření hesla vlastníka prostředku](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Žádost o autorizaci

Tok ROPC je jeden požadavek: odešle identifikaci klienta a pověření uživatele idp a pak obdrží tokeny na oplátku. Klient musí před tím požádat o e-mailovou adresu uživatele (UPN) a heslo. Ihned po úspěšném požadavku by měl klient bezpečně uvolnit pověření uživatele z paměti. Nikdy je to nesmí zachránit.

> [!TIP]
> Zkuste provést tento požadavek v Pošťák!
> [![Zkuste spustit tento požadavek v Pošťáku.](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


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
| `tenant` | Požaduje se | Klient adresáře, ke kterému chcete uživatele přihlásit. To může být ve formátu GUID nebo popisný název. Tento parametr nelze nastavit `common` na `consumers`hodnotu nebo `organizations`, ale může být nastaven na hodnotu . |
| `client_id` | Požaduje se | ID aplikace (klienta), které je k vaší aplikaci přiřazena stránka [Registrace aplikací – a registrací aplikací.](https://go.microsoft.com/fwlink/?linkid=2083908) | 
| `grant_type` | Požaduje se | Musí být `password`nastavena na . |
| `username` | Požaduje se | E-mailová adresa uživatele. |
| `password` | Požaduje se | Heslo uživatele. |
| `scope` | Doporučené | Prostorově oddělený seznam [oborů](v2-permissions-and-consent.md)nebo oprávnění, které aplikace vyžaduje. V interaktivním toku musí správce nebo uživatel předem souhlasit s těmito obory. |
| `client_secret`| Někdy je vyžadováno | Pokud je vaše aplikace veřejným `client_secret` klientem, pak nebo `client_assertion` nelze zahrnout.  Pokud je aplikace důvěrným klientem, musí být zahrnuta. | 
| `client_assertion` | Někdy je vyžadováno | Jiná forma `client_secret`aplikace , generovaná pomocí certifikátu.  Další podrobnosti najdete v [tématu pověření certifikátu.](active-directory-certificate-credentials.md) | 

### <a name="successful-authentication-response"></a>Úspěšná odpověď na ověření

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
| `token_type` | Řetězec | Vždy nastavena na `Bearer`. |
| `scope` | Řetězce oddělené mezerami | Pokud byl vrácen přístupový token, tento parametr uvádí obory, pro které je přístupový token platný. |
| `expires_in`| int | Počet sekund, pro které je zahrnutý přístupový token platný. |
| `access_token`| Neprůhledný řetězec | Vydáno pro [obory,](v2-permissions-and-consent.md) které byly požadovány. |
| `id_token` | JWT | Vydáno, pokud `scope` původní parametr `openid` zahrnoval obor. |
| `refresh_token` | Neprůhledný řetězec | Vydáno, pokud `scope` je `offline_access`zahrnut původní parametr . |

Obnovovací token můžete použít k získání nových přístupových tokenů a obnovovacích tokenů pomocí stejného toku popsaného v [dokumentaci toku kódu OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Odpověď na chybu

Pokud uživatel neposkytl správné uživatelské jméno nebo heslo nebo klient neobdržel požadovaný souhlas, ověření se nezdaří.

| Chyba | Popis | Akce klienta |
|------ | ----------- | -------------|
| `invalid_grant` | Ověření se nezdařilo. | Pověření byla nesprávná nebo klient nemá souhlas s požadovanými obory. Pokud obory nejsou uděleny, `consent_required` bude vrácena chyba. Pokud k tomu dojde, klient by měl odeslat uživatele do interaktivní výzvy pomocí webového zobrazení nebo prohlížeče. |
| `invalid_request` | Požadavek byl nesprávně vytvořen. | Typ grantu není podporován `/common` v `/consumers` kontextu nebo ověřování.  Místo `/organizations` toho použít nebo ID klienta. |

## <a name="learn-more"></a>Další informace

* Vyzkoušejte si ROPC sami pomocí [ukázkové konzolové aplikace](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* Chcete-li zjistit, zda byste měli použít koncový bod v2.0, přečtěte si o [omezení platformy identit y společnosti Microsoft](active-directory-v2-limitations.md).

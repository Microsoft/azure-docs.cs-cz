---
title: Protokoly souhlasu správce Microsoft Identity Platform
description: Popis autorizace na koncovém bodu Microsoft Identity Platform, včetně oborů, oprávnění a souhlasu
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/3/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 7de97fd775853f64803ab62ac397e754d065e4df
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97509321"
---
# <a name="admin-consent-on-the-microsoft-identity-platform"></a>Souhlas správce na platformě Microsoft identity

Některá oprávnění vyžadují souhlas od správce, aby je bylo možné udělit v rámci tenanta.  K udělení oprávnění celému klientovi můžete také použít koncový bod pro souhlas správce.

## <a name="recommended-sign-the-user-into-your-app"></a>Doporučeno: podepsat uživatele do aplikace

Při vytváření aplikace, která používá koncový bod souhlasu správce, obvykle aplikace potřebuje stránku nebo zobrazení, ve kterém může správce schválit oprávnění aplikace. Tato stránka může být součástí toku registrace aplikace, části nastavení aplikace nebo může být vyhrazeným tokem "Connect". V mnoha případech má aplikace smysl zobrazit toto zobrazení "připojit" až po přihlášení uživatele pomocí pracovní nebo školní účet Microsoft.

Když uživatele podepíšete do své aplikace, můžete určit organizaci, ke které správce patří, a teprve potom požádat o schválení potřebných oprávnění. I když není nezbytně nutné, může vám to usnadnit vytváření intuitivnějšího prostředí pro uživatele vaší organizace. Pokud chcete uživatele podepsat, postupujte podle [kurzů Microsoft Identity Platform Protocol](active-directory-v2-protocols.md).

## <a name="request-the-permissions-from-a-directory-admin"></a>Vyžádání oprávnění od správce adresáře

Až budete připraveni požádat o oprávnění od správce vaší organizace, můžete uživatele přesměrovat na *koncový bod souhlasu správce* Microsoft Identity Platform.

```HTTP
// Line breaks are for legibility only.
GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
&scope=
https://graph.microsoft.com/calendars.read
https://graph.microsoft.com/mail.send
```

| Parametr | Podmínka | Popis |
| ---: | ---: | :---: |
| `tenant` | Povinné | Tenant adresáře, ze kterého chcete požádat o oprávnění. Dá se zadat v GUID nebo ve formátu popisného názvu nebo obecně odkazovaného, `organizations` jak je vidět v příkladu. Nepoužívejte "Common", protože osobní účty nemůžou poskytovat souhlas správce, s výjimkou kontextu tenanta. Aby se zajistila nejlepší kompatibilita s osobními účty, které spravují klienty, použijte ID tenanta, pokud je to možné. |
| `client_id` | Vyžadováno | **ID aplikace (klienta)** , které [Azure Portal – registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) prostředí přiřazené k vaší aplikaci. |
| `redirect_uri` | Vyžadováno |Identifikátor URI přesměrování, kde má být odeslána odpověď pro zpracování vaší aplikace. Musí přesně odpovídat jednomu z identifikátorů URI přesměrování, které jste zaregistrovali na portálu pro registraci aplikací. |
| `state` | Doporučeno | Hodnota obsažená v požadavku, která se také vrátí v odpovědi tokenu. Může to být řetězec libovolného obsahu, který chcete. Použijte stav ke kódování informací o stavu uživatele v aplikaci předtím, než došlo k žádosti o ověření, jako je například stránka nebo zobrazení, na kterých se nachází. |
|`scope` | Vyžadováno | Definuje sadu oprávnění, kterou aplikace požaduje. Může se jednat o statickou hodnotu (pomocí/.Default) nebo dynamické obory. To může zahrnovat obory OIDC ( `openid` , `profile` , `email` ). |


V tomto okamžiku Azure AD vyžaduje, aby se k dokončení žádosti přihlásil správce tenanta. Správce se vyzve ke schválení všech oprávnění, která jste si vyžádali v `scope` parametru.  Pokud jste použili statickou `/.default` hodnotu (), bude fungovat jako koncový bod souhlasu správce v 1.0 a souhlas se žádostí pro všechny obory nalezené v požadovaných oprávněních pro danou aplikaci.

### <a name="successful-response"></a>Úspěšná odpověď

Pokud správce schválí oprávnění pro vaši aplikaci, úspěšná odpověď vypadá takto:

```
http://localhost/myapp/permissions?admin_consent=True&tenant=fa00d692-e9c7-4460-a743-29f2956fd429&state=12345&scope=https%3a%2f%2fgraph.microsoft.com%2fCalendars.Read+https%3a%2f%2fgraph.microsoft.com%2fMail.Send
```

| Parametr | Popis |
| ---: | :---: |
| `tenant`| Tenant adresáře, který vaší aplikaci udělil požadovaná oprávnění, ve formátu identifikátoru GUID.|
| `state` | Hodnota obsažená v požadavku, která se také vrátí v odpovědi tokenu. Může to být řetězec libovolného obsahu, který chcete. Stav se používá ke kódování informací o stavu uživatele v aplikaci předtím, než došlo k žádosti o ověření, jako je například stránka nebo zobrazení, na kterých se nachází.|
| `scope` | Sada oprávnění, kterým byl udělen přístup pro aplikaci.|
| `admin_consent` | Bude nastaveno na `True` .|

### <a name="error-response"></a>Chybová odezva

`http://localhost/myapp/permissions?error=consent_required&error_description=AADSTS65004%3a+The+resource+owner+or+authorization+server+denied+the+request.%0d%0aTrace+ID%3a+d320620c-3d56-42bc-bc45-4cdd85c41f00%0d%0aCorrelation+ID%3a+8478d534-5b2c-4325-8c2c-51395c342c89%0d%0aTimestamp%3a+2019-09-24+18%3a34%3a26Z&admin_consent=True&tenant=fa15d692-e9c7-4460-a743-29f2956fd429&state=12345`

Při přidání k parametrům zobrazeným v úspěšné odpovědi se zobrazí parametry chyby uvedené níže.

| Parametr | Popis |
|-------------------:|:-------------------------------------------------------------------------------------------------:|
| `error` | Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým dojde, a lze jej použít k reakci na chyby.|
| `error_description` | Konkrétní chybová zpráva, která může vývojářům pomáhat určit hlavní příčinu chyby.|
| `tenant`| Tenant adresáře, který vaší aplikaci udělil požadovaná oprávnění, ve formátu identifikátoru GUID.|
| `state` | Hodnota obsažená v požadavku, která se také vrátí v odpovědi tokenu. Může to být řetězec libovolného obsahu, který chcete. Stav se používá ke kódování informací o stavu uživatele v aplikaci předtím, než došlo k žádosti o ověření, jako je například stránka nebo zobrazení, na kterých se nachází.|
| `admin_consent` | Bude nastaveno na `True` , aby označovali, že k této odezvě došlo v toku souhlasu správce.|

## <a name="next-steps"></a>Další kroky
- Podívejte se, [Jak převést aplikaci na více tenantů](howto-convert-app-to-be-multi-tenant.md) .
- Přečtěte si, jak [je ve vrstvě protokolu OAuth 2,0 během toku grant autorizačního kódu podporovaná podpora](v2-oauth2-auth-code-flow.md#request-an-authorization-code).
- Přečtěte si, [Jak může aplikace pro více tenantů používat souhlasu architektury](./howto-convert-app-to-be-multi-tenant.md) k implementaci souhlasu "User" a "admin", který podporuje pokročilejší vzory vícevrstvých aplikací.
- Principy [prostředí pro vyjádření souhlasu s aplikacemi Azure AD](application-consent-experience.md)
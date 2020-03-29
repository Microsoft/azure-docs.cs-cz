---
title: Protokoly o souhlasu správce platformy identit microsoftu | Dokumenty společnosti Microsoft
description: Popis autorizace v koncovém bodě platformy identit microsoftu, včetně oborů, oprávnění a souhlasu.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 8f98cbf0-a71d-4e34-babf-e642ad9ff423
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/3/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: b9d6ab0fc2f2bf500f17161de7e090a6f60c0feb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76700732"
---
# <a name="admin-consent-on-the-microsoft-identity-platform"></a>Souhlas správce na platformě microsoft identity

Některá oprávnění vyžadují souhlas správce před tím, než mohou být udělena v rámci klienta.  Koncový bod souhlasu správce můžete také použít k udělení oprávnění celému tenantovi.  

## <a name="recommended-sign-the-user-into-your-app"></a>Doporučené: Přihlášení uživatele do aplikace

Obvykle při vytváření aplikace, která používá koncový bod souhlasu správce, aplikace potřebuje stránku nebo zobrazení, ve kterém správce může schválit oprávnění aplikace. Tato stránka může být součástí toku registrace aplikace, součástí nastavení aplikace nebo může být vyhrazený tok "připojení". V mnoha případech má smysl, aby aplikace zobrazila toto zobrazení "připojení" až poté, co se uživatel přihlásí pomocí pracovního nebo školního účtu Microsoft.

Když uživatele přihlásíte do aplikace, můžete určit organizaci, do které správce patří, než ho požádáte o schválení potřebných oprávnění. I když to není nezbytně nutné, může vám pomoci vytvořit intuitivnější prostředí pro uživatele vaší organizace. Chcete-li uživatele přihlásit, postupujte podle [našich výukových programů protokolu platformy identit společnosti Microsoft](active-directory-v2-protocols.md).

## <a name="request-the-permissions-from-a-directory-admin"></a>Vyžádání oprávnění od správce adresáře

Až budete připraveni požádat o oprávnění správce vaší organizace, můžete uživatele přesměrovat na *koncový bod souhlasu správce*platformy identit Microsoftu .

```
// Line breaks are for legibility only.
    GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
  client_id=6731de76-14a6-49ae-97bc-6eba6914391e
  &state=12345
  &redirect_uri=http://localhost/myapp/permissions
    &scope=
    https://graph.microsoft.com/calendars.read 
    https://graph.microsoft.com/mail.send
```


| Parametr     | Podmínka     | Popis                                                                               |
|--------------:|--------------:|:-----------------------------------------------------------------------------------------:|
| `tenant` | Požaduje se | Klient adresáře, od kterého chcete požádat o oprávnění. Může být poskytnuta ve formátu GUID nebo popisný název nebo obecně odkazuje, `organizations` jak je vidět v příkladu. Nepoužívejte "společné", protože osobní účty nemohou poskytnout souhlas správce s výjimkou v kontextu klienta. Chcete-li zajistit nejlepší kompatibilitu s osobními účty, které spravují klienty, použijte ID klienta, pokud je to možné. |
| `client_id` | Požaduje se | **ID aplikace (klienta),** které je k vaší aplikaci přiřazené [na portálu Azure – možnosti registrace aplikací.](https://go.microsoft.com/fwlink/?linkid=2083908) |
| `redirect_uri` | Požaduje se |Identifikátor URI přesměrování, kam chcete odeslat odpověď, aby ji vaše aplikace zpracovat. Musí přesně odpovídat jednomu z identifikátorů URI přesměrování, které jste zaregistrovali na portálu pro registraci aplikací. |
| `state` | Doporučené | Hodnota zahrnutá v požadavku, která bude také vrácena v odpovědi tokenu. Může to být řetězec libovolného obsahu, který chcete. Pomocí stavu můžete zakódovat informace o stavu uživatele v aplikaci před tím, než došlo k požadavku na ověření, například stránku nebo zobrazení, na kterých se používali. |
|`scope`        | Požaduje se      | Definuje sadu oprávnění požadovaných aplikací. To může být statické (pomocí /.default) nebo dynamické obory.  To může zahrnovat obory`openid`OIDC ( , `profile`, `email`). | 


V tomto okamžiku Azure AD vyžaduje správce klienta k přihlášení k dokončení požadavku. Správce je požádán o schválení všech oprávnění, `scope` která jste požadovali v parametru.  Pokud jste použili statickou (`/.default`) hodnotu, bude fungovat jako koncový bod souhlasu správce v1.0 a požádá o souhlas pro všechny obory nalezené v požadovaných oprávněních pro aplikaci.

### <a name="successful-response"></a>Úspěšná odpověď

Pokud správce schválí oprávnění pro vaši aplikaci, úspěšná odpověď vypadá takto:

```
http://localhost/myapp/permissions?admin_consent=True&tenant=fa00d692-e9c7-4460-a743-29f2956fd429&state=12345&scope=https%3a%2f%2fgraph.microsoft.com%2fCalendars.Read+https%3a%2f%2fgraph.microsoft.com%2fMail.Send
```

| Parametr         | Popis                                                                                       |
|------------------:|:-------------------------------------------------------------------------------------------------:|
| `tenant`| Klient adresáře, který vaší aplikaci udělil požadovaná oprávnění ve formátu GUID.|
| `state`           | Hodnota zahrnutá v požadavku, která bude také vrácena v odpovědi tokenu. Může to být řetězec libovolného obsahu, který chcete. Stav se používá ke kódování informací o stavu uživatele v aplikaci před došlo k požadavku na ověření, jako je například stránka nebo zobrazení, na které se nachází.|
| `scope`          | Sada oprávnění, kterým byl udělen přístup k aplikaci.|
| `admin_consent`   | Bude nastavena `True`na .|

### <a name="error-response"></a>Odpověď na chybu

`http://localhost/myapp/permissions?error=consent_required&error_description=AADSTS65004%3a+The+resource+owner+or+authorization+server+denied+the+request.%0d%0aTrace+ID%3a+d320620c-3d56-42bc-bc45-4cdd85c41f00%0d%0aCorrelation+ID%3a+8478d534-5b2c-4325-8c2c-51395c342c89%0d%0aTimestamp%3a+2019-09-24+18%3a34%3a26Z&admin_consent=True&tenant=fa15d692-e9c7-4460-a743-29f2956fd429&state=12345`

Přidání parametrů vidět v úspěšné odpovědi, parametry chyb y jsou uvedeny níže.

| Parametr          | Popis                                                                                      |
|-------------------:|:-------------------------------------------------------------------------------------------------:|
| `error`            | Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým dochází, a lze jej použít k reakci na chyby.|
| `error_description`| Konkrétní chybová zpráva, která může pomoci vývojáři identifikovat hlavní příčinu chyby.|
| `tenant`| Klient adresáře, který vaší aplikaci udělil požadovaná oprávnění ve formátu GUID.|
| `state`           | Hodnota zahrnutá v požadavku, která bude také vrácena v odpovědi tokenu. Může to být řetězec libovolného obsahu, který chcete. Stav se používá ke kódování informací o stavu uživatele v aplikaci před došlo k požadavku na ověření, jako je například stránka nebo zobrazení, na které se nachází.|
| `admin_consent`   | Bude nastavena `True` tak, aby označovala, že k této odpovědi došlo při toku souhlasu správce.|

## <a name="next-steps"></a>Další kroky
- Podívejte [se, jak převést aplikaci na víceklientské aplikace](howto-convert-app-to-be-multi-tenant.md)
- Zjistěte, jak [je souhlas podporován ve vrstvě protokolu OAuth 2.0 během toku udělení autorizačního kódu](v2-oauth2-auth-code-flow.md#request-an-authorization-code).
- Zjistěte, [jak může víceklientská aplikace použít rámec souhlasu](active-directory-devhowto-multi-tenant-overview.md) k implementaci souhlasu uživatele a správce a podporovat pokročilejší vícevrstvé vzory aplikací.
- Principy [prostředí souhlasu aplikace Azure AD](application-consent-experience.md)

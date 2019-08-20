---
title: 'Azure AD Connect: Bezproblémové jednotné přihlašování | Microsoft Docs'
description: Toto téma popisuje Azure Active Directory (Azure AD) bezproblémové jednotné přihlašování a to, jak vám umožňuje poskytovat skutečné jednotné přihlašování pro uživatele firemních stolních počítačů v podnikové síti.
services: active-directory
keywords: Co je Azure AD Connect, instalace služby Active Directory, požadované součásti pro Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86895ab315784c49c2b240badb249dce57ae958a
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622562"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory bezproblémové jednotné přihlašování

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Co je Azure Active Directory bezproblémové jednotné přihlašování?

Bezproblémové jednotné přihlašování Azure Active Directory (bezproblémové jednotné přihlašování Azure AD) automaticky přihlašuje uživatele, kteří zrovna používají svá podniková zařízení připojená k vaší podnikové síti. Pokud je tato možnost povolená, uživatelé nebudou muset zadávat hesla pro přihlášení ke službě Azure AD a obvykle dokonce zadávejte své uživatelské jméno. Tato funkce poskytuje uživatelům snadný přístup k vašim cloudovým aplikacím bez potřeby dalších místních komponent.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Bezproblémové jednotné přihlašování se dá kombinovat buď se [synchronizací hodnot hash hesel](how-to-connect-password-hash-synchronization.md) , nebo [pomocí předávacích ověřovacích metod ověřování](how-to-connect-pta.md) . Bezproblémové jednotné přihlašování se _nevztahuje_ na Active Directory Federation Services (AD FS) (ADFS).

![Bezproblémové jednotné přihlašování](./media/how-to-connect-sso/sso1.png)

>[!IMPORTANT]
>Bezproblémové jednotné přihlašování vyžaduje, aby zařízení uživatele bylo **připojené k doméně**, ale není nutné, aby bylo zařízení [připojené k Azure AD](../active-directory-azureadjoin-overview.md).

## <a name="key-benefits"></a>Klíčové výhody

- *Skvělé uživatelské prostředí*
  - Uživatelé se automaticky přihlásí k místním i cloudovým aplikacím.
  - Uživatelé nemusejí opakovaně zadávat hesla.
- *Snadné nasazení & spravovat*
  - K provedení této práce nejsou potřeba žádné další součásti v místním prostředí.
  - Funguje s libovolnou metodou cloudového ověřování – [synchronizace hodnot hash hesel](how-to-connect-password-hash-synchronization.md) nebo [předávacího ověřování](how-to-connect-pta.md).
  - Je možné je navrátit pro některé nebo všechny uživatele pomocí Zásady skupiny.
  - Registrovat zařízení s jiným systémem než Windows 10 pomocí Azure AD bez nutnosti infrastruktury AD FS. Tato funkce vyžaduje, abyste používali verzi 2,1 nebo novější z [klienta připojení k síti](https://www.microsoft.com/download/details.aspx?id=53554)na pracovišti.

## <a name="feature-highlights"></a>Zvýraznění funkcí

- Přihlašovací uživatelské jméno může být buď místní výchozí uživatelské jméno (`userPrincipalName`), nebo jiný atribut nakonfigurovaný v Azure AD Connect (`Alternate ID`). Jak fungují případy použití, protože bezproblémová `securityIdentifier` služba jednotného přihlašování používá deklaraci identity v lístku protokolu Kerberos k vyhledání odpovídajícího objektu uživatele v Azure AD.
- Bezproblémové jednotné přihlašování je příležitostné funkce. Pokud z nějakého důvodu dojde k chybě, přihlašovací prostředí uživatele se vrátí k běžnému chování – tzn. uživatel musí na přihlašovací stránce zadat heslo.
- Pokud aplikace `https://myapps.microsoft.com/contoso.com`(například) předá parametr (OpenID Connect) nebo `whr` (SAML), který `domain_hint` identifikuje vašeho tenanta, nebo `login_hint` parametr – identifikuje uživatele, v jeho žádosti o přihlášení k Azure AD jsou uživatelé k automatickému přihlášení bez zadání uživatelských jmen a hesel.
- `https://contoso.sharepoint.com`Uživatelé také získají tiché přihlašování, pokud aplikace (například) odesílá žádosti o přihlášení koncovým bodům služby Azure AD nastaveným jako klienti – to `https://login.microsoftonline.com/contoso.com/<..>` znamená nebo `https://login.microsoftonline.com/<tenant_ID>/<..>` – místo společného koncového bodu `https://login.microsoftonline.com/common/<...>` služby Azure AD – tj. .
- Odhlášení se podporuje. To umožňuje uživatelům zvolit si jiný účet služby Azure AD pro přihlášení, místo aby se automaticky přihlásili pomocí bezproblémového jednotného přihlašování automaticky.
- Klienti Win32 Office 365 (Outlook, Word, Excel a další) s verzemi 16.0.8730. xxxx a novější se podporují pomocí neinteraktivního toku. Pro OneDrive budete muset pro tiché přihlašování aktivovat [funkci bezobslužné konfigurace OneDrive](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) .
- Dá se povolit prostřednictvím Azure AD Connect.
- Je to bezplatná funkce a nepotřebujete žádné placené edice Azure AD, abyste ji mohli používat.
- Podporuje se u klientů a klientů Office využívajících webový prohlížeč, kteří podporují [moderní ověřování](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) na platformách a prohlížečích, které podporují ověřování protokolem Kerberos:

| OS\Browser |Internet Explorer|Microsoft Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Ano\*|Ne|Ano|Ano\*\*\*|Není k dispozici
|Windows 8.1|Ano\*|Není k dispozici|Ano|Ano\*\*\*|Není k dispozici
|Windows 8|Ano\*|Není k dispozici|Ano|Ano\*\*\*|Není k dispozici
|Windows 7|Ano\*|Není k dispozici|Ano|Ano\*\*\*|Není k dispozici
|Windows Server 2012 R2 nebo novější|Ano\*\*|Není k dispozici|Ano|Ano\*\*\*|Není k dispozici
|Mac OS X|Není k dispozici|Není k dispozici|Ano\*\*\*|Ano\*\*\*|Ano\*\*\*


\*Vyžaduje Internet Explorer verze 10 nebo vyšší.

\*\*Vyžaduje Internet Explorer verze 10 nebo vyšší. Zakázat Rozšířený chráněný režim

\*\*\*Vyžaduje [Další konfiguraci](how-to-connect-sso-quick-start.md#browser-considerations) .

>[!NOTE]
>V případě Windows 10 doporučujeme použít službu [Azure AD JOIN](../active-directory-azureadjoin-overview.md) k optimálnímu jednotnému přihlašování v Azure AD.

## <a name="next-steps"></a>Další kroky

- [**Rychlé zprovoznění**](how-to-connect-sso-quick-start.md) – zprovoznění služby Azure AD bez problémů s jednotným přihlašováním
- [**Plán nasazení**](https://aka.ms/deploymentplans/sso) – podrobný plán nasazení.
- [**Technický obsáhlý podrobně**](how-to-connect-sso-how-it-works.md) – pochopení, jak tato funkce funguje.
- [**Nejčastější dotazy**](how-to-connect-sso-faq.md) – odpovědi na nejčastější dotazy
- [**Řešení potíží**](tshoot-connect-sso.md) – Naučte se řešit běžné problémy s touto funkcí.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – pro nové žádosti o funkce


---
title: 'Azure AD Connect: bezproblémové jednoduché Sign-On | Microsoft Docs'
description: Toto téma popisuje Azure Active Directory (Azure AD) bezproblémové jednoduché Sign-On a to, jak vám umožňuje poskytovat skutečné jednotné přihlašování pro uživatele firemních stolních počítačů v podnikové síti.
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
ms.topic: how-to
ms.date: 08/13/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eeede88bfbe024a66e40270240d32488e581dd77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102517697"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Bezproblémové jednotné přihlašování s Azure Active Directory

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Co je Azure Active Directory bezproblémové jednotné přihlašování?

Bezproblémové jednotné přihlašování Azure Active Directory (bezproblémové jednotné přihlašování Azure AD) automaticky přihlašuje uživatele, kteří zrovna používají svá podniková zařízení připojená k vaší podnikové síti. Pokud je tato možnost povolená, uživatelé nebudou muset zadávat hesla pro přihlášení ke službě Azure AD a obvykle dokonce zadávejte své uživatelské jméno. Tato funkce poskytuje uživatelům snadný přístup k vašim cloudovým aplikacím bez potřeby dalších místních komponent.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Bezproblémové jednotné přihlašování se dá kombinovat buď se [synchronizací hodnot hash hesel](how-to-connect-password-hash-synchronization.md) , nebo [pomocí předávacích ověřovacích metod ověřování](how-to-connect-pta.md) . Bezproblémové jednotné přihlašování se _nevztahuje_ na Active Directory Federation Services (AD FS) (ADFS).

![Bezproblémové jednoduché Sign-On](./media/how-to-connect-sso/sso1.png)

## <a name="sso-via-primary-refresh-token-vs-seamless-sso"></a>Jednotné přihlašování prostřednictvím tokenu primární aktualizace vs. bezproblémové jednotné přihlašování

U systémů Windows 10, Windows Server 2016 a novějších verzí se doporučuje používat jednotné přihlašování prostřednictvím primárního obnovovacího tokenu (PRT). U systémů Windows 7 a 8,1 se doporučuje používat bezproblémové jednotné přihlašování.
Bezproblémové jednotné přihlašování vyžaduje, aby zařízení uživatele bylo připojené k doméně, ale nepoužívá se na [zařízeních připojených](../devices/concept-azure-ad-join.md) k Windows 10 Azure AD ani na [zařízeních připojených k hybridní službě Azure AD](../devices/concept-azure-ad-join-hybrid.md). Jednotné přihlašování k Azure AD, připojené k hybridní službě Azure AD a zařízení registrovaná službou Azure AD funguje na základě [primárního obnovovacího tokenu (PRT)](../devices/concept-primary-refresh-token.md) .

Jednotné přihlašování prostřednictvím PRT funguje, když jsou zařízení zaregistrovaná ve službě Azure AD pro připojení k hybridní službě Azure AD, připojené k Azure AD nebo osobní registrovaná zařízení prostřednictvím Přidat pracovní nebo školní účet. Další informace o tom, jak jednotné přihlašování funguje s Windows 10 pomocí PRT, najdete v tématech [primární aktualizační token (PRT) a Azure AD](../devices/concept-primary-refresh-token.md) .


## <a name="key-benefits"></a>Klíčové výhody

- *Skvělé uživatelské prostředí*
  - Uživatelé se automaticky přihlásí k místním i cloudovým aplikacím.
  - Uživatelé nemusejí opakovaně zadávat hesla.
- *Snadné nasazení & spravovat*
  - K provedení této práce nejsou potřeba žádné další součásti v místním prostředí.
  - Funguje s libovolnou metodou cloudového ověřování – [synchronizace hodnot hash hesel](how-to-connect-password-hash-synchronization.md) nebo [předávacího ověřování](how-to-connect-pta.md).
  - Je možné je navrátit pro některé nebo všechny uživatele pomocí Zásady skupiny.
  - Registrovat zařízení s jiným systémem než Windows 10 pomocí Azure AD bez nutnosti infrastruktury AD FS. Tato funkce vyžaduje, abyste používali verzi 2,1 nebo novější z [klienta připojení k síti na pracovišti](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="feature-highlights"></a>Zvýraznění funkcí

- Přihlašovací uživatelské jméno může být buď místní výchozí uživatelské jméno ( `userPrincipalName` ), nebo jiný atribut nakonfigurovaný v Azure AD Connect ( `Alternate ID` ). Jak fungují případy použití, protože bezproblémová služba jednotného přihlašování používá `securityIdentifier` deklaraci identity v lístku protokolu Kerberos k vyhledání odpovídajícího objektu uživatele v Azure AD.
- Bezproblémové jednotné přihlašování je příležitostné funkce. Pokud z nějakého důvodu dojde k chybě, přihlašovací prostředí uživatele se vrátí k běžnému chování – tzn. uživatel musí na přihlašovací stránce zadat heslo.
- Pokud aplikace (například  `https://myapps.microsoft.com/contoso.com` ) předá `domain_hint` parametr (OpenID Connect) nebo `whr` (SAML), který identifikuje vašeho tenanta, nebo `login_hint` Parametr-identifikuje uživatele, v jeho žádosti o přihlášení k Azure AD se uživatelé automaticky přihlásí, aniž by museli zadávat uživatelská jména nebo hesla.
- Uživatelé také získají tiché přihlašování, pokud aplikace (například `https://contoso.sharepoint.com` ) odesílá žádosti o přihlášení koncovým bodům služby Azure AD nastaveným jako klienti – to znamená `https://login.microsoftonline.com/contoso.com/<..>` nebo `https://login.microsoftonline.com/<tenant_ID>/<..>` – místo společného koncového bodu služby Azure AD – to znamená `https://login.microsoftonline.com/common/<...>` .
- Odhlášení se podporuje. To umožňuje uživatelům zvolit si jiný účet služby Azure AD pro přihlášení, místo aby se automaticky přihlásili pomocí bezproblémového jednotného přihlašování automaticky.
- Microsoft 365 klienti Win32 (Outlook, Word, Excel a další) s verzemi 16.0.8730. xxxx a vyšší se podporují pomocí neinteraktivního toku. Pro OneDrive budete muset pro tiché přihlašování aktivovat [funkci bezobslužné konfigurace OneDrive](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) .
- Dá se povolit prostřednictvím Azure AD Connect.
- Je to bezplatná funkce a nepotřebujete žádné placené edice Azure AD, abyste ji mohli používat.
- Podporuje se u klientů a klientů Office využívajících webový prohlížeč, kteří podporují [moderní ověřování](/office365/enterprise/modern-auth-for-office-2013-and-2016) na platformách a prohlížečích, které podporují ověřování protokolem Kerberos:

| OS\Browser |Internet Explorer|Microsoft Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Ano\*|Yes|Yes|Ano\*\*\*|–
|Windows 8.1|Yes\*|Odpoví\*\*\*|Yes|Ano\*\*\*|–
|Windows 8|Yes\*|Není k dispozici|Ano|Ano\*\*\*|–
|Windows 7|Ano\*|Není k dispozici|Ano|Ano\*\*\*|–
|Windows Server 2012 R2 nebo novější|Ano\*\*|Není k dispozici|Ano|Ano\*\*\*|–
|Mac OS X|N/A|N/A|Ano\*\*\*|Ano\*\*\*|Ano\*\*\*


\*Vyžaduje Internet Explorer verze 10 nebo novější.

\*\*Vyžaduje Internet Explorer verze 10 nebo novější. Zakažte Rozšířený chráněný režim.

\*\*\*Vyžaduje [Další konfiguraci](how-to-connect-sso-quick-start.md#browser-considerations).

\*\*\*\*Vyžaduje Microsoft Edge verze 77 nebo novější.

## <a name="next-steps"></a>Další kroky

- [**Rychlé zprovoznění**](how-to-connect-sso-quick-start.md) – zprovoznění služby Azure AD bez problémů s jednotným přihlašováním
- [**Plán nasazení**](../manage-apps/plan-sso-deployment.md) – podrobný plán nasazení.
- [**Technický obsáhlý podrobně**](how-to-connect-sso-how-it-works.md) – pochopení, jak tato funkce funguje.
- [**Nejčastější dotazy**](how-to-connect-sso-faq.md) – odpovědi na nejčastější dotazy
- [**Řešení potíží**](tshoot-connect-sso.md) – Naučte se řešit běžné problémy s touto funkcí.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – pro nové žádosti o funkce

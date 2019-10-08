---
title: 'Azure AD Connect: bezproblémové jednotné přihlašování | Microsoft Docs'
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
ms.date: 08/13/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7791e7b50a963d2f92a2cbc460e36f9e83bb1b52
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025702"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory bezproblémové jednotné přihlašování

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Co je Azure Active Directory bezproblémové jednotné přihlašování?

Azure Active Directory bezproblémové jednotné přihlašování (Azure AD bezproblémové jednotné přihlašování) automaticky podepisuje uživatele v případě, že jsou na podnikových zařízeních, která jsou připojená k podnikové síti. Pokud je tato možnost povolená, uživatelé nebudou muset zadávat hesla pro přihlášení ke službě Azure AD a obvykle dokonce zadávejte své uživatelské jméno. Tato funkce poskytuje uživatelům snadný přístup k vašim cloudovým aplikacím bez nutnosti dalších místních součástí.

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
  - Registrovat zařízení s jiným systémem než Windows 10 pomocí Azure AD bez nutnosti infrastruktury AD FS. Tato funkce vyžaduje, abyste používali verzi 2,1 nebo novější z [klienta připojení k síti na pracovišti](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="feature-highlights"></a>Zvýraznění funkcí

- Přihlašovací uživatelské jméno může být buď místní výchozí uživatelské jméno (`userPrincipalName`), nebo jiný atribut nakonfigurovaný v Azure AD Connect (`Alternate ID`). Jak fungují případy použití, protože bezproblémové přihlašování používá k vyhledání odpovídajícího objektu uživatele v Azure AD deklaraci `securityIdentifier` v lístku protokolu Kerberos.
- Bezproblémové jednotné přihlašování je příležitostné funkce. Pokud z nějakého důvodu dojde k chybě, přihlašovací prostředí uživatele se vrátí k běžnému chování – tzn. uživatel musí na přihlašovací stránce zadat heslo.
- Pokud aplikace (například `https://myapps.microsoft.com/contoso.com`) předá parametr `domain_hint` (OpenID Connect) nebo `whr` (SAML) – Identifikujte vašeho tenanta nebo parametr `login_hint` – identifikace uživatele, budou uživatelé automaticky přihlášeni bez nich. zadávání uživatelských jmen a hesel.
- Uživatelé také získají tiché přihlašování, pokud aplikace (například `https://contoso.sharepoint.com`) odesílá požadavky na přihlášení koncovým bodům služby Azure AD nastaveným jako klienti – to znamená `https://login.microsoftonline.com/contoso.com/<..>` nebo `https://login.microsoftonline.com/<tenant_ID>/<..>`-místo společného koncového bodu služby Azure AD – to znamená `https://login.microsoftonline.com/common/<...>`.
- Odhlášení se podporuje. To umožňuje uživatelům zvolit si jiný účet služby Azure AD pro přihlášení, místo aby se automaticky přihlásili pomocí bezproblémového jednotného přihlašování automaticky.
- Klienti Win32 Office 365 (Outlook, Word, Excel a další) s verzemi 16.0.8730. xxxx a novější se podporují pomocí neinteraktivního toku. Pro OneDrive budete muset pro tiché přihlašování aktivovat [funkci bezobslužné konfigurace OneDrive](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) .
- Dá se povolit prostřednictvím Azure AD Connect.
- Je to bezplatná funkce a nepotřebujete žádné placené edice Azure AD, abyste ji mohli používat.
- Podporuje se u klientů a klientů Office využívajících webový prohlížeč, kteří podporují [moderní ověřování](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) na platformách a prohlížečích, které podporují ověřování protokolem Kerberos:

| OS\Browser |Internet Explorer|Microsoft Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Ano @ no__t-0|Ano|Ano|Ano @ no__t-0 @ no__t-1 @ no__t-2|Není k dispozici
|Windows 8.1|Ano @ no__t-0|Není k dispozici|Ano|Ano @ no__t-0 @ no__t-1 @ no__t-2|Není k dispozici
|Windows 8|Ano @ no__t-0|Není k dispozici|Ano|Ano @ no__t-0 @ no__t-1 @ no__t-2|Není k dispozici
|Windows 7|Ano @ no__t-0|Není k dispozici|Ano|Ano @ no__t-0 @ no__t-1 @ no__t-2|Není k dispozici
|Windows Server 2012 R2 nebo novější|Ano @ no__t-0 @ no__t-1|Není k dispozici|Ano|Ano @ no__t-0 @ no__t-1 @ no__t-2|Není k dispozici
|Mac OS X|Není k dispozici|Není k dispozici|Ano @ no__t-0 @ no__t-1 @ no__t-2|Ano @ no__t-0 @ no__t-1 @ no__t-2|Ano @ no__t-0 @ no__t-1 @ no__t-2


@no__t – 0Requires Internet Explorer verze 10 nebo vyšší

\* @ no__t-1Requires Internet Explorer verze 10 nebo vyšší. Zakázat Rozšířený chráněný režim

\* @ no__t-1 @ no__t-2Requires [Další konfigurace](how-to-connect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>V případě Windows 10 doporučujeme použít službu [Azure AD JOIN](../active-directory-azureadjoin-overview.md) k optimálnímu jednotnému přihlašování v Azure AD.

## <a name="next-steps"></a>Další kroky

- [**Rychlé zprovoznění**](how-to-connect-sso-quick-start.md) – zprovoznění služby Azure AD bez problémů s jednotným přihlašováním
- [**Plán nasazení**](https://aka.ms/deploymentplans/sso) – podrobný plán nasazení.
- [**Technický obsáhlý podrobně**](how-to-connect-sso-how-it-works.md) – pochopení, jak tato funkce funguje.
- [**Nejčastější dotazy**](how-to-connect-sso-faq.md) – odpovědi na nejčastější dotazy
- [**Řešení potíží**](tshoot-connect-sso.md) – Naučte se řešit běžné problémy s touto funkcí.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – pro nové žádosti o funkce


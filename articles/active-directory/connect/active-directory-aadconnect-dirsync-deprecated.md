---
title: Upgrade z nástroje DirSync a Azure AD Sync | Microsoft Docs
description: Popisuje, jak upgradovat z nástroje DirSync a Azure AD Sync Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: bd68fb88-110b-4d76-978a-233e15590803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 23d2be6e3fb7d14cd7ea42ffc2bf6d4174a894cc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591816"
---
# <a name="upgrade-windows-azure-active-directory-sync-and-azure-active-directory-sync"></a>Upgrade systému Windows Azure Active Directory Sync a Azure Active Directory Sync.
Azure AD Connect je nejlepší způsob, jak propojit místní adresář s Azure AD a Office 365. Toto je nejvhodnější doba pro upgrade na Azure AD Connect z Windows Azure Active Directory Sync (DirSync) nebo Azure AD Sync, jak tyto nástroje jsou už zastaralé a již nejsou podporovány od 13. dubna 2017.

Nástrojů pro synchronizaci dvě identity, které jsou zastaralé byly nabízeny pro zákazníky s jednou doménovou strukturou (DirSync) a pro více doménovými strukturami a další pokročilé zákazníků (Azure AD Sync). Tyto starší nástroje nahradil jeden řešení, které je k dispozici ve všech scénářích: Azure AD Connect. Nabízí nové funkce, nové funkce a vylepšení a podporu pro nové scénáře. Abyste mohli pokračovat v synchronizaci dat místní identity do služby Azure AD a Office 365, důrazně doporučujeme upgradovat na Azure AD Connect. Společnost Microsoft nezaručuje tyto starší verze fungovat po 31. prosince 2017.

Poslední verzi nástroje DirSync byla vydána v červenec 2014 a poslední verzi Azure AD Sync byla vydaná v květnu 2015.

## <a name="what-is-azure-ad-connect"></a>Co je služba Azure AD Connect
Azure AD Connect je nástupcem nástroje DirSync a Azure AD Sync. Ve všech scénářích je kombinací tyto dva podporována. Další informace o něm v [integrace místních identit s Azure Active Directory](active-directory-aadconnect.md).

## <a name="deprecation-schedule"></a>Vyřazení plán
| Datum | Poznámka |
| --- | --- |
| 13. dubna 2016 |Windows Azure Active Directory Sync (DirSync") a Microsoft Azure Active Directory Sync (Azure AD Sync") jsou oznámila jako zastaralé. |
| 13. dubna 2017 |Podporují elementy end. Zákazníci už nebude moct otevřít případu podpory bez nejprve upgradovat na Azure AD Connect. |
|31. prosince 2017|Azure AD mohou už přijímají komunikaci od Windows Azure Active Directory Sync (DirSync") a Microsoft Azure Active Directory Sync (Azure AD Sync").

## <a name="how-to-transition-to-azure-ad-connect"></a>Postup přechodu na Azure AD Connect
Pokud používáte nástroje DirSync, existují dva způsoby, můžete upgradovat: místní upgrade a paralelního nasazení. Místní upgrade se doporučuje pro většinu zákazníků a pokud máte poslední operační systém a menší než 50 000 objektů. V ostatních případech doporučujeme provést paralelní nasazení, kde se přesune konfiguraci nástroje DirSync na nový server se systémem Azure AD Connect.

| Řešení | Scénář |
| --- | --- |
| [Upgrade z nástroje DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md) |<li>Pokud máte existující server DirSync již byla spuštěna.</li> |
| [Upgrade z Azure AD Sync](active-directory-aadconnect-upgrade-previous-version.md) |<li>Pokud přecházíte z Azure AD Sync.</li> |

Pokud chcete zjistit, jak provést místní upgrade z nástroje DirSync na Azure AD Connect, pak najdete v tomto videu Channel 9:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-in-place-upgrade-from-legacy-tools/player]
>
>

## <a name="faq"></a>Nejčastější dotazy
**Otázka: I dostali e-mailová oznámení od týmu Azure a/nebo zprávy z Centra zpráv Office 365, ale používám připojit.**  
Oznámení byla odeslána také pro zákazníky používající Azure AD Connect s číslem sestavení 1.0. \*.0 (s použitím verze pre-1.1). Společnost Microsoft doporučuje zákazníkům držet krok s verzí Azure AD Connect. [Automatický upgrade](active-directory-aadconnect-feature-automatic-upgrade.md) funkce zavedená v 1.1 usnadňuje vždy mají nejnovější verzi nainstalovanou službu Azure AD Connect.

**Otázka: bude DirSync nebo Azure AD Sync přestat pracovat na 13. dubna 2017?**  
DirSync nebo Azure AD Sync se bude nadále fungovat na 13. dubna 2017.  Ale Azure AD mohou už přijímají komunikaci od DirSync nebo Azure AD Sync po 31. prosince 2017.

**Otázka: které verze nástroje DirSync můžete upgradovat z?**  
Podporuje se upgrade z libovolné verze nástroje DirSync aktuálně používá. 

**Otázka: co o konektoru služby Azure AD pro FIM nebo MIM?**  
Konektor služby Azure AD pro FIM nebo MIM má **není** byla oznámeno jako zastaralé. Je na **funkce zablokování**; je přidány žádné nové funkce a obdrží žádné opravy chyb. Společnost Microsoft doporučuje zákazníkům, kteří používají k plánování pro přesun z ho do Azure AD Connect. Důrazně doporučujeme nespustí všechna nová nasazení jeho použití. Tento konektor budou oznámeny zastaralé v budoucnu.

## <a name="additional-resources"></a>Další prostředky
* [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md)

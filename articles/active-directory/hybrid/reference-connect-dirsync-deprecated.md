---
title: Upgrade z nástroje DirSync a Azure AD Sync | Dokumentace Microsoftu
description: Popisuje, jak upgradovat z nástroje DirSync a Azure AD Sync na Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: bd68fb88-110b-4d76-978a-233e15590803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1ea3f0bccfe18f026926f6fabe37d6976ba6269a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55203721"
---
# <a name="upgrade-windows-azure-active-directory-sync-and-azure-active-directory-sync"></a>Upgrade Windows Azure Active Directory Sync a Azure Active Directory Sync
Azure AD Connect je nejlepší způsob, jak propojit místní adresář s Azure AD a Office 365. Toto je nejvhodnější doba pro upgrade na Azure AD Connect z Windows Azure Active Directory Sync (DirSync) nebo Azure AD Sync, protože tyto nástroje jsou už zastaralé a od 13. dubna 2017 se už nepodporují.

Nástroje synchronizace dva identity, které se považují za zastaralé byly nabízí pro zákazníky s jednou doménovou strukturou (DirSync) a pro více doménových struktur a další pokročilé zákazníků (Azure AD Sync). Tyto starší nástroje byly nahrazeny jednoho řešení, která je dostupná ve všech případech: Azure AD Connect. Nabízí nové funkce, vylepšení funkcí a podporu pro nové scénáře. Abyste mohli pokračovat v synchronizaci dat místních identit do služby Azure AD a Office 365, důrazně doporučujeme upgradovat na Azure AD Connect. Microsoft nezaručuje tyto starší verze fungovat po 31. prosince 2017.

Poslední verze nástroje DirSync byla vydána v červenci 2014 a v květnu 2015 byla vydána v poslední verzi služby Azure AD Sync.

## <a name="what-is-azure-ad-connect"></a>Co je služba Azure AD Connect
Azure AD Connect je nástupcem nástroje DirSync a Azure AD Sync. Tyto dvě podporované kombinuje všechny scénáře. Další informace o řezu [integrace místních identit s Azure Active Directory](whatis-hybrid-identity.md).

## <a name="deprecation-schedule"></a>Plánu ukončování používání
| Datum | Poznámka |
| --- | --- |
| 13. dubna 2016 |Windows Azure Active Directory Sync (DirSync") a Microsoft Azure Active Directory Sync (Azure AD Sync") jsou ohlášené jako zastaralé. |
| 13. dubna 2017 |Podpora skončí. Zákazníci už budou moci otevřít případ podpory bez upgradu na Azure AD Connect poprvé. |
|Do 31. prosince 2017|Azure AD může už přijímají komunikaci od Windows Azure Active Directory Sync (DirSync") a Microsoft Azure Active Directory Sync (Azure AD Sync").

## <a name="how-to-transition-to-azure-ad-connect"></a>Postup přechodu k Azure AD Connect
Pokud používáte nástroj DirSync, existují dva způsoby, které můžete upgradovat: Místní upgrade a paralelní nasazení. Místní upgrade se doporučuje pro většinu zákazníků a pokud máte Nedávná operační systém a menší než 50 000 objektů. V ostatních případech se doporučuje provést paralelní nasazení, kde je přesunuty konfiguraci nástroje DirSync na nový server se systémem Azure AD Connect.

| Řešení | Scénář |
| --- | --- |
| [Upgrade z nástroje DirSync](how-to-dirsync-upgrade-get-started.md) |<li>Pokud máte existující server DirSync už běží.</li> |
| [Upgrade z Azure AD Sync](how-to-upgrade-previous-version.md) |<li>Pokud přesouváte ze služby Azure AD Sync.</li> |

Pokud chcete zjistit, jak provést místní upgrade z nástroje DirSync na Azure AD Connect, pak najdete v tomto videu Channel 9:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-in-place-upgrade-from-legacy-tools/player]
>
>

## <a name="faq"></a>Nejčastější dotazy
**Otázka: Můžu dostali e-mailové oznámení od týmu Azure a/nebo zprávy v Centru zpráv Office 365, ale já používám připojit.**  
Oznámení se také odeslala na zákazníky, kteří používají Azure AD Connect s číslem sestavení 1.0. \*.0 (pomocí verze pre-1.1). Společnost Microsoft doporučuje zákazníků, kteří mají aktuální verzí služby Azure AD Connect. [Automatický upgrade](how-to-connect-install-automatic-upgrade.md) , která byla zavedena v 1.1 usnadňuje vždy mít nejnovější verzi služby Azure AD Connect, které jsou nainstalované.

**Otázka: DirSync nebo Azure AD Sync kód přestane fungovat na 13. dubna 2017?**  
DirSync nebo Azure AD Sync, budou nadále fungovat na 13. dubna 2017.  Ale Azure AD může už nepřijímají komunikaci ze služby DirSync nebo Azure AD Sync po 31. prosince 2017.

**Otázka: Které verze nástroje DirSync můžete upgradovat z?**  
Podporuje se upgrade z libovolné verze nástroje DirSync aktuálně používá. 

**Otázka: Jak Azure AD Connector pro FIM nebo MIM?**  
Azure AD Connector pro FIM nebo MIM má **není** byla oznámena jako zastaralé. Je na **funkce zablokování**; přidat žádné nové funkce a přijímá žádné opravy chyb. Společnost Microsoft doporučuje zákazníci, kteří používají k plánování přesunout z něj do služby Azure AD Connect. Důrazně se doporučuje spustit všechna nová nasazení používat. Tento konektor bude oznámena v budoucnu zastaralé.

## <a name="additional-resources"></a>Další prostředky
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)

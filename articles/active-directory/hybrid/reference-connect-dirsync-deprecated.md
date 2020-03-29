---
title: Upgrade z DirSync a Azure AD Sync | Dokumenty společnosti Microsoft
description: Popisuje, jak upgradovat z DirSync a Azure AD Sync na Azure AD Connect.
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
ms.topic: reference
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 803fcc0161f2a092006e60db5a98f5bf18dce1c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60381174"
---
# <a name="upgrade-windows-azure-active-directory-sync-and-azure-active-directory-sync"></a>Upgrade Windows Azure Active Directory Sync a Azure Active Directory Sync
Azure AD Connect je nejlepší způsob, jak propojit místní adresář s Azure AD a Office 365. To je skvělý čas na upgrade na Azure AD Connect z Windows Azure Active Directory Sync (DirSync) nebo Azure AD Sync, protože tyto nástroje jsou teď zastaralé a už nejsou podporované od dubna 13, 2017.

Dva nástroje pro synchronizaci identit, které jsou zastaralé byly nabízeny pro zákazníky jedné doménové struktury (DirSync) a pro více doménových domén ových domén ových a dalších pokročilých zákazníků (Azure AD Sync). Tyto starší nástroje byly nahrazeny jediným řešením, které je k dispozici pro všechny scénáře: Azure AD Connect. Nabízí nové funkce, vylepšení funkcí a podporu pro nové scénáře. Aby bylo možné pokračovat v synchronizaci dat místní identity do Azure AD a Office 365, důrazně doporučujeme upgradovat na Azure AD Connect. Společnost Microsoft nezaručuje, že tyto starší verze budou fungovat po 31.

Poslední verze DirSync byla vydána v červenci 2014 a poslední verze Azure AD Sync byla vydána v květnu 2015.

## <a name="what-is-azure-ad-connect"></a>Co je služba Azure AD Connect
Azure AD Connect je nástupcem DirSync a Azure AD Sync. Kombinuje všechny scénáře, které tyto dva podporované. Další informace o tom najdete v [integrací místních identit pomocí služby Azure Active Directory](whatis-hybrid-identity.md).

## <a name="deprecation-schedule"></a>Plán vyřazení
| Datum | Poznámka |
| --- | --- |
| 13. dubna 2016April 13, 2016 |Synchronizace služby Windows Azure Active Directory ("DirSync") a Synchronizace Microsoft Azure Active Directory ("Azure AD Sync") se oznamují jako zastaralé. |
| 13. dubna 2017April 13, 2017 |Podpora končí. Zákazníci už nebudou moct otevřít případ podpory bez předchozího upgradu na Azure AD Connect. |
|31. prosince 2017December 31, 2017|Azure AD už nemusí přijímat komunikaci ze služby Windows Azure Active Directory Sync ("DirSync") a Microsoft Azure Active Directory Sync ("Azure AD Sync").

## <a name="how-to-transition-to-azure-ad-connect"></a>Jak přejít na Azure AD Connect
Pokud používáte DirSync, existují dva způsoby, jak můžete upgradovat: Vlastní upgrade a paralelní nasazení. Pro většinu zákazníků je doporučen a pokud máte poslední operační systém a méně než 50 000 objektů, doporučuje se upgrade na místě. V ostatních případech se doporučuje provést paralelní nasazení, kde je konfigurace DirSync přesunuta na nový server se systémem Azure AD Connect.

| Řešení | Scénář |
| --- | --- |
| [Upgrade z nástroje DirSync](how-to-dirsync-upgrade-get-started.md) |<li>Pokud máte existující server DirSync již spuštěn.</li> |
| [Upgrade ze synchronizace Azure AD](how-to-upgrade-previous-version.md) |<li>Pokud přecházíte z Azure AD Sync.</li> |

Pokud chcete vidět, jak provést vlastní upgrade z DirSync na Azure AD Connect, pak se podívejte na toto video channel 9:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-in-place-upgrade-from-legacy-tools/player]
>
>

## <a name="faq"></a>Nejčastější dotazy
**Otázka: Obdržel jsem e-mailové oznámení od týmu Azure a/nebo zprávu z Centra zpráv Office 365, ale používám Connect.**  
Oznámení bylo také odesláno zákazníkům pomocí Azure AD Connect s číslem sestavení 1.0. \*0 (pomocí verze pre-1.1). Společnost Microsoft doporučuje zákazníkům, aby zůstali aktuální s verzemi Azure AD Connect. Funkce [automatického upgradu](how-to-connect-install-automatic-upgrade.md) zavedená v 1.1 usnadňuje vždy nainstalovanou nejnovější verzi služby Azure AD Connect.

**Otázka: Přestane DirSync/Azure AD Sync fungovat 13.**  
13. dubna 2017 bude služba DirSync/Azure AD Sync pokračovat v práci.  Azure AD však již nemusí přijímat komunikaci z DirSync/Azure AD Sync po 31 prosince 2017.

**Otázka: Ze kterých verzí DirSync mohu upgradovat?**  
Je podporován pro upgrade z libovolné verze DirSync, která je právě používána. 

**Otázka: A co konektor Azure AD pro FIM/MIM?**  
Konektor Azure AD pro FIM/MIM **nebyl** ahlášen jako zastaralé. Je na **funkci zmrazení**; nejsou přidány žádné nové funkce a neobdrží žádné opravy chyb. Společnost Microsoft doporučuje zákazníkům, kteří ji používají k plánování přechodu z něj do služby Azure AD Connect. Důrazně doporučujeme nespouštět žádná nová nasazení, která by používala. Tento konektor bude v budoucnu oznámen zastaralá.

## <a name="additional-resources"></a>Další zdroje
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)

---
title: Upgrade z DirSync a Azure AD Sync | Microsoft Docs
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
ms.openlocfilehash: 713ec3a4020434fa73aad2e04676129cf43853be
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165837"
---
# <a name="upgrade-windows-azure-active-directory-sync-and-azure-active-directory-sync"></a>Upgrade Windows Azure Active Directory Sync a Azure Active Directory Sync
Azure AD Connect je nejlepší způsob, jak propojit místní adresář s Azure AD a Microsoft 365. Jedná se o skvělou dobu pro upgrade na Azure AD Connect ze služby Windows Azure Active Directory Sync (DirSync) nebo Azure AD Sync (AADSync), protože tyto nástroje jsou už zastaralé a už nejsou podporované od 13. dubna 2017.

Pro zákazníky s jednou doménovou strukturou (DirSync) a pro více doménovou strukturu a další pokročilé zákazníky (Azure AD Sync) se nabídly dva nástroje pro synchronizaci identity, které jsou zastaralé. Tyto starší nástroje byly nahrazeny jedním řešením, který je k dispozici pro všechny scénáře: Azure AD Connect. Nabízí nové funkce, vylepšení funkcí a podporu pro nové scénáře. Aby bylo možné pokračovat v synchronizaci vašich místních dat identity s Azure AD a Microsoft 365, důrazně doporučujeme upgradovat na Azure AD Connect. Společnost Microsoft nezaručuje, že tyto starší verze budou fungovat až do 31. prosince 2017.

Poslední vydání DirSync bylo vydány v červenci 2014 a poslední vydání Azure AD Sync bylo vydáno v květnu 2015.

## <a name="what-is-azure-ad-connect"></a>Co je služba Azure AD Connect
Azure AD Connect je následníkem DirSync a Azure AD Sync. Kombinuje všechny scénáře, které jsou tyto dvě podporovány. Další informace o této službě najdete v tématu [Integrace místních identit s Azure Active Directory](whatis-hybrid-identity.md).

## <a name="deprecation-schedule"></a>Plán vyřazení
| Datum | Komentář |
| --- | --- |
| 13. dubna 2016 |Windows Azure Active Directory Sync ("DirSync") a Microsoft Azure Active Directory Sync ("Azure AD Sync") jsou vyhlášena jako zastaralá. |
| 13. dubna 2017 |Podpora končí. Zákazníci už nebudou moct otevřít případ podpory bez upgradu na Azure AD Connect jako první. |
|31. prosince 2017|Služba Azure AD už nemusí přijímat komunikaci ze služby Windows Azure Active Directory Sync (DirSync) a Microsoft Azure Active Directory synchronizaci ("Azure AD Sync").

## <a name="how-to-transition-to-azure-ad-connect"></a>Přechod na Azure AD Connect
Pokud používáte DirSync, existují dva způsoby, jak můžete upgradovat: místní upgrade a paralelní nasazení. Pro většinu zákazníků se doporučuje místní upgrade a pokud máte poslední operační systém a méně než 50 000 objektů. V ostatních případech se doporučuje provést paralelní nasazení, kde se vaše konfigurace DirSync přesune na nový server se systémem Azure AD Connect.

| Řešení | Scenario |
| --- | --- |
| [Upgrade z nástroje DirSync](how-to-dirsync-upgrade-get-started.md) |<li>Máte-li již existující server DirSync spuštěn.</li> |
| [Upgrade z Azure AD Sync](how-to-upgrade-previous-version.md) |<li>Pokud přesouváte z Azure AD Sync.</li> |

Pokud se chcete podívat, jak provést místní upgrade z DirSync na Azure AD Connect, přečtěte si toto video o kanálu 9:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-in-place-upgrade-from-legacy-tools/player]
>
>

## <a name="faq"></a>Nejčastější dotazy
**Otázka: jsem přijal e-mailové oznámení z týmu Azure nebo zprávy z centra zpráv Microsoft 365, ale používám Connect.**  
Oznámení bylo odesláno také zákazníkům, kteří používají Azure AD Connect s číslem sestavení 1,0. \* . 0 (s využitím verze před 1,1). Microsoft doporučuje zákazníkům aktuálnost Azure AD Connect verzí. Funkce [automatického upgradu](how-to-connect-install-automatic-upgrade.md) představená v 1,1 usnadňuje vždy instalaci nejnovější verze Azure AD Connect.

**Otázka: bude DirSync/Azure AD Sync přestat fungovat 13. dubna 2017?**  
DirSync/Azure AD Sync bude i nadále fungovat 13. dubna 2017.  Služba Azure AD však již nesmí přijímat komunikaci z DirSync/Azure AD Sync po 31. prosince 2017.

**Otázka: které verze DirSync můžu upgradovat?**  
Podporuje se upgrade z aktuálně používané verze DirSync. 

**Otázka: co je to konektor Azure AD pro FIM/MIM?**  
Služba Azure AD Connector pro FIM/ **MIM se** neoznámila jako zastaralá. Je na **zablokované funkci**; nepřidaly se žádné nové funkce a neobdrží žádné opravy chyb. Microsoft doporučuje zákazníkům, aby se z něho naplánovali, aby se přesunuli na Azure AD Connect. Důrazně doporučujeme, abyste nespouštěli nová nasazení pomocí této aplikace. Tento konektor bude v budoucnu ohlášen jako zastaralý.

## <a name="additional-resources"></a>Další materiály
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)

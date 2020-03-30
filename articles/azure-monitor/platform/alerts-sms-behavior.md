---
title: Chování výstrahy SMS ve skupinách akcí
description: Formát SMS zpráv a odpovídání na SMS zprávy pro odhlášení, opětovný odběr nebo žádost o pomoc.
author: dkamstra
ms.author: dukek
services: monitoring
ms.topic: conceptual
ms.date: 02/16/2018
ms.subservice: alerts
ms.openlocfilehash: b75bda626f887f1224c1b0f18a80887983a2367d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665302"
---
# <a name="sms-alert-behavior-in-action-groups"></a>Chování upozornění sms ve skupinách akcí

## <a name="overview"></a>Přehled 
Skupiny akcí umožňují konfigurovat seznam akcí. Tyto skupiny se používají při definování výstrah; zajistit, aby byla konkrétní akční skupina informována o aktivaci záznamu. Jednou z podporovaných akcí je SMS; SMS oznámení podporují obousměrnou komunikaci. Uživatel může odpovědět na SMS na:

- **Odhlásit se od upozornění:** Uživatel se může odhlásit ze všech upozornění sms pro všechny skupiny akcí nebo z jedné skupiny akcí.
- **Znovu se přihlásit k odběru upozornění:** Uživatel se může znovu přihlásit k odběru všech upozornění sms pro všechny skupiny akcí nebo pro jednu skupinu akcí.  
- **Požádat o pomoc:** Uživatel může požádat o další informace o SMS. Jsou přesměrováni na tento článek.

Tento článek popisuje chování upozornění SMS a akce odezvy, které může uživatel provést na základě národního prostředí uživatele:

## <a name="receiving-an-sms-alert"></a>Příjem upozornění sms
Přijímač SMS nakonfigurovaný jako součást skupiny akcí obdrží sms při aktivaci výstrahy. Sms obsahuje následující informace:
* Zkrácený název skupiny akcí, které byla tato výstraha odeslána
* Název záznamu

| REPLY | Popis |
| ----- | ----------- |
| Zakázat`<Action Group Short name>` | Zakáže další SMS ze skupiny akcí |
| Povolit`<Action Group Short name>` | Znovu povolí SMS z akční skupiny |
| Stop | Zakáže další SMS ze všech skupin akcí. |
| Spustit | Znovu povolí SMS ze všech akčních skupin |
| HELP | Odpověď je odeslána uživateli s odkazem na tento článek. |

>[!NOTE]
>Pokud se uživatel odhlásil ze upozornění SMS, ale pak je přidán do nové skupiny akcí. budou dostávat SMS upozornění pro tuto novou akční skupinu, ale zůstávají odhlášeni od všech předchozích akčních skupin.

## <a name="next-steps"></a>Další kroky
Získejte [přehled upozornění v protokolu aktivit](alerts-overview.md) a zjistěte, jak se dostat do upozornění  
Další informace o [omezení sms rychlosti](alerts-rate-limiting.md)  
Další informace o [skupinách akcí](../../azure-monitor/platform/action-groups.md)


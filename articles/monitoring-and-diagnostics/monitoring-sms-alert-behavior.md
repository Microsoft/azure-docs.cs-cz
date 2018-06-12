---
title: SMS výstrahy chování ve skupinách, akce
description: Formát zprávy SMS a odpovídat na zprávy SMS k odhlášení odběru, obnovit předplatné nebo požádat o pomoc.
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: f2f463f6c428ce6c72e2640472376fa17a2bfe5a
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263003"
---
# <a name="sms-alert-behavior-in-action-groups"></a>SMS výstrahy chování ve skupinách, akce
## <a name="overview"></a>Přehled ##
Akce skupiny umožňují nakonfigurovat seznam akcí. Tyto skupiny se používá při definování výstrah; zajištění, že konkrétní akce skupiny zasláno oznámení, když je výstraha. Jednu z akcí podporovaných je SMS; Oznámení SMS podporují jednosměrnou komunikaci. Uživatel může reagovat na serveru služby SMS na:

- **Odběr upozornění:** uživatel může odběr všech upozornění služby SMS pro všechny akce, nebo jednu akci skupiny.
- **Obnovit předplatné výstrah:** uživatel může obnovit předplatné na všechny výstrahy služby SMS pro všechny skupiny akce nebo skupinu jedné akce.  
- **Požádat o pomoc:** uživatel může požádat o další informace o serveru SMS. Je přesměrován na tomto článku.

Tento článek se zabývá chování výstrahy SMS a akce odpovědi uživatele můžete provádět na základě národního prostředí uživatele:

## <a name="receiving-an-sms-alert"></a>Příjem oznámení SMS
Přijímače SMS, který je nakonfigurovaný jako součást skupiny akce obdrží serveru služby SMS, když se výstraha. Serveru SMS obsahuje následující informace:
* Shortname skupiny akce, které tato výstraha byla odeslána
- Název výstrahy

| ODPOVĚĎ | Popis |
| ----- | ----------- |
| ZAKÁZAT <Action Group Short name> | Zakáže další SMS ze skupiny akce |
| POVOLIT <Action Group Short name> | Znovu povolí SMS ze skupiny akce |
| STOP | Zakáže další SMS ze všech skupin, akce |
| SPUŠTĚNÍ | Znovu povolí SMS ze všech skupin, akce |
| NÁPOVĚDA | Uživateli se zobrazí odkaz na tento článek je odeslána odpověď. |

>[!NOTE]
>Pokud uživatel odhlásil(a) odběr z výstrah služby SMS, ale se pak přidá do nové skupiny akce; BUDE přijímat výstrahy služby SMS pro tuto novou skupinu akce ale zůstávají odhlásit ze všech skupin, předchozí akce.

## <a name="next-steps"></a>Další kroky
Získat [přehled výstrah aktivity protokolu](monitoring-overview-alerts.md) a zjistěte, jak získat výstrahy  
Další informace o [omezení rychlosti SMS](monitoring-alerts-rate-limiting.md)  
Další informace o [skupiny akcí](monitoring-action-groups.md)

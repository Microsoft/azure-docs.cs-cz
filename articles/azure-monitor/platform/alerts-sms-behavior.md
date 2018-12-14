---
title: Chování výstrah SMS ve skupinách akcí
description: Formát zprávy SMS a reaguje na zprávy SMS k odhlášení odběru, obnovit předplatné nebo požádat o pomoc.
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: 481340dbab6a2ae7247a53cb78b17f3353edcd00
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53345994"
---
# <a name="sms-alert-behavior-in-action-groups"></a>SMS upozornění chování ve skupinách akcí
## <a name="overview"></a>Přehled ##
Skupiny akcí umožňují nakonfigurovat seznam akcí. Tyto skupiny se používají při definování oznámení; zajištění, že konkrétní akci skupiny zasláno oznámení, když se aktivuje upozornění. Jednou z akcí podporovaných je SMS; Oznámení SMS podporují obousměrné komunikace. Uživatel může odpovědět na zprávu SMS na:

- **Zrušit odběr upozornění:** Uživatel může zrušit odběr všechna oznámení SMS pro všechny skupiny akcí nebo skupinu jedné akce.
- **Nevybrala výstrah:** Uživatel může obnovit předplatné, pro všechny výstrahy služby SMS pro všechny skupiny akcí nebo skupinu jedné akce.  
- **Požádat o pomoc:** Uživatel může požádat o další informace o serveru SMS. Bude přesměrován na tomto článku.

Tento článek se týká chování výstrah SMS a odpověď akce, které uživateli umožňuje pořizovat závislosti na národním prostředí uživatele:

## <a name="receiving-an-sms-alert"></a>Přijetí upozornění zprávou SMS
Přijímače SMS, který je nakonfigurovaný jako součást skupiny akcí obdrží zprávu SMS, když se aktivuje upozornění. Serveru SMS obsahuje následující informace:
* Shortname skupinu akcí, kdy byla vyslána toto upozornění
- Název výstrahy

| ODPOVĚĎ | Popis |
| ----- | ----------- |
| ZAKÁZAT <Action Group Short name> | Další SMS zakáže skupiny akcí |
| POVOLIT <Action Group Short name> | Znovu povolí SMS skupiny akcí |
| STOP | Další SMS zakáže ze všech skupin akcí |
| SPUŠTĚNÍ | Znovu povolí SMS ze všech skupin akcí |
| POMOC | Odkazy na tento článek je uživateli odeslána odpověď. |

>[!NOTE]
>Pokud uživatel odhlásil(a) z výstrah SMS, ale se pak přidá do nové skupiny akcí; BUDE dostávat oznámení SMS pro tuto nová skupina akcí ale zůstanou odhlásit ze všech skupin pro předchozí akci.

## <a name="next-steps"></a>Další kroky
Získat [přehled upozornění protokolu aktivit](alerts-overview.md) a zjistěte, jak nechte se upozornit,  
Další informace o [rychlosti SMS](alerts-rate-limiting.md)  
Další informace o [skupiny akcí](../../azure-monitor/platform/action-groups.md)

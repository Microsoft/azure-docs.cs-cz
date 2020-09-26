---
title: Sledování průběhu projektů vědeckého zpracování týmových dat
description: Jak mohou správci skupin pro datové vědy, vedoucí týmu a vedoucí projektu sledovat průběh projektu pro datové vědy.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 78a543fabadcc0d4e1766af1bc5c65aac0dadebe
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91358921"
---
# <a name="track-the-progress-of-data-science-projects"></a>Sledování průběhu projektů pro datové vědy

Manažeři skupin pro datové vědy, vedoucí týmu a vedoucí projektu můžou sledovat průběh svých projektů.  Manažeři chtějí zjistit, jakou práci jste dokončili, kdo pracoval a jaká práce zůstává.   Správa očekávání je důležitým prvkem úspěchu.

## <a name="azure-devops-dashboards"></a>Řídicí panely Azure DevOps

Pokud používáte Azure DevOps, můžete vytvořit řídicí panely, které sledují aktivity a pracovní položky přidružené k danému agilnímu projektu. Další informace o řídicích panelech najdete v tématu [řídicí panely, sestavy a widgety](/azure/devops/report/dashboards/).

Pokyny k vytvoření a přizpůsobení řídicích panelů a widgetů v Azure DevOps najdete v následujících rychlých startech:

- [Přidávání a Správa řídicích panelů](/azure/devops/report/dashboards/dashboards)
- [Přidání widgetů na řídicí panel](/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>Ukázkový řídicí panel

Tady je jednoduchý příklad řídicího panelu, který sleduje aktivity sprintu projektu agilního zpracování dat, včetně počtu potvrzení změn přidružených k úložištím. 

- Dlaždice **odpočítávání** zobrazuje počet dní, které zůstanou v aktuálním sprintu. 

- Dlaždice se dvěma **kódy** zobrazují počet potvrzení ve dvou úložištích projektu za posledních sedm dní. 

- **Projekt pracovních položek pro zákazníka TDSP** zobrazuje výsledky dotazu pro všechny pracovní položky a jejich stav. 

- **Kumulativní vývojový diagram** (CFD) zobrazuje počet uzavřených a aktivních pracovních položek.

- V **grafu burndown** se zobrazuje práce, která se pořád dokončuje ve zbývajícím čase v sprintu.

- **Graf burnup** znázorňuje dokončenou práci ve srovnání s celkovým množstvím práce v sprintu.

![Snímek obrazovky ukazuje příklad řídicího panelu Azure DevOps.](./media/track-progress/dashboard.png)

## <a name="next-steps"></a>Další kroky

[Návody, kterými se spouští vědecké zpracování týmových dat](walkthroughs.md) , uvádí postupy, které ukazují všechny kroky procesu. Propojené scénáře ukazují, jak spravovat cloudové a místní prostředky do inteligentních aplikací. 

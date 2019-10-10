---
title: Sledování průběhu projektů vědeckého zpracování týmových dat
description: Jak mohou správci skupin pro datové vědy, vedoucí týmu a vedoucí projektu sledovat průběh projektu pro datové vědy.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/26/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 28bd3e558294c000ba65a1c60fe227bbae7e82dd
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244061"
---
# <a name="track-the-progress-of-data-science-projects"></a>Sledování průběhu projektů pro datové vědy

Manažeři skupin pro datové vědy, vedoucí týmu a vedoucí projektu mohou sledovat průběh svých projektů, jako je práce, která byla dokončena, kdo pracoval a jaká práce zůstává. 

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

![Řídicí panel](./media/track-progress/dashboard.png)

## <a name="next-steps"></a>Další kroky

[Návody, kterými se spouští vědecké zpracování týmových dat](walkthroughs.md) , uvádí seznam návodů, které ukazují všechny kroky v procesu pro konkrétní scénáře s odkazy a popisy miniatur. Propojené scénáře znázorňují, jak kombinovat cloudové a místní nástroje a služby do pracovních postupů nebo kanálů k vytváření inteligentních aplikací. 

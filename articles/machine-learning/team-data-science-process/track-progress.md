---
title: Sledování průběhu projektů procesu vědeckého zpracování týmových dat
description: Jak správci skupiny datových věd, vedoucí týmu a vedoucí projektu mohou sledovat průběh projektu datové vědy.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8cf1e5a4d97b882e7a8d0c81041bbcde709760d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864192"
---
# <a name="track-the-progress-of-data-science-projects"></a>Sledování pokroku v projektech datové vědy

Manažeři skupindatových věd, vedoucí týmů a zájemci projektů mohou sledovat průběh svých projektů.  Manažeři chtějí vědět, co bylo vykonáno, kdo práci odvedl a jaká práce zůstává.   Řízení očekávání je důležitým prvkem úspěchu.

## <a name="azure-devops-dashboards"></a>Řídicí panely Azure DevOps

Pokud používáte Azure DevOps, můžete vytvořit řídicí panely ke sledování aktivit a pracovních položek přidružených k danému agilnímu projektu. Další informace o řídicích panelech naleznete v [tématu Řídicí panely, sestavy a widgety](/azure/devops/report/dashboards/).

Pokyny k vytváření a přizpůsobení řídicích panelů a widgetů v Azure DevOps najdete v následujících rychlých startech:

- [Přidání a správa řídicích panelů](/azure/devops/report/dashboards/dashboards)
- [Přidání widgetů na řídicí panel](/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>Ukázkový řídicí panel

Zde je jednoduchý příklad řídicího panelu, který sleduje sprint aktivity projektu agilní datové vědy, včetně počtu potvrzení do přidružených úložišť. 

- Dlaždice **odpočítávání** zobrazuje počet dní, které zůstávají v aktuálním sprintu. 

- Dvě **dlaždice kódu** zobrazují počet potvrzení ve dvou úložištích projektu za posledních sedm dní. 

- **Pracovní položky pro projekt zákazníka TDSP** zobrazuje výsledky dotazu pro všechny pracovní položky a jejich stav. 

- **Kumulativní vývojový diagram** (CFD) zobrazuje počet uzavřených a aktivních pracovních položek.

- **Graf úbytku práce** zobrazuje práci, která je stále dokončena proti zbývajícímu času ve sprintu.

- **Graf vyhoření** zobrazuje dokončenou práci ve srovnání s celkovým množstvím práce ve sprintu.

![Řídicí panel](./media/track-progress/dashboard.png)

## <a name="next-steps"></a>Další kroky

[Návody provádějící proces vědeckého procesu týmových dat](walkthroughs.md) uvádí návody, které ukazují všechny kroky procesu. Propojené scénáře ilustrují, jak spravovat cloudové a místní prostředky do inteligentních aplikací. 

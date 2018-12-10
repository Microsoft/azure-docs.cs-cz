---
title: Sledování průběhu projekty datových věd - vědecké zpracování týmových dat
description: Jak správci skupiny datové vědy, týmových zapůjčení a zájemci projektu můžete sledovat průběh projektu datové vědy.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/28/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a9616a4f80a3105118b82ce3f4106a65fdd9ddfe
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53134297"
---
# <a name="tracking-the-progress-of-data-science-projects"></a>Sledování průběhu projekty datových věd

Data science skupiny správců, týmů a nutnost zájemci projektu sledovat průběh své projekty, jaká práce bylo provedeno na nich a kým a zůstává na seznamy úkolů. 

## <a name="azure-devops-dashboards"></a>Řídicích panelů Azure DevOps
Pokud používáte Azure DevOps, budete moci sestavovat řídicí panely pro sledování aktivity a pracovní položky přidružené k dané agilní projektů. 

Další informace o tom, jak vytvářet a přizpůsobovat řídicích panelů a widgetů na Azure DevOps najdete v následující sadu pokynů:

- [Přidávat a spravovat řídicí panely](https://docs.microsoft.com/azure/devops/report/dashboards/dashboards)
- [Přidání pomůcek na řídicí panel](https://docs.microsoft.com/azure/devops/report/dashboards/add-widget-to-dashboard).

## <a name="example-dashboard"></a>Příklad řídicího panelu

Tady je jednoduchý příklad řídicí panel, který je určený pro sledování sprintové činnosti projektu agilní datové vědy a také počet potvrzení přidružené úložiště. **Horní levé straně** panelu ukazuje:

- odpočítávání aktuálního sprintu 
- Počet potvrzení pro každé úložiště za posledních 7 dní
- pracovní položky pro konkrétní uživatele. 

Zbývající panelů zobrazit kumulativní vývojový diagram (CFD), zbývající práci oproti času a burnup pro projekt:

- **Vlevo dole**: CFD množství práce v daném státě, zobrazuje schválené šedě, potvrzené modrou barvu a provádí zeleně.
- **Pravý horní**: burndown graf práce zbývající k dokončení a zbývající čas).
- **Vpravo dole**: burnup graf práce, která byla dokončena a celkové množství práce.

![řídicí panel](./media/track-progress/dashboard.png)

Popis postupu vytvoření těchto grafů, najdete v rychlých startů a kurzů v [řídicí panely](https://docs.microsoft.com/azure/devops/report/dashboards/).
 
## <a name="next-steps"></a>Další postup

Postupy, které popisují všechny kroky v procesu pro **konkrétních scénářů** jsou také k dispozici. Jsou uvedena v seznamu a propojené s Miniatura popisy v [příklad návody](walkthroughs.md) článku. Jejich ukazují, jak zkombinovat cloudové, místní nástroje a služby do pracovního postupu nebo kanálu pro vytváření inteligentních aplikací. 

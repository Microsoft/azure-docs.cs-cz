---
title: Provádění projekty datových věd - Azure Machine Learning | Dokumentace Microsoftu
description: Jak mezi odborníky přes data, můžete sledovat průběh projektu datové vědy.
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: deguhath
ms.openlocfilehash: 32390b05d2ec258a68ed4f53135399675105a7e9
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302081"
---
# <a name="track-progress-of-data-science-projects"></a>Sledování průběhu projekty datových věd

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

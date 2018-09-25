---
title: Správa výstrah a inteligentní skupinu stavů
description: Správa stavů výstrah a inteligentní skupiny instancí
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: f1c4e34f9c14a9ca273cd115c653f8075286ee2a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46994543"
---
# <a name="manage-alert-and-smart-group-states"></a>Správa výstrah a inteligentní skupinu stavů
Výstrahy ve službě Azure Monitor teď mají [výstrahy, stav a stav monitorování](https://aka.ms/azure-alerts-overview) a podobně, inteligentní skupiny mají [inteligentní stavu skupiny](https://aka.ms/smart-groups). Změny stavu jsou nyní zachycené v historii spojenou s příslušné výstrahy nebo inteligentní skupinu. Tento článek vás provede procesem změnu stavu pro výstrahy a inteligentní skupinu.

## <a name="change-the-state-of-an-alert"></a>Změnit stav upozornění
1. Stav výstrahy můžete změnit následující různými způsoby: 
    * Na stránce všechny výstrahy klikněte na zaškrtávací políčko vedle výstrahy, kterou chcete změnit stav a klikněte na tlačítko Změnit stav.   
    ![Monitorování](./media/monitoring-alerts-managing-alert-states/state-all-alerts.jpg)
    * Na stránce Podrobnosti výstrahy pro konkrétní instanci výstrahy můžete kliknout na změnu stavu   
    ![Monitorování](./media/monitoring-alerts-managing-alert-states/state-alert-details.jpg)
    * Na stránce Podrobnosti výstrahy pro konkrétní instanci výstrahy v podokně Smart skupiny můžete můžete kliknout na zaškrtávací políčko vedle výstrahy, kterou požadujete    
    ![Monitorování](./media/monitoring-alerts-managing-alert-states/state-alert-details-sg.jpg)

    * Na stránce inteligentní podrobností skupiny v seznamu členů výstrah můžete klikněte na zaškrtávací políčko vedle výstrahy, kterou chcete změnit stav a klikněte na tlačítko Změnit Stateto změnit stav a klikněte na tlačítko Změnit stav.   
    ![Monitorování](./media/monitoring-alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. Po kliknutí na tlačítko Změnit stav, automaticky otevíraného okna otevře díky tomu můžete vybrat stav (nový/potvrzeno nebo Uzavřeno) a zadejte komentář v případě potřeby.   
![Monitorování](./media/monitoring-alerts-managing-alert-states/state-alert-change.jpg)
1. Až to uděláte, změny stavu se zaznamená do historie příslušné výstrahy. Zobrazením otevřete příslušnou stránku informace a kontroluje v části historie.    
![Monitorování](./media/monitoring-alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>Změna stavu inteligentní skupinu
1. Stav inteligentní skupiny můžete změnit následující různými způsoby:
    1. Na stránce seznamu Smart skupiny můžete kliknout na zaškrtávací políčko vedle inteligentní skupiny, kterou chcete změnit stav a klikněte na tlačítko Změnit stav  
    ![Monitorování](./media/monitoring-alerts-managing-alert-states/state-sg-list.jpg)
    1. Na stránce podrobností inteligentní skupiny můžete kliknout na změnu stavu        
    ![Monitorování](./media/monitoring-alerts-managing-alert-states/state-sg-details.jpg)
1. Po kliknutí na tlačítko Změnit stav, automaticky otevíraného okna otevře díky tomu můžete vybrat stav (nový/potvrzeno nebo Uzavřeno) a zadejte komentář v případě potřeby. 
![Monitorování](./media/monitoring-alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  Změna stavu inteligentní skupiny nezmění stav výstrahy jednotliví členové.

1. Až to uděláte, změny stavu se zaznamená do historie příslušné inteligentní skupiny. Zobrazením otevřete příslušnou stránku informace a kontroluje v části historie.     
![Monitorování](./media/monitoring-alerts-managing-alert-states/state-sg-history.jpg)

---
title: Správa stavů inteligentních skupin a upozornění
description: Správa stavů výstrah a inteligentní skupiny instancí
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: 2aa521b0552b60e5a875a5f46ab9887c6e5b6e3e
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54472054"
---
# <a name="manage-alert-and-smart-group-states"></a>Správa stavů inteligentních skupin a upozornění
Výstrahy ve službě Azure Monitor teď mají [výstrahy, stav a stav monitorování](https://aka.ms/azure-alerts-overview) a podobně, inteligentní skupiny mají [inteligentní stavu skupiny](https://aka.ms/smart-groups). Změny stavu jsou nyní zachycené v historii spojenou s příslušné výstrahy nebo inteligentní skupinu. Tento článek vás provede procesem změnu stavu pro výstrahy a inteligentní skupinu.

## <a name="change-the-state-of-an-alert"></a>Změnit stav upozornění
1. Stav výstrahy můžete změnit následující různými způsoby: 
    * Na stránce všechny výstrahy klikněte na zaškrtávací políčko vedle výstrahy, kterou chcete změnit stav a klikněte na tlačítko Změnit stav.   
    ![Monitorování](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * Na stránce Podrobnosti výstrahy pro konkrétní instanci výstrahy můžete kliknout na změnu stavu   
    ![Monitorování](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * Na stránce Podrobnosti výstrahy pro konkrétní instanci výstrahy v podokně Smart skupiny můžete můžete kliknout na zaškrtávací políčko vedle výstrahy, kterou požadujete    
    ![Monitorování](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * Na stránce inteligentní podrobností skupiny v seznamu členů výstrah můžete klikněte na zaškrtávací políčko vedle výstrahy, kterou chcete změnit stav a klikněte na tlačítko Změnit Stateto změnit stav a klikněte na tlačítko Změnit stav.   
    ![Monitorování](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. Po kliknutí na tlačítko Změnit stav, automaticky otevíraného okna otevře díky tomu můžete vybrat stav (nový/potvrzeno nebo Uzavřeno) a zadejte komentář v případě potřeby.   
![Monitorování](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. Až to uděláte, změny stavu se zaznamená do historie příslušné výstrahy. Zobrazením otevřete příslušnou stránku informace a kontroluje v části historie.    
![Monitorování](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>Změna stavu inteligentní skupinu
1. Stav inteligentní skupiny můžete změnit následující různými způsoby:
    1. Na stránce seznamu Smart skupiny můžete kliknout na zaškrtávací políčko vedle inteligentní skupiny, kterou chcete změnit stav a klikněte na tlačítko Změnit stav  
    ![Monitorování](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. Na stránce podrobností inteligentní skupiny můžete kliknout na změnu stavu        
    ![Monitorování](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. Po kliknutí na tlačítko Změnit stav, automaticky otevíraného okna otevře díky tomu můžete vybrat stav (nový/potvrzeno nebo Uzavřeno) a zadejte komentář v případě potřeby. 
![Monitorování](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  Změna stavu inteligentní skupiny nezmění stav výstrahy jednotliví členové.

1. Až to uděláte, změny stavu se zaznamená do historie příslušné inteligentní skupiny. Zobrazením otevřete příslušnou stránku informace a kontroluje v části historie.     
![Monitorování](./media/alerts-managing-alert-states/state-sg-history.jpg)


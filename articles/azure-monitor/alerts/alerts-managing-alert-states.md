---
title: Správa stavů inteligentních skupin a upozornění
description: Správa stavů výstrahy a instancí inteligentní skupiny
ms.topic: conceptual
author: anantr
ms.date: 09/24/2018
ms.openlocfilehash: cac926c3c56c7f58e5237b820e0328999ffe2c52
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033567"
---
# <a name="manage-alert-and-smart-group-states"></a>Správa stavů inteligentních skupin a upozornění

Výstrahy v Azure Monitor nyní mají [stav výstrahy a stav monitorování](./alerts-overview.md) , a podobně inteligentní skupiny mají [stav inteligentní skupiny](./alerts-smartgroups-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json). Změny stavu jsou nyní zachyceny v historii přidružené k příslušné výstraze nebo inteligentní skupině. Tento článek vás provede procesem změny stavu pro výstrahu i pro čipovou skupinu.

## <a name="change-the-state-of-an-alert"></a>Změna stavu výstrahy

1. Stav výstrahy můžete změnit následujícími různými způsoby: 
    * Na stránce všechny výstrahy klikněte na zaškrtávací políčko vedle výstrah, u kterých chcete změnit stav, a klikněte na změnit stav.   
    ![Snímek obrazovky s vybraným stavem změna zobrazí stránku všechna upozornění.](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * Na stránce Podrobnosti výstrahy konkrétní instance výstrahy můžete kliknout na změnit stav.   
    ![Snímek obrazovky zobrazující stránku podrobností výstrahy s vybraným stavem výstrahy změny.](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * Na stránce Podrobnosti výstrahy konkrétní instance výstrahy můžete v podokně inteligentní skupina kliknout na zaškrtávací políčko vedle výstrah, které chcete.    
    ![Snímek obrazovky s podrobnostmi o výstraze zobrazí stránku podrobností výstrahy s některými instancemi se značkami zaškrtnutí.](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * Na stránce Podrobnosti o inteligentní skupině v seznamu výstrah členů můžete kliknout na zaškrtávací políčko vedle výstrah, u kterých chcete změnit stav, a kliknout na změnit Stateto změnit stav a kliknout na změnit stav.   
    ![Snímek obrazovky se zobrazí stránka podrobností inteligentní skupiny, kde můžete vybrat výstrahy, pro které chcete změnit stav.](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. Při kliknutí na změnit stav se otevře automaticky otevírané okno, které vám umožní vybrat stav (nový/potvrzeno/uzavřeno) a v případě potřeby zadat komentář.   
![Snímek obrazovky se zobrazí dialogové okno upozornění na změnu podrobností.](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. Až to uděláte, změna stavu se zaznamená do historie příslušné výstrahy. To lze zobrazit otevřením příslušné stránky s podrobnostmi a zaškrtnutím části Historie.    
![Snímek obrazovky znázorňující historii změn stavu.](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>Změna stavu inteligentní skupiny
1. Stav inteligentní skupiny můžete změnit následujícími různými způsoby:
    1. Na stránce seznam inteligentních skupin můžete kliknout na zaškrtávací políčko vedle inteligentních skupin, u kterých chcete změnit stav, a kliknout na změnit stav.  
    ![Snímek obrazovky se zobrazením stránky stav změny pro skupiny Smart groups.](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. Na stránce Podrobnosti inteligentní skupiny můžete kliknout na změnit stav.        
    ![Snímek obrazovky se zobrazí stránka s podrobnostmi inteligentní skupiny se zaškrtnutým políčko změnit stav inteligentní skupiny.](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. Při kliknutí na změnit stav se otevře automaticky otevírané okno, které vám umožní vybrat stav (nový/potvrzeno/uzavřeno) a v případě potřeby zadat komentář. 
![Snímek obrazovky se zobrazí v dialogovém okně změnit stav pro inteligentní skupinu.](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  Změna stavu inteligentní skupiny nemění stav výstrah jednotlivých členů.

1. Až to uděláte, změna stavu se zaznamená do historie příslušné inteligentní skupiny. To lze zobrazit otevřením příslušné stránky s podrobnostmi a zaškrtnutím části Historie.     
![Snímek obrazovky znázorňující historii změn pro inteligentní skupinu.](./media/alerts-managing-alert-states/state-sg-history.jpg)
---
title: Správa stavů inteligentních skupin a upozornění
description: Správa stavů výstrahy a instancí inteligentní skupiny
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.date: 09/24/2018
ms.openlocfilehash: a22d7b7b962b36f93ee804c64b3bc6b08ecceb0f
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77667512"
---
# <a name="manage-alert-and-smart-group-states"></a>Správa stavů inteligentních skupin a upozornění

Výstrahy v Azure Monitor nyní mají [stav výstrahy a stav monitorování](https://aka.ms/azure-alerts-overview) , a podobně inteligentní skupiny mají [stav inteligentní skupiny](https://aka.ms/smart-groups). Změny stavu jsou nyní zachyceny v historii přidružené k příslušné výstraze nebo inteligentní skupině. Tento článek vás provede procesem změny stavu pro výstrahu i pro čipovou skupinu.

## <a name="change-the-state-of-an-alert"></a>Změna stavu výstrahy

1. Stav výstrahy můžete změnit následujícími různými způsoby: 
    * Na stránce všechny výstrahy klikněte na zaškrtávací políčko vedle výstrah, u kterých chcete změnit stav, a klikněte na změnit stav.   
    ![Monitorování](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * Na stránce Podrobnosti výstrahy konkrétní instance výstrahy můžete kliknout na změnit stav.   
    ![Monitorování](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * Na stránce Podrobnosti výstrahy konkrétní instance výstrahy můžete v podokně inteligentní skupina kliknout na zaškrtávací políčko vedle výstrah, které chcete.    
    ![Monitorování](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * Na stránce Podrobnosti o inteligentní skupině v seznamu výstrah členů můžete kliknout na zaškrtávací políčko vedle výstrah, u kterých chcete změnit stav, a kliknout na změnit Stateto změnit stav a kliknout na změnit stav.   
    ![Monitorování](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. Při kliknutí na změnit stav se otevře automaticky otevírané okno, které vám umožní vybrat stav (nový/potvrzeno/uzavřeno) a v případě potřeby zadat komentář.   
![Monitorování](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. Až to uděláte, změna stavu se zaznamená do historie příslušné výstrahy. To lze zobrazit otevřením příslušné stránky s podrobnostmi a zaškrtnutím části Historie.    
![Monitorování](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>Změna stavu inteligentní skupiny
1. Stav inteligentní skupiny můžete změnit následujícími různými způsoby:
    1. Na stránce seznam inteligentních skupin můžete kliknout na zaškrtávací políčko vedle inteligentních skupin, u kterých chcete změnit stav, a kliknout na změnit stav.  
    ![Monitorování](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. Na stránce Podrobnosti inteligentní skupiny můžete kliknout na změnit stav.        
    ![Monitorování](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. Při kliknutí na změnit stav se otevře automaticky otevírané okno, které vám umožní vybrat stav (nový/potvrzeno/uzavřeno) a v případě potřeby zadat komentář. 
![Monitorování](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  Změna stavu inteligentní skupiny nemění stav výstrah jednotlivých členů.

1. Až to uděláte, změna stavu se zaznamená do historie příslušné inteligentní skupiny. To lze zobrazit otevřením příslušné stránky s podrobnostmi a zaškrtnutím části Historie.     
![Monitorování](./media/alerts-managing-alert-states/state-sg-history.jpg)


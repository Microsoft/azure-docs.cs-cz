---
title: Správa stavů inteligentních skupin a upozornění
description: Správa stavů instancí výstrah a inteligentních skupin
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.date: 09/24/2018
ms.openlocfilehash: a22d7b7b962b36f93ee804c64b3bc6b08ecceb0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667512"
---
# <a name="manage-alert-and-smart-group-states"></a>Správa stavů inteligentních skupin a upozornění

Výstrahy v Azure Monitor u teď mají [stav výstrahy a stav monitorování](https://aka.ms/azure-alerts-overview) a podobně inteligentní skupiny mají stav inteligentní [skupiny](https://aka.ms/smart-groups). Změny stavu jsou nyní zachyceny v historii přidružené k příslušné výstrahy nebo inteligentní skupiny. Tento článek vás provede procesem změny stavu, a to jak pro výstrahu, tak pro inteligentní skupinu.

## <a name="change-the-state-of-an-alert"></a>Změna stavu výstrahy

1. Stav výstrahy můžete změnit následujícími způsoby: 
    * Na stránce Všechny výstrahy klikněte na zaškrtávací políčko vedle výstrah, které chcete změnit, a klikněte na Změnit stav.   
    ![Monitorování](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * Na stránce Podrobnosti výstrahy pro konkrétní instanci výstrahy můžete kliknout na změnit stav.   
    ![Monitorování](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * Na stránce Podrobnosti výstrahy pro konkrétní instanci výstrah můžete v podokně Inteligentní skupina kliknout na zaškrtávací políčko vedle upozornění, která chcete    
    ![Monitorování](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * Na stránce Podrobnosti inteligentní skupiny můžete v seznamu upozornění členů kliknout na zaškrtávací políčko vedle výstrah, které chcete změnit, a kliknout na Změnit stav a změnit stav a kliknout na Změnit stav.   
    ![Monitorování](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. Po kliknutí na tlačítko Změnit stav se otevře vyskakovací okno, které vám umožní vybrat stav (Nový/Potvrzovaný/Uzavřený) a v případě potřeby zadat komentář.   
![Monitorování](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. Jakmile je to hotovo, změna stavu je zaznamenána v historii příslušné výstrahy. To lze zobrazit otevřením příslušné stránky Podrobnosti a kontrolou sekce historie.    
![Monitorování](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>Změna stavu inteligentní skupiny
1. Stav inteligentní skupiny můžete změnit různými způsoby:
    1. Na stránce se seznamem Inteligentní skupina můžete kliknout na zaškrtávací políčko vedle inteligentních skupin, které chcete změnit stav, a kliknout na Změnit stav.  
    ![Monitorování](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. Na stránce Podrobnosti inteligentní skupiny můžete kliknout na změnit stav        
    ![Monitorování](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. Po kliknutí na tlačítko Změnit stav se otevře vyskakovací okno, které vám umožní vybrat stav (Nový/Potvrzovaný/Uzavřený) a v případě potřeby zadat komentář. 
![Monitorování](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  Změna stavu inteligentní skupiny nezmění stav jednotlivých výstrah členů.

1. Jakmile je to hotovo, změna stavu je zaznamenána v historii příslušné inteligentní skupiny. To lze zobrazit otevřením příslušné stránky Podrobnosti a kontrolou sekce historie.     
![Monitorování](./media/alerts-managing-alert-states/state-sg-history.jpg)


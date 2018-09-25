---
title: Správa výstrah instancí
description: Správa instancí upozornění v Azure
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: 614e65ca791559f4649963ee82940c3f96beddb3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948447"
---
# <a name="manage-alert-instances"></a>Správa výstrah instancí
S [sjednocené prostředí upozornění](https://aka.ms/azure-alerts-overview) ve službě Azure Monitor, nyní je vidět všechny různé typy výstrah v Azure, zahrnující více předplatných, v jednom jeden podokně ze skla. Tento článek vás provede s vaší výstrah instance, jak můžete zobrazit a tom, jak podrobně nyní popíšeme portál k vyhledání konkrétních instancí upozornění pro řešení potíží.

1. Existují tři způsoby, jak přejít na stránky s upozorněními

   + V [portál](https://portal.azure.com/)vyberte **monitorování** a v části monitorování – tlačítko **výstrahy**.  
    ![Monitorování](./media/monitoring-alerts-managing-alerts-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
   + Můžete přejít na výstrahy z v rámci konkrétní **prostředků**. Po otevření prostředku procházet své tabulky obsah v části monitorování a zvolte **výstrahy**, s cílovou stránkou předem filtrovanou pro výstrahy týkající se tento konkrétní prostředek.
   
    ![Monitorování](./media/monitoring-alerts-managing-alerts-instances/alert-resource.JPG)
    
   + Můžete přejít na výstrahy z v rámci konkrétní **skupiny prostředků**. Jakmile se otevře skupinu prostředků, procházet své tabulky obsah v části monitorování a zvolte **výstrahy**, s cílovou stránkou předem filtrovanou pro výstrahy týkající se této konkrétní skupině prostředků.    
   
    ![Monitorování](./media/monitoring-alerts-managing-alerts-instances/alert-rg.JPG)

1.  Tím přejdete **souhrn výstrah podle** stránky, která poskytuje přehled o všech instancí upozornění v Azure. Souhrnné zobrazení můžete upravit tak, že vyberete **více předplatných** (maximálně 5) nebo filtrování napříč **skupiny prostředků**konkrétní **prostředky**, nebo **časové rozsahy**. Klikněte na celkový počet upozornění nebo některý z pruhy závažnost přejdete na zobrazení seznamu pro upozornění.     
    ![Souhrn výstrah podle](./media/monitoring-alerts-managing-alerts-instances/alerts-summary.jpg)
 
1.  Tím přejdete **všechny výstrahy** stránku, kde se zobrazí všechny výstrahy instance v Azure vypsali. Pokud přecházíte na portál z oznámení výstrah, můžete použít filtry, které jsou k dispozici v zúžit na konkrétní instanci výstrahy. (**Poznámka**:-li na stránku kliknutím na jakékoli pruhy závažnosti, při zobrazí bude seznam předem filtrovanou pro tento závažnost). Kromě filtrů dostupných na předchozí stránce teď můžete také filtrovat na základě monitorování služby (například platformu pro metriky), monitorování stavu (aktivováno nebo vyřešené), závažnosti, stav výstrahy (nový/potvrzeny nebo Uzavřeno) nebo ID inteligentní skupiny.

    ![Všechny výstrahy](./media/monitoring-alerts-managing-alerts-instances/all-alerts.jpg)

    > [!NOTE]
    >  Pokud přišel na stránku kliknutím na jakékoli pruhy závažnost seznamu bude předem filtrovanou pro tento závažnost, když budete přesměrováni na této stránce.
 
1.  Kliknutím na libovolnou instanci výstrahy otevře **podrobnosti výstrahy** stránku, abyste mohli podrobně na informace o konkrétní instanci výstrahy.   
![Podrobnosti výstrahy](./media/monitoring-alerts-managing-alerts-instances/alert-details.jpg)  

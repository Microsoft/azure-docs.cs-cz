---
title: Správa instancí upozornění
description: Správa instancí upozornění v Azure
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: 0324fffdfaaa512c3610d5389d68dba2164f8d55
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54436446"
---
# <a name="manage-alert-instances"></a>Správa instancí upozornění
S [sjednocené prostředí upozornění](https://aka.ms/azure-alerts-overview) ve službě Azure Monitor, nyní je vidět všechny různé typy výstrah v Azure, zahrnující více předplatných, v jednom jeden podokně ze skla. Tento článek vás provede s vaší výstrah instance, jak můžete zobrazit a tom, jak podrobně nyní popíšeme portál k vyhledání konkrétních instancí upozornění pro řešení potíží.

1. Existují tři způsoby, jak přejít na stránky s upozorněními

   + V [portál](https://portal.azure.com/)vyberte **monitorování** a v části monitorování – tlačítko **výstrahy**.  
    ![Monitorování](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
   + Můžete přejít na výstrahy z v rámci konkrétní **prostředků**. Po otevření prostředku procházet své tabulky obsah v části monitorování a zvolte **výstrahy**, s cílovou stránkou předem filtrovanou pro výstrahy týkající se tento konkrétní prostředek.
   
    ![Monitorování](media/alerts-managing-alert-instances/alert-resource.JPG)
    
   + Můžete přejít na výstrahy z v rámci konkrétní **skupiny prostředků**. Jakmile se otevře skupinu prostředků, procházet své tabulky obsah v části monitorování a zvolte **výstrahy**, s cílovou stránkou předem filtrovanou pro výstrahy týkající se této konkrétní skupině prostředků.    
   
    ![Monitorování](media/alerts-managing-alert-instances/alert-rg.JPG)

1.  Tím přejdete **souhrn výstrah podle** stránky, která poskytuje přehled o všech instancí upozornění v Azure. Souhrnné zobrazení můžete upravit tak, že vyberete **více předplatných** (maximálně 5) nebo filtrování napříč **skupiny prostředků**konkrétní **prostředky**, nebo **časové rozsahy**. Klikněte na celkový počet upozornění nebo některý z pruhy závažnost přejdete na zobrazení seznamu pro upozornění.     
    ![Souhrn výstrah podle](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
1.  Tím přejdete **všechny výstrahy** stránku, kde se zobrazí všechny výstrahy instance v Azure vypsali. Pokud přecházíte na portál z oznámení výstrah, můžete použít filtry, které jsou k dispozici v zúžit na konkrétní instanci výstrahy. (**Poznámka**:-li na stránku kliknutím na jakékoli pruhy závažnosti, při zobrazí bude seznam předem filtrovanou pro tento závažnost). Kromě filtrů dostupných na předchozí stránce teď můžete také filtrovat na základě monitorování služby (například platformu pro metriky), monitorování stavu (aktivováno nebo vyřešené), závažnosti, stav výstrahy (nový/potvrzeny nebo Uzavřeno) nebo ID inteligentní skupiny.

    ![Všechny výstrahy](media/alerts-managing-alert-instances/all-alerts.jpg)

    > [!NOTE]
    >  Pokud přišel na stránku kliknutím na jakékoli pruhy závažnost seznamu bude předem filtrovanou pro tento závažnost, když budete přesměrováni na této stránce.
 
1.  Kliknutím na libovolnou instanci výstrahy otevře **podrobnosti výstrahy** stránku, abyste mohli podrobně na informace o konkrétní instanci výstrahy.   
![Podrobnosti výstrahy](media/alerts-managing-alert-instances/alert-details.jpg)  


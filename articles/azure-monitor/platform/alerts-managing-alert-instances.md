---
title: Správa instancí upozornění
description: Správa instancí výstrah napříč Azure
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: dbc7d37ce311d203ee051b5a23632d2bf401e27a
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034828"
---
# <a name="manage-alert-instances"></a>Správa instancí upozornění
Díky [jednotnému prostředí výstrah](https://aka.ms/azure-alerts-overview) v Azure monitor teď můžete v rámci Azure Zobrazit všechny vaše různé typy výstrah, a to s více předplatnými, a to v jednom podokně skla. Tento článek vás seznámí s tím, jak si můžete zobrazit instance výstrah a jak hluboko podrobně na portálu najít konkrétní instance výstrahy pro řešení potíží.

> [!NOTE]
   >  V uživatelském rozhraní nebo prostřednictvím rozhraní REST API můžete mít k dispozici jenom výstrahy vygenerované za posledních 30 dní.

1. Existují tři způsoby, jak obložit na stránce s výstrahami.

   + Na [portálu](https://portal.azure.com/)vyberte **monitorování** a v části Monitorování – zvolte **výstrahy**.  
     ![Monitorování](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
   + Můžete přejít na výstrahy z kontextu určitého **prostředku**. Po otevření prostředku přejděte přes jeho obsah do části monitorování a vyberte možnost **výstrahy**a u cílové stránky předem filtrované výstrahy na konkrétní prostředek.
   
     ![Monitorování](media/alerts-managing-alert-instances/alert-resource.JPG)
    
   + V rámci konkrétní **skupiny prostředků**můžete přejít na výstrahy. Po otevření skupiny prostředků přejděte přes její obsah do části monitorování a vyberte možnost **výstrahy**, u cílové stránky předem filtrované pro výstrahy v této konkrétní skupině prostředků.    
   
     ![Monitorování](media/alerts-managing-alert-instances/alert-rg.JPG)

1. Vydáte stránku souhrnu **výstrah** , která vám poskytne přehled o všech instancích upozornění v rámci Azure. Souhrnné zobrazení můžete upravit tak, že vyberete **více** předplatných (maximálně 5) nebo filtrování napříč **skupinami prostředků**, konkrétními **prostředky**nebo **časovými rozsahy**. Kliknutím na celkový počet výstrah nebo na kterýkoli z pásem závažnosti přejdete do zobrazení seznamu výstrah.     
   ![Souhrn výstrah](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
1. Na stránce **všechny výstrahy** budete mít na místě, kde uvidíte všechny instance výstrah v rámci Azure vypsané na maximum. Pokud přejdete na portál z oznámení výstrahy, můžete použít filtry, které jsou k dispozici pro zúžení v konkrétní instanci výstrahy. (**Poznámka**: Pokud jste na stránku dostali kliknutím na kterýkoli ze pásem závažnosti, seznam se při svém využití předem vyfiltruje.) Kromě filtrů dostupných na předchozí stránce teď můžete také filtrovat na základě služby monitorování (například platformy pro metriky), podmínky monitorování (aktivované nebo vyřešené), závažnosti, stavu výstrahy (nové/potvrzené/uzavřené) nebo ID inteligentní skupiny.

   ![Všechny výstrahy](media/alerts-managing-alert-instances/all-alerts.jpg)

   > [!NOTE]
   >  Pokud jste na stránku dostali kliknutím na kterékoli z pásem závažnosti, seznam se při obstání na této stránce předem vyfiltruje.
 
1. Kliknutím na libovolnou instanci výstrahy otevřete stránku **Podrobnosti výstrahy** , která vám umožní podrobné podrobně informace o konkrétní instanci výstrahy.   
   ![Podrobnosti výstrahy](media/alerts-managing-alert-instances/alert-details.jpg)  


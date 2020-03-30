---
title: Správa instancí výstrah v Azure Monitoru
description: Správa instancí výstrah v Azure
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 1910b3b474012365e8117f584379b2b29f8ce3de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667614"
---
# <a name="manage-alert-instances-with-unified-alerts"></a>Správa instancí výstrah pomocí jednotných výstrah

Díky [jednotnému prostředí pro výstrahy](https://aka.ms/azure-alerts-overview) ve službě Azure Monitor uvidíte všechny různé typy výstrah v Azure. To zahrnuje více předplatných v jednom podokně. Tento článek ukazuje, jak můžete zobrazit instance výstrah a jak najít konkrétní instance výstrah pro řešení potíží.

> [!NOTE]
> Přístup k výstrahy generované v posledních 30 dnech.

## <a name="go-to-the-alerts-page"></a>Přejít na stránku upozornění

Na stránku upozornění můžete přejít libovolným z následujících způsobů:

- Na [webu Azure Portal](https://portal.azure.com/)vyberte **Monitor** > **Alerts**.  

     ![Snímek obrazovky s výstrahami monitoru](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
- Použijte kontext konkrétního prostředku. Otevřete prostředek, přejděte do části **Monitorování** a zvolte **Výstrahy**. Vstupní stránka je předem filtrována pro výstrahy pro daný prostředek.

     ![Snímek obrazovky s výstrahami monitorování zdrojů](media/alerts-managing-alert-instances/alert-resource.JPG)

- Použijte kontext určité skupiny prostředků. Otevřete skupinu prostředků, přejděte do části **Monitorování** a zvolte **Výstrahy**. Vstupní stránka je předem filtrována pro výstrahy pro danou konkrétní skupinu prostředků.    

     ![Snímek obrazovky s výstrahami monitorování skupiny prostředků](media/alerts-managing-alert-instances/alert-rg.JPG)

## <a name="find-alert-instances"></a>Hledání instancí výstrah

Stránka **Souhrn výstrah** poskytuje přehled všech instancí výstrah v Azure. Souhrnné zobrazení můžete upravit výběrem **více předplatných** (maximálně 5) nebo filtrováním mezi **skupinami prostředků**, **konkrétními zdroji**nebo **časovými rozsahy**. Výběrem **možnosti Celkový počet výstrah**nebo libovolného pásma závažnosti přejdete do zobrazení seznamu výstrah.     

![Snímek obrazovky se stránkou Souhrn výstrah](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
Na stránce **Všechny výstrahy** jsou uvedeny všechny instance výstrah v rámci Azure. Pokud přicházíte na portál z upozornění, můžete použít filtry, které jsou k dispozici pro zúžení v této konkrétní instanci výstrahy.

> [!NOTE]
> Pokud jste na stránku přišli výběrem libovolného z pásem závažnosti, je seznam předem filtrován pro tuto závažnost.

Kromě filtrů dostupných na předchozí stránce můžete také filtrovat na základě služby monitorování (například platforma pro metriky), stavu monitorování (aktivováno nebo vyřešeno), závažnosti, stavu výstrah (nové/potvrzené/zavřené) nebo ID inteligentní skupiny.

![Snímek obrazovky se stránkou Všechny výstrahy](media/alerts-managing-alert-instances/all-alerts.jpg)

> [!NOTE]
> Pokud jste na stránku přišli výběrem libovolného z pásem závažnosti, je seznam předem filtrován pro tuto závažnost.

Výběrem libovolné instance výstrahy se otevře stránka **Podrobnosti výstrahy,** která vám umožní zobrazit další podrobnosti o konkrétní instanci výstrahy.   

![Snímek obrazovky se stránkou Podrobnosti výstrahy](media/alerts-managing-alert-instances/alert-details.jpg)  


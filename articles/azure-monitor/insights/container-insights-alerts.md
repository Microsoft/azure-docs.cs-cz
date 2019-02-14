---
title: Vytvoření výstrahy související s výkonem pomocí Azure monitoru pro kontejnery | Dokumentace Microsoftu
description: Tento článek popisuje, jak můžete vytvořit vlastní upozornění v Azure na základě dotazů protokolu pro využití paměti a procesoru ze služby Azure Monitor pro kontejnery.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2019
ms.author: magoedte
ms.openlocfilehash: c275d50ab927895eca3aa018b71ab6989a4dde5a
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56238852"
---
# <a name="how-to-set-up-alerts-in-azure-monitor-for-container-performance-problems"></a>Jak nastavit výstrahy ve službě Azure Monitor pro problémy s výkonem kontejneru
Azure Monitor pro monitorování kontejnerů výkon úloh kontejneru nasadit buď Azure Container Instances nebo spravovat clustery Kubernetes hostované ve službě Azure Kubernetes Service (AKS). 

Tento článek popisuje, jak povolit výstrahy, když využití procesoru a paměti na uzlech clusteru přesáhne definovanou prahovou hodnotu.

## <a name="create-alert-on-cluster-cpu-or-memory-utilization"></a>Vytvoření upozornění na clusteru CPU nebo využití paměti v aplikaci
Pro upozornění, když je pro cluster se vysoké využití procesoru nebo paměti, můžete vytvořit pravidlo upozornění metriky měření, který je založen na protokolu dotazy, které poskytuje. Porovnání datum a čas k dispozici pomocí dotazů `now` operátor a přejde zpět jednu hodinu. Všechna data uložená službou Azure Monitor pro kontejnery jsou ve formátu UTC.  

Před zahájením, pokud nejste obeznámeni s využitím upozornění ve službě Azure Monitor, přečtěte si téma [přehled výstrah v Microsoft Azure](../platform/alerts-overview.md). Další informace o výstrahách pomocí dotazů protokolu najdete v tématu [upozornění protokolů ve službě Azure Monitor](../platform/alerts-unified-log.md)

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **monitorování** z podokna vlevo na webu Azure Portal. V části **Insights** vyberte **kontejnery**.    
3. Z **monitorovat clustery** kartu, vybrat cluster, ze seznamu kliknutím na název clusteru.
4. V levém podokně v části **monitorování** vyberte **protokoly** otevřete Azure Monitor protokoly stránky, které usnadňují zápis a spouštění dotazů Azure Log Analytics.
5. Na **protokoly** klikněte na **+ nové pravidlo upozornění**.
6. V části **podmínku** části, klikněte na předem definované vlastní protokol podmínku **pokaždé, když se vlastní prohledávání protokolu je <logic undefined>** . **Vlastní protokol hledání** typ signálu se vybere automaticky pro nás, protože jsme spustili, vytváří se pravidlo upozornění přímo na stránce protokoly Azure monitoru.  
7. Vložte některá z dotazů níže do **vyhledávací dotaz** pole. Následující dotaz vypočítá průměrné využití procesoru jako průměr využití výkonu procesoru uzly člen každou minutu.

    ```
    let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let capacityCounterName = 'cpuCapacityNanoCores';
    let usageCounterName = 'cpuUsageNanoCores';
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    // cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
    | distinct ClusterName, Computer
    | join hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime
       | where TimeGenerated >= startDateTime
       | where ObjectName == 'K8SNode'
       | where CounterName == capacityCounterName
       | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
       | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
    ) on Computer
    | join kind=inner hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime + trendBinSize
       | where TimeGenerated >= startDateTime - trendBinSize
       | where ObjectName == 'K8SNode'
       | where CounterName == usageCounterName
       | project Computer, UsageValue = CounterValue, TimeGenerated
    ) on Computer
    | where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
    | project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
    | summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
    ```

    Následující dotaz vypočítá průměrné využití paměti v aplikaci jako průměr využití paměti uzlů člen každou minutu.

    ```
    let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let capacityCounterName = 'memoryCapacityBytes';
    let usageCounterName = 'memoryRssBytes';
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    // cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
    | distinct ClusterName, Computer
    | join hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime
       | where TimeGenerated >= startDateTime
       | where ObjectName == 'K8SNode'
       | where CounterName == capacityCounterName
       | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
       | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
    ) on Computer
    | join kind=inner hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime + trendBinSize
       | where TimeGenerated >= startDateTime - trendBinSize
       | where ObjectName == 'K8SNode'
       | where CounterName == usageCounterName
       | project Computer, UsageValue = CounterValue, TimeGenerated
    ) on Computer
    | where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
    | project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
    | summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
    ```

8. Nakonfigurujte upozornění podle následujících informací:

    a. V rozevíracím seznamu **Na základě** vyberte **Měření metriky**. Měření metriky vytvoří pro každý objekt dotazu upozornění s hodnotou, která překračuje zadanou prahovou hodnotu.  
    b. Pro **podmínku**vyberte **větší než** a zadejte **75** jako počáteční základní **prahová hodnota** nebo zadejte hodnotu, která splňuje vaše kritéria.  
    c. V části **aktivační událost výstrahy založené na** vyberte **po sobě jdoucí porušení** a z rozevíracího seznamu vyberte možnost **větší než** zadejte hodnotu **2**.  
    d. V části **Evaluated na základě** oddíl, změna **období** hodnotu na 60 minut. Toto pravidlo spustí každých pět minut a vrátí záznamy, které se vytvořily za poslední hodinu od aktuálního času. Nastavení delšího období zvyšuje potenciál latence dat a zajišťuje, aby dotaz vrátil data a aby se zabránilo falešně negativním hodnotám, kdy se výstraha nespustí. 

9. Klikněte na **Hotovo** a dokončete pravidlo upozornění.
10. Zadejte název upozornění v **název pravidla upozornění** pole. Zadejte **popis** s podrobnostmi o konkrétní výstrahu a vyberte odpovídající závažnost z možností.
11. Pokud chcete vytvořené pravidlo ihned aktivovat, potvrďte výchozí hodnotu přepínače **Po vytvoření povolit pravidlo**.
12. Na poslední krok, vyberte existující nebo vytvořte novou **skupiny akcí**, které zajišťuje, že stejné akce pocházejí pokaždé, když výstraha se aktivuje a lze použít pro každé pravidlo, které definujete. Konfigurace podle toho, jak tým IT nebo operace DevOps spravuje incidenty. 
13. K dokončení pravidla upozornění klikněte na **Vytvořit pravidlo upozornění**. Pravidlo se okamžitě spustí.

## <a name="next-steps"></a>Další postup

* Podívejte se na některé z [protokolu Příklady dotazů](container-insights-analyze.md#search-logs-to-analyze-data) Další informace o předdefinované dotazy nebo příklady vyhodnotit a použít nebo přizpůsobit pro jiné scénáře výstrah. 
* Chcete-li pokračovat v učení, jak používat Azure Monitor a monitorovat další aspekty vašeho clusteru AKS, přečtěte si téma [zobrazení Azure Kubernetes Service health](container-insights-analyze.md)

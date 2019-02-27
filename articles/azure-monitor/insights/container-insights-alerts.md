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
ms.date: 02/26/2019
ms.author: magoedte
ms.openlocfilehash: e6fdb0d57a44578647c1f16dc76c557296f20ddb
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56886759"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>Jak nastavit výstrahy pro problémy s výkonem ve službě Azure Monitor pro kontejnery
Azure Monitor pro monitorování kontejnerů výkon úloh kontejneru nasadit buď Azure Container Instances nebo spravovat clustery Kubernetes hostované ve službě Azure Kubernetes Service (AKS). 

Tento článek popisuje, jak povolit výstrahy v těchto situacích:

* Při využití procesoru a paměti na uzlech clusteru a překračuje definovanou prahovou hodnotu.
* Pokud využití procesoru nebo paměti v kontejnery v rámci kontroleru překročí vaše definovanou prahovou hodnotu než limit nastavený na odpovídající prostředek.

Pro upozornění, když je využití procesoru nebo paměti vysoké pro cluster nebo kontroleru, vytvoříte pravidlo upozornění metriky měření, který je založen na protokolu dotazy, které poskytuje. Dotazy porovnat hodnoty datetime až po současnost pomocí operátoru teď a vrátí jednu hodinu. Všechna data uložená službou Azure Monitor pro kontejnery jsou ve formátu UTC.

Před zahájením, pokud nejste obeznámeni s využitím upozornění ve službě Azure Monitor, přečtěte si téma [přehled výstrah v Microsoft Azure](../platform/alerts-overview.md). Další informace o výstrahách pomocí dotazů protokolu najdete v tématu [upozornění protokolů ve službě Azure Monitor](../platform/alerts-unified-log.md)

## <a name="resource-utilization-log-search-queries"></a>Vyhledávací dotazy protokolů využití prostředků
Dotazy v této části jsou k dispozici pro podporu jednotlivých výstrah scénářů. Dotazy jsou požadovány pro krok 7 v části [vytvoření upozornění](#create-alert-rule) níže v části.  

Následující dotaz vypočítá průměrné využití procesoru jako průměr využití výkonu procesoru uzly člen každou minutu.  

```kusto
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

```kusto
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
>[!IMPORTANT]
>Níže uvedené dotazy obsahovat zástupné hodnoty řetězce pro váš cluster a řadič názvy - < your clusteru name > a < your řadiče name >. Zástupné znaky nahraďte hodnotami, které jsou specifické pro vaše prostředí před nastavením oznámení. 


Následující dotaz vypočítá průměrné využití procesoru u všech kontejnerů v kontroleru jako průměr využití CPU každou instanci kontejneru v kontroleru každou minutu jako procento z limitu nastaveného pro kontejner.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

Následující dotaz vypočítá průměrné využití paměti v aplikaci všechny kontejnery v kontroleru jako průměr využití paměti v aplikaci každou instanci kontejneru v kontroleru každou minutu jako procento z limitu nastaveného pro kontejner.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

## <a name="create-alert-rule"></a>Vytvořit pravidlo upozornění
Proveďte následující kroky k vytvoření výstrah protokolu ve službě Azure Monitor, pomocí některé z pravidel vyhledávání protokolu jste zadali dříve.  

>[!NOTE]
>Následující postup je potřeba přepnout na nové rozhraní API upozornění Log, jak je popsáno v [předvoleb přepněte rozhraní API pro výstrahy protokolu](../platform/alerts-log-api-switch.md) Pokud vytváříte pravidlo upozornění pro využití prostředků kontejneru. 
>

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **monitorování** z podokna vlevo na webu Azure Portal. V části **Insights** vyberte **kontejnery**.    
3. Z **monitorovat clustery** kartu, vybrat cluster, ze seznamu kliknutím na název clusteru.
4. V levém podokně v části **monitorování** vyberte **protokoly** otevřete Azure Monitor protokoly stránky, které usnadňují zápis a spouštění dotazů Azure Log Analytics.
5. Na **protokoly** klikněte na **+ nové pravidlo upozornění**.
6. V části **podmínku** části, klikněte na předem definované vlastní protokol podmínku **pokaždé, když se vlastní prohledávání protokolu je <logic undefined>** . **Vlastní protokol hledání** typ signálu se vybere automaticky pro nás, protože jsme spustili, vytváří se pravidlo upozornění přímo na stránce protokoly Azure monitoru.  
7. Vložte jednu z [dotazy](#resource-utilization-log-search-queries) dříve **vyhledávací dotaz** pole. 

8. Nakonfigurujte upozornění podle následujících informací:

    a. V rozevíracím seznamu **Na základě** vyberte **Měření metriky**. Měření metriky vytvoří pro každý objekt dotazu upozornění s hodnotou, která překračuje zadanou prahovou hodnotu.  
    b. Pro **podmínku**vyberte **větší než** a zadejte **75** jako počáteční základní **prahová hodnota** nebo zadejte hodnotu, která splňuje vaše kritéria.  
    c. V části **aktivační událost výstrahy založené na** vyberte **po sobě jdoucí porušení** a z rozevíracího seznamu vyberte možnost **větší než** zadejte hodnotu **2**.  
    d. Při konfiguraci upozornění pro kontejner CPU nebo využití paměti, v části **agregační na** vyberte **ContainerName** z rozevíracího seznamu.  
    e. V části **Evaluated na základě** oddíl, změna **období** hodnotu na 60 minut. Toto pravidlo spustí každých pět minut a vrátí záznamy, které se vytvořily za poslední hodinu od aktuálního času. Nastavení delšího období zvyšuje potenciál latence dat a zajišťuje, aby dotaz vrátil data a aby se zabránilo falešně negativním hodnotám, kdy se výstraha nespustí. 

9. Klikněte na **Hotovo** a dokončete pravidlo upozornění.
10. Zadejte název upozornění v **název pravidla upozornění** pole. Zadejte **popis** s podrobnostmi o konkrétní výstrahu a vyberte odpovídající závažnost z možností.
11. Pokud chcete vytvořené pravidlo ihned aktivovat, potvrďte výchozí hodnotu přepínače **Po vytvoření povolit pravidlo**.
12. Na poslední krok, vyberte existující nebo vytvořte novou **skupiny akcí**, které zajišťuje, že stejné akce pocházejí pokaždé, když výstraha se aktivuje a lze použít pro každé pravidlo, které definujete. Konfigurace podle toho, jak tým IT nebo operace DevOps spravuje incidenty. 
13. K dokončení pravidla upozornění klikněte na **Vytvořit pravidlo upozornění**. Pravidlo se okamžitě spustí.

## <a name="next-steps"></a>Další postup

* Podívejte se na některé z [protokolu Příklady dotazů](container-insights-analyze.md#search-logs-to-analyze-data) Další informace o předdefinované dotazy nebo příklady vyhodnotit a použít nebo přizpůsobit pro jiné scénáře výstrah. 
* Chcete-li pokračovat v učení, jak používat Azure Monitor a monitorovat další aspekty vašeho clusteru AKS, přečtěte si téma [zobrazení Azure Kubernetes Service health](container-insights-analyze.md)

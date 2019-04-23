---
title: Vytvoření výstrahy související s výkonem pomocí Azure monitoru pro kontejnery | Dokumentace Microsoftu
description: Tento článek popisuje, jak využít Azure Monitor pro kontejnery, jak vytvářet vlastní výstrahy na základě dotazů protokolu pro využití paměti a procesoru.
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
ms.date: 04/01/2019
ms.author: magoedte
ms.openlocfilehash: ebe2c2b488e3d71597dd24f5504a14dd7ce6671e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59791537"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>Jak nastavit výstrahy pro problémy s výkonem ve službě Azure Monitor pro kontejnery
Azure Monitor pro kontejnery sleduje výkon úloh kontejneru, které jsou nasazeny do služby Azure Container Instances nebo spravovat clustery Kubernetes, které jsou hostované ve službě Azure Kubernetes Service (AKS).

Tento článek popisuje, jak povolit výstrahy v těchto situacích:

* Pokud překročí definované prahové hodnoty využití procesoru nebo paměti na uzlech clusteru
* Pokud využití procesoru nebo paměti na každý kontejner v rámci kontroleru překročí definované prahové hodnoty, než limit, který je nastaven na odpovídající prostředek
* *Nepřipraveno* vrátí stav uzlu
*  *Se nezdařilo*, *čekající*, *neznámý*, *systémem*, nebo *Succeeded* počítá pod fáze

Výstrahy vysoké využití procesoru nebo využití paměti na uzlech clusteru, použijte dotazy, které jsou k dispozici k vytvoření upozornění na metriku nebo oznámení na základě měření metriky. Upozornění metrik, mají nižší latenci než upozornění protokolů. Ale výstrahy protokolu poskytují dotazování rozšířené i sofistikovanější větší. Upozornění dotazy porovnat hodnoty datetime až po současnost pomocí protokolů *nyní* operátor a přejít zpět jednu hodinu. (Monitorování azure pro kontejnery ukládá všechna data ve formátu koordinovaného univerzálního času (UTC).)

Pokud nejste obeznámeni s výstrahami monitorování Azure, přečtěte si téma [přehled výstrah v Microsoft Azure](../platform/alerts-overview.md) před zahájením. Další informace o výstrahách, které používají protokol dotazů najdete v tématu [upozornění protokolů ve službě Azure Monitor](../platform/alerts-unified-log.md). Další informace o upozorněních metrik najdete v tématu [upozornění metrik ve službě Azure Monitor](../platform/alerts-metric-overview.md).

## <a name="resource-utilization-log-search-queries"></a>Vyhledávací dotazy protokolů využití prostředků
Dotazy v této části podporují jednotlivé výstrahy scénáře. Máte použít v kroku 7 [vytvoření upozornění](#create-an-alert-rule) části tohoto článku.

Následující dotaz vypočítá průměrné využití procesoru jako průměr využití výkonu procesoru členskými uzly každou minutu.  

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

Následující dotaz vypočítá průměrné využití paměti v aplikaci jako průměr využití paměti členskými uzly každou minutu.

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
>Následující dotazy použijte zástupné hodnoty \<si název clusteru > a \<svůj název řadiče > představující clusteru a kontroleru. Když nastavíte upozornění, nahradí je hodnotami specifické pro vaše prostředí.

Následující dotaz vypočítá průměrné využití procesoru u všech kontejnerů v kontroleru jako průměr využití CPU každou instanci kontejneru v kontroleru každou minutu. Měření je procento z limitu nastaveného pro kontejner.

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

Následující dotaz vypočítá průměrné využití paměti v aplikaci všechny kontejnery v kontroleru jako průměr využití paměti v aplikaci každou instanci kontejneru v kontroleru každou minutu. Měření je procento z limitu nastaveného pro kontejner.

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

Následující dotaz vrátí všechny uzly a výčty, které jsou ve stavu *připravené* a *NotReady*.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
Následující dotaz vrací hodnotu, kterou pod fáze se počítá podle všech fázích: *Se nezdařilo*, *čekající*, *neznámý*, *systémem*, nebo *úspěšné*.  

```kusto
let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let clusterName = '<your-cluster-name>';
    KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                 by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>K upozornění na určité pod fází, jako například *čekající*, *neúspěšné*, nebo *neznámý*, upravit poslední řádek dotazu. Například k výstraze na *úspěšně* použít: <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

## <a name="create-an-alert-rule"></a>Vytvoření pravidla upozornění
Postupujte podle těchto kroků k vytvoření upozornění protokolu ve službě Azure Monitor můžete použít jeden z pravidel vyhledávání protokolu, které bylo k dispozici dříve.  

>[!NOTE]
>Následující postup k vytvoření pravidla upozornění pro využití prostředků kontejneru je potřeba přepnout na nový protokol výstrah rozhraní API, jak je popsáno v [předvoleb přepněte rozhraní API pro výstrahy protokolu](../platform/alerts-log-api-switch.md).
>

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **monitorování** z podokna na levé straně. V části **Insights**vyberte **kontejnery**.
3. Na **monitorovat clustery** kartu, vybrat cluster, ze seznamu.
4. V podokně na levé straně v části **monitorování**vyberte **protokoly** otevřete stránku protokoly Azure monitoru. Pomocí této stránky pro zápis a spouštění dotazů Azure Log Analytics.
5. Na **protokoly** stránce **+ nové pravidlo upozornění**.
6. V **podmínku** vyberte **pokaždé, když se vlastní prohledávání protokolu je \<logika se nedefinovala >** předem definované podmínky vlastní protokol. **Vlastní protokol hledání** typ signálu je automaticky vybrána, protože vytváříme pravidlo upozornění přímo na stránce protokoly Azure monitoru.  
7. Vložte jednu z [dotazy](#resource-utilization-log-search-queries) dříve **vyhledávací dotaz** pole.
8. Konfigurace upozornění následujícím způsobem:

    1. V rozevíracím seznamu **Na základě** vyberte **Měření metriky**. Metriky měření vytvoří upozornění pro každý objekt v dotazu, jehož hodnota je vyšší než naše zadanou prahovou hodnotu.
    1. Pro **podmínku**vyberte **větší než**a zadejte **75** jako počáteční základní **prahová hodnota**. Nebo zadejte jinou hodnotu, která splňuje vaše kritéria.
    1. V **aktivační událost výstrahy založené na** vyberte **po sobě jdoucí porušení**. V rozevíracím seznamu vyberte **větší než**a zadejte **2**.
    1. Konfigurace upozornění pro kontejner CPU nebo využití paměti, v části **agregační na**vyberte **ContainerName**. 
    1. V **Evaluated na základě** nastavte **období** hodnota, která se **60 minut**. Toto pravidlo spustí každých 5 minut a vrátí záznamy, které se vytvořily za poslední hodinu od aktuálního času. Nastavení doby široké okno účtům pro potenciální data latence. Také zajistí, že dotaz vrací dat. vyhnete se tak falešně negativní, v němž výstraha nikdy aktivuje.

9. Vyberte **provádí** dokončete pravidlo upozornění.
10. Zadejte název do pole **název pravidla upozornění** pole. Zadejte **popis** , která poskytuje podrobnosti o výstraze. A vyberte úroveň závažnosti odpovídající možnosti.
11. Okamžitě aktivovat pravidlo upozornění, přijměte výchozí hodnotu pro **Povolit pravidlo po vytvoření**.
12. Vyberte existující **skupiny akcí** nebo vytvořte novou skupinu. Tento krok zajistí, že stejné akce pocházejí pokaždé, když se aktivuje upozornění. Konfigurace podle toho, jak tým IT nebo DevOps provozní tým spravuje incidenty.
13. Vyberte **vytvořit pravidlo upozornění** dokončete pravidlo upozornění. Pravidlo se okamžitě spustí.

## <a name="next-steps"></a>Další postup

* Zobrazení [protokolu Příklady dotazů](container-insights-analyze.md#search-logs-to-analyze-data) Další informace o předdefinovaných dotazů a příklady k vyhodnocování nebo přizpůsobit pro jiné scénáře výstrah.
* Další informace o Azure Monitor a monitorovat další aspekty vašeho clusteru AKS najdete v tématu [zobrazení Azure Kubernetes Service health](container-insights-analyze.md).

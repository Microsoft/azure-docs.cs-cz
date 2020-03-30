---
title: Vytváření výstrah výkonu pro Azure Monitor pro kontejnery | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak vytvořit vlastní výstrahy založené na dotazech protokolu pro využití paměti a procesoru z Azure Monitor pro kontejnery.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 5d73f4399d10683597fb2a2e8a3a2ab4ba0d1165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75730921"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>Jak nastavit výstrahy pro problémy s výkonem ve službě Azure Monitor pro kontejnery

Azure Monitor pro kontejnery monitoruje výkon úloh kontejnerů, které se nasazují do instancí kontejnerů Azure nebo do spravovaných clusterů Kubernetes, které jsou hostované ve službě Azure Kubernetes Service (AKS).

Tento článek popisuje, jak povolit výstrahy pro následující situace:

- Když využití procesoru nebo paměti v uzlech clusteru překročí prahovou hodnotu
- Když využití procesoru nebo paměti na libovolném kontejneru v rámci řadiče překročí prahovou hodnotu ve srovnání s limitem nastaveným na odpovídající prostředek.
- Počet stavových uzlů *NotReady*
- *Počet neúspěšných*, *čekajících*, *neznámých*, *spuštěných*nebo *úspěšných* počtů podových fází
- Pokud volné místo na disku v uzlech clusteru překročí prahovou hodnotu 

Chcete-li upozornit na vysoké využití procesoru nebo paměti nebo nedostatek volného místa na disku v uzlech clusteru, použijte dotazy, které jsou k dispozici k vytvoření upozornění metriky nebo upozornění na měření metriky. Upozornění metriky mají nižší latenci než výstrahy protokolu. Ale výstrahy protokolu poskytují rozšířené dotazování a větší propracovanost. Dotazy výstrah protokolu porovnat datetime k současnosti pomocí *nyní* operátor a přecvač zpět jednu hodinu. (Azure Monitor pro kontejnery ukládá všechna data ve formátu UTC (Coordinated Universal Time).)

Pokud nejste obeznámeni s výstrahami Azure Monitor, přečtěte si [přehled výstrah v Microsoft Azure,](../platform/alerts-overview.md) než začnete. Další informace o výstrahách, které používají dotazy protokolu, najdete [v tématu Protokolvýstrahy v Azure Monitor](../platform/alerts-unified-log.md). Další informace o upozorněních na metriky najdete [v tématu Upozornění metrik y v Azure Monitoru](../platform/alerts-metric-overview.md).

## <a name="resource-utilization-log-search-queries"></a>Vyhledávací dotazy protokolu využití prostředků

Dotazy v této části podporují každý scénář výstrahy. Používají se v kroku 7 části [vytvořit upozornění](#create-an-alert-rule) tohoto článku.

Následující dotaz vypočítá průměrné využití procesoru jako průměr využití procesoru členských uzlů každou minutu.  

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

Následující dotaz vypočítá průměrné využití paměti jako průměr využití paměti členských uzlů každou minutu.

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
>Následující dotazy používají zástupné \<hodnoty vašeho názvu clusteru> a \<název řadiče> představují váš cluster a řadič. Při nastavování výstrah je nahraďte hodnotami specifickými pro vaše prostředí.

Následující dotaz vypočítá průměrné využití procesoru všech kontejnerů v řadiči jako průměr využití procesoru každé instance kontejneru v řadiči každou minutu. Měření je procento limitu nastaveného pro kontejner.

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

Následující dotaz vypočítá průměrné využití paměti všech kontejnerů v řadiči jako průměr využití paměti každé instance kontejneru v řadiči každou minutu. Měření je procento limitu nastaveného pro kontejner.

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

Následující dotaz vrátí všechny uzly a počty, které mají stav *Ready* a *NotReady*.

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
Následující dotaz vrátí počty fází podu na základě všech fází: *Se nezdařilo*, *Čeká na vyřízení*, *Neznámý*, *Spuštěno*nebo *Úspěšné*.  

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
>Chcete-li upozornit na určité fáze podu, například *Čekající*, *Neúspěšné*nebo *Neznámé*, upravte poslední řádek dotazu. Chcete-li například upozornit na *failedcount* použití: <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

Následující dotaz vrátí disky uzlů clusteru, které přesahují 90 % volného místa. Chcete-li získat ID clusteru, nejprve spusťte následující dotaz a zkopírujte hodnotu z vlastnosti: `ClusterId`

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'container.azm.ms/disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```

## <a name="create-an-alert-rule"></a>Vytvoření pravidla upozornění

Podle těchto kroků vytvořte výstrahu protokolu v Azure Monitorpomocí jednoho z pravidel hledání protokolu, která byla poskytnuta dříve. Pokud chcete vytvořit pomocí šablony ARM, přečtěte [si tématu Ukázkový protokol vytváření výstrah pomocí šablony prostředků Azure](../platform/alerts-log.md#sample-log-alert-creation-using-azure-resource-template).

>[!NOTE]
>Následující postup pro vytvoření pravidla výstrahy pro využití prostředků kontejneru vyžaduje, abyste přepnuli na nové rozhraní API upozornění protokolu, jak je popsáno v [předvolbách Rozhraní API přepínače pro výstrahy protokolu](../platform/alerts-log-api-switch.md).
>

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Na webu Azure Portal vyhledejte a vyberte **pracovní prostory Log Analytics**.
3. V seznamu pracovních prostorů Log Analytics vyberte pracovní prostor podporující Azure Monitor pro kontejnery. 
4. V podokně na levé straně vyberte **Protokoly,** chcete-li otevřít stránku protokolů Azure Monitoru. Tato stránka slouží k zápisu a spuštění dotazů Azure Log Analytics.
5. Na stránce **Protokoly** vložte jeden z [dotazů uvedených](#resource-utilization-log-search-queries) dříve do pole **Vyhledávací dotaz** a pak vyberte **Spustit,** abyste ověřili výsledky. Pokud tento krok neprovedete, možnost **+Nová výstraha** není k dispozici pro výběr.
6. Chcete-li vytvořit výstrahu protokolu, vyberte **možnost +Nová výstraha.**
7. V části **Podmínka** vyberte **vždy, když \<je hledání vlastního protokolu logické,>** předdefinované vlastní podmínky protokolu. Typ **signálu vlastního hledání protokolu** je automaticky vybrán, protože vytváříme pravidlo výstrahy přímo ze stránky protokolů Azure Monitoru.  
8. Vložte jeden z [dotazů uvedených](#resource-utilization-log-search-queries) dříve do pole **Vyhledávací dotaz.**
9. Nakonfigurujte výstrahu následujícím způsobem:

    1. V rozevíracím seznamu **Na základě** vyberte **metrické měření**. Měření metriky vytvoří výstrahu pro každý objekt v dotazu, který má hodnotu nad naší zadanou prahovou hodnotu.
    1. V **poli Podmínka**vyberte možnost Větší **než**a zadejte **hodnotu 75** jako počáteční **prahovou hodnotu** podle směrného plánu pro výstrahy využití procesoru a paměti. Pro upozornění na nedostatek místa na disku zadejte **hodnotu 90**. Nebo zadejte jinou hodnotu, která splňuje vaše kritéria.
    1. V části **Trigger Alert Based Based On** vyberte Po sobě jdoucí **porušení**. V rozevíracím seznamu vyberte **možnost Větší než**a zadejte **hodnotu 2**.
    1. Chcete-li nakonfigurovat výstrahu pro využití procesoru kontejneru nebo paměti, vyberte v části **Agregace na** **položku ContainerName**. Chcete-li konfigurovat výstrahu nízkého disku uzlu clusteru, vyberte **možnost ClusterId**.
    1. V části **Vyhodnocení na základě** nastavte hodnotu **Období** na **60 minut**. Pravidlo bude spuštěno každých 5 minut a vrátí záznamy, které byly vytvořeny během poslední hodiny od aktuálního času. Nastavení časového období na široké okno účty pro potenciální latenci dat. Také zajišťuje, že dotaz vrátí data, aby se zabránilo falešně negativní, ve kterém výstraha nikdy požáry.

10. Chcete-li vyplnit pravidlo **výstrahy,** vyberte Hotovo.
11. Do pole **Název pravidla výstrahy** zadejte název. Zadejte **popis,** který obsahuje podrobnosti o výstraze. A vyberte odpovídající úroveň závažnosti z uvedených možností.
12. Chcete-li pravidlo výstrahy okamžitě aktivovat, přijměte při vytváření výchozí hodnotu pravidla **Povolit**.
13. Vyberte existující **skupinu akcí** nebo vytvořte novou skupinu. Tento krok zajišťuje, že stejné akce jsou prováděny při každém spuštění výstrahy. Nakonfigurujte na základě toho, jak váš provozní tým IT nebo DevOps spravuje incidenty.
14. Chcete-li pravidlo výstrahy dokončit, vyberte **možnost Vytvořit pravidlo výstrahy.** Pravidlo se okamžitě spustí.

## <a name="next-steps"></a>Další kroky

- Chcete-li zobrazit předdefinované dotazy a příklady pro vyhodnocení nebo přizpůsobení pro výstrahy, vizualizaci nebo analýzu clusterů, zobrazte [příklady dotazů](container-insights-log-search.md#search-logs-to-analyze-data) protokolu.
- Další informace o Azure Monitoru a o tom, jak sledovat další aspekty vašeho clusteru Kubernetes, najdete [v tématu Zobrazení výkonu clusteru Kubernetes](container-insights-analyze.md) a [Zobrazení stavu clusteru Kubernetes](container-insights-health.md).

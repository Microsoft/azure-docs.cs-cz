---
title: Protokolování výstrah z Azure Monitor pro kontejnery | Microsoft Docs
description: Tento článek popisuje, jak vytvořit vlastní výstrahy protokolu pro paměť a využití procesoru z Azure Monitor pro kontejnery.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: e9b0e01ca4c0ccb24d0d1b04a4d17ec06db253b6
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966247"
---
# <a name="how-to-create-log-alerts-from-azure-monitor-for-containers"></a>Jak vytvořit výstrahy protokolu z Azure Monitor pro kontejnery

Azure Monitor pro kontejnery monitorují výkon úloh kontejneru, které jsou nasazeny na spravované nebo samostatné clustery Kubernetes. V tomto článku se dozvíte, jak vytvořit výstrahy založené na protokolech v následujících situacích s clustery AKS:

- Pokud využití procesoru nebo paměti na uzlech clusteru překročí prahovou hodnotu
- Pokud využití procesoru nebo paměti na jakémkoli kontejneru v rámci kontroleru překročí prahovou hodnotu v porovnání s limitem nastaveným na odpovídajícím prostředku
- Počet *uzlů stavu pro* stav
- Počet *neúspěšných*, *nevyřízených*, *neznámých*, *spuštěných* nebo *úspěšných* fází pod sebou
- Pokud volné místo na disku na uzlech clusteru překročí prahovou hodnotu

Pokud chcete upozornit na vysoké využití procesoru nebo paměti nebo je málo volného místa na disku v uzlech clusteru, použijte dotazy, které jsou k dispozici k vytvoření výstrahy metriky nebo upozornění na měření metriky. I když výstrahy metrik mají nižší latenci než výstrahy protokolu, výstrahy protokolu poskytují pokročilé dotazy a větší sofistikovanější. Dotazy na výstrahy protokolu porovnávají hodnotu DateTime s použitím operátoru *Now* a vrátí jednu hodinu. (Azure Monitor pro kontejnery ukládají všechna data ve formátu koordinovaného světového času (UTC).)

Pokud nejste obeznámeni s výstrahami Azure Monitor, přečtěte si téma [Přehled výstrah v Microsoft Azure](../platform/alerts-overview.md) před tím, než začnete. Další informace o výstrahách, které používají dotazy protokolu, najdete [v tématu protokolování výstrah v Azure monitor](../platform/alerts-unified-log.md). Další informace o upozorněních metrik najdete [v tématu výstrahy metrik v Azure monitor](../platform/alerts-metric-overview.md).

## <a name="resource-utilization-log-search-queries"></a>Dotazy na hledání protokolu využití prostředků

Dotazy v této části podporují jednotlivé scénáře upozorňování. Používají se v kroku 7 části [Vytvoření výstrahy](#create-an-alert-rule) tohoto článku.

Následující dotaz vypočítá průměrné využití procesoru jako průměr využití procesoru členskými uzly každou minutu.  

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
>Následující dotazy používají zástupné hodnoty \<your-cluster-name> a \<your-controller-name> reprezentují svůj cluster a kontroler. Nahraďte je hodnotami specifickými pro vaše prostředí při nastavování výstrah.

Následující dotaz vypočítá průměrné využití procesoru u všech kontejnerů v řadiči jako průměrnou hodnotu využití procesoru každé instance kontejneru v řadiči v každé minutě. Měření je procento limitu nastaveného pro kontejner.

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

Následující dotaz vypočítá průměrné využití paměti u všech kontejnerů v řadiči jako průměr využití paměti každé instance kontejneru v kontroleru každou minutu. Měření je procento limitu nastaveného pro kontejner.

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

Následující dotaz vrátí všechny uzly a počty, které mají stav *připraveno* a nepříliš *stupňů*.

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
Následující dotaz vrátí hodnotu pod počty fází na základě všech fází: *selhání*, *čeká*, *Neznámý*, *spuštěný* nebo *úspěšný*.  

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
        | summarize PodStatus=any(PodStatus) by TimeGenerated, PodUid, ClusterId
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
>Chcete-li upozornit na některé fáze pod, jako je například *nevyřízená*, *neúspěšná* nebo *neznámá*, upravte poslední řádek dotazu. Například pro upozornění na *FailedCount* použití: <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

Následující dotaz vrátí disky uzlů clusteru, které překračují 90% volného místa. Chcete-li získat ID clusteru, nejprve spusťte následující dotaz a zkopírujte hodnotu z `ClusterId` vlastnosti:

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

Tato část vás provede vytvořením pravidla výstrahy měření metriky pomocí údajů o výkonu z Azure Monitor pro kontejnery. Tento základní postup můžete použít u nejrůznějších dotazů protokolu pro upozornění na různé čítače výkonu. Použijte jeden z vyhledávacích dotazů protokolu, které jsou k dispozici dříve, a začněte s. Informace o vytvoření pomocí šablony ARM najdete v tématu [ukázky vytvoření upozornění protokolu pomocí šablony prostředků Azure](../platform/alerts-log-create-templates.md).

>[!NOTE]
>Následující postup vytvoření pravidla výstrahy pro využití prostředků kontejneru vyžaduje, abyste přešli na nové rozhraní API upozornění protokolu, jak je popsáno v tématu [předvoleb rozhraní API pro protokolování výstrah](../platform/alerts-log-api-switch.md).
>

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. V Azure Portal vyhledejte a vyberte **Log Analytics pracovní prostory**.
3. V seznamu pracovních prostorů Log Analytics vyberte pracovní prostor podporující Azure Monitor pro kontejnery. 
4. V podokně na levé straně vyberte **protokoly** a otevřete stránku Azure monitor protokoly. Tato stránka slouží k zápisu a provádění dotazů protokolu Azure.
5. Na stránce **protokoly** vložte jeden z [dotazů](#resource-utilization-log-search-queries) , které jste zadali dříve do pole **vyhledávací dotaz** , a pak výběrem **příkazu Spustit** Ověřte výsledky. Pokud tento krok neprovedete, možnost **+ nová výstraha** není dostupná k výběru.
6. Vyberte **+ nová výstraha** a vytvořte výstrahu protokolu.
7. V části **Podmínka** vyberte **vždy, když je \<logic undefined> vlastní prohledávání protokolu** předem definovaný vlastní podmínka protokolu. Vlastní typ signálu **hledání vlastního protokolu** se vybere automaticky, protože vytváříme pravidlo upozornění přímo ze stránky Azure monitor protokoly.  
8. Vložte jeden z [dotazů](#resource-utilization-log-search-queries) , které byly zadány dříve, do pole **vyhledávacího dotazu** .
9. Výstrahy nakonfigurujte následujícím způsobem:

    1. Z rozevíracího seznamu na **základě** vyberte **měření metriky**. Měření metriky vytvoří výstrahu pro každý objekt v dotazu, který má hodnotu nad naši zadanou prahovou hodnotou.
    1. Jako **podmínku** vyberte **větší než** a jako počáteční **hodnotu prahové hodnoty** počátečního směrného plánu pro výstrahy využití procesoru a paměti zadejte **75** . Pro upozornění na nedostatek místa na disku zadejte **90**. Nebo zadejte jinou hodnotu, která vyhovuje vašim kritériím.
    1. V části **Výstraha aktivační události na základě** oddílu vyberte **po sobě jdoucí porušení**. V rozevíracím seznamu vyberte **větší než** a zadejte **2**.
    1. Chcete-li nakonfigurovat výstrahu pro využití procesoru nebo paměti kontejneru, vyberte v části **agregace zapnuto** možnost **ContainerName**. Pokud chcete konfigurovat pro upozornění na nízký disk uzlu clusteru, vyberte **ClusterId**.
    1. V části **vyhodnocováno na základě** oddílu nastavte hodnotu **perioda** na **60 minut**. Pravidlo se spustí každých 5 minut a vrátí záznamy, které byly vytvořeny během poslední hodiny od aktuálního času. Nastavení časového období pro velké účty oken za účelem potenciální latence dat. Také zajišťuje, že dotaz vrátí data, aby se předešlo nezápornému falešně pozitivnímu upozornění, že výstraha nebude nikdy aktivována.

10. Vyberte **Hotovo** a dokončete pravidlo výstrahy.
11. Do pole **název pravidla výstrahy** zadejte název. Zadejte **Popis** , který poskytuje podrobnosti o výstraze. A vyberte příslušnou úroveň závažnosti z poskytnutých možností.
12. Chcete-li ihned aktivovat pravidlo výstrahy, přijměte výchozí hodnotu pro **pravidlo Povolit při vytváření**.
13. Vyberte existující **skupinu akcí** nebo vytvořte novou skupinu. Tento krok zajistí, aby se při každém spuštění výstrahy provedly stejné akce. Nakonfigurujte na základě toho, jak váš tým IT nebo DevOps Operations spravuje incidenty.
14. Vyberte **vytvořit pravidlo výstrahy** a dokončete pravidlo výstrahy. Pravidlo se okamžitě spustí.

## <a name="next-steps"></a>Další kroky

- Podívejte se na [příklady dotazů protokolu](container-insights-log-search.md#search-logs-to-analyze-data) , kde najdete předdefinované dotazy a příklady pro vyhodnocení nebo přizpůsobení výstrah, vizualizace a analýzy clusterů.

- Další informace o Azure Monitor a o tom, jak monitorovat další aspekty clusteru Kubernetes, najdete v tématu [zobrazení výkonu clusterů Kubernetes](container-insights-analyze.md) a [zobrazení stavu clusteru Kubernetes](./container-insights-overview.md).
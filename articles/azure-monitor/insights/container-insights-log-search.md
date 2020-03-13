---
title: Dotazování protokolů z Azure Monitor pro kontejnery | Microsoft Docs
description: Azure Monitor pro kontejnery shromažďuje metriky a data protokolů a tento článek popisuje záznamy a obsahuje vzorové dotazy.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: dcd1656673e549b583de26bca897d0055f389d0a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275357"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Postup dotazování protokolů z Azure Monitor pro kontejnery

Azure Monitor pro kontejnery shromažďují metriky výkonu, data inventáře a informace o stavu z hostitelů a kontejnerů kontejnerů a předávají je do pracovního prostoru Log Analytics v Azure Monitor. Data jsou shromažďována každé 3 minuty. Tato data jsou k dispozici pro [dotazy](../../azure-monitor/log-query/log-query-overview.md) v Azure monitor. Tato data můžete použít ve scénářích, které zahrnují plánování migrace, analýzu kapacity, zjišťování a řešení potíží s výkonem na vyžádání.

## <a name="container-records"></a>Záznamy kontejneru

Příklady záznamů, které byly shromážděny sadou monitorování Azure pro kontejnery a datové typy, které se zobrazí ve výsledcích hledání protokolů, které jsou zobrazeny v následující tabulce:

| Typ dat | Datový typ v prohledávání protokolu | Fields (Pole) |
| --- | --- | --- |
| Výkon pro hostitele a kontejnery | `Perf` | Počítače, název_objektu, CounterName &#40;% času procesoru, disku přečte MB, zapíše MB, MB využití paměti, disku sítě přijatých bajtů, síť posílat bajtů, procesor doby využití, sítě&#41;, CounterValue TimeGenerated, Cesta_k_čítači, SourceSystem |
| Kontejner inventáře | `ContainerInventory` | TimeGenerated, počítače, název kontejneru, ContainerHostname, Image, ImageTag, ContainerState, ukončovací kód, EnvironmentVar, příkaz, čas vytvoření, StartedTime, FinishedTime, SourceSystem, identifikátor ContainerID, ID obrázku |
| Protokol kontejneru | `ContainerLog` | TimeGenerated, počítač, ID bitové kopie, název kontejneru, LogEntrySource LogEntry, SourceSystem, identifikátor ContainerID |
| Inventář kontejnerových uzlů | `ContainerNodeInventory`| TimeGenerated, počítače, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventář podů v clusteru Kubernetes | `KubePodInventory` | TimeGenerated, Computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, Controller, ContainerStatus,  ContainerStatusReason, ContainerID, ContainerName, název, PodLabel, obor názvů, PodStatus, název_clusteru, PodIp, SourceSystem |
| Seznam uzlů nepatří do clusteru Kubernetes | `KubeNodeInventory` | TimeGenerated, počítače, název clusteru, ClusterId, LastTransitionTimeReady, popisky, stav, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Události Kubernetes | `KubeEvents` | TimeGenerated, počítače, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, zprávy, SourceSystem | 
| Služby v clusteru Kubernetes | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Metriky výkonu pro uzly jsou součástí clusteru Kubernetes | Perf &#124; kde ObjectName == "K8SNode" | Počítač, ObjectName, CounterName &#40;CpuAllocatableBytes, MemoryAllocatableBytes, CpuCapacityNanoCores, MemoryCapacityBytes, MemoryRssBytes, CpuUsageNanoCores, MemoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Metriky výkonu pro kontejnery jsou součástí clusteru Kubernetes | Perf &#124; kde ObjectName == "K8SContainer" | CounterName &#40; CpuRequestNanoCores, MemoryRequestBytes, CpuLimitNanoCores, MemoryWorkingSetBytes, RestartTimeEpoch, CpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Vlastní metriky |`InsightsMetrics` | Počítač, název, obor názvů, počátek, SourceSystem, značky<sup>1</sup>, TimeGenerated, Type, Va, _ResourceId | 

<sup>1</sup> vlastnost *tagss* představuje [více dimenzí](../platform/data-platform-metrics.md#multi-dimensional-metrics) pro odpovídající metriku. Další informace o metrikách shromážděných a uložených v `InsightsMetrics` tabulce a popisu vlastností záznamu naleznete v tématu [InsightsMetrics Overview](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md).

>[!NOTE]
>Podpora pro Prometheus je ve verzi Public Preview v současnosti funkce.
>

## <a name="search-logs-to-analyze-data"></a>Hledání protokolů pro analýzu dat

Protokoly Azure Monitor vám můžou pomáhat při hledání trendů, diagnostikování slabých míst, předpovědi nebo korelují dat, která vám pomůžou určit, jestli aktuální konfigurace clusteru funguje optimálně. Prohledávání protokolů předem definovaných jsou k dispozici pro vás okamžitě začít využívat nebo přizpůsobit vrátit informace způsobem, jaký požadujete.

Interaktivní analýzu dat v pracovním prostoru můžete provádět tak, že v rozevíracím seznamu **Zobrazit v analýze** vyberete možnost **Zobrazit protokoly událostí Kubernetes** nebo **Zobrazit protokoly kontejnerů** v podokně náhledu. Stránka pro **prohledávání protokolu** se zobrazí napravo od Azure Portal stránky, na které jste byli.

![Analýza dat v Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)   

Výstup protokolu kontejnerů, který se předává do vašeho pracovního prostoru, je STDOUT a STDERR. Protože Azure Monitor je monitorování Azure managed Kubernetes (AKS), Kube-system nejsou ještě dnes shromažďovány z důvodu velkého objemu generovaná data. 

### <a name="example-log-search-queries"></a>Příklad protokolu vyhledávacích dotazů

Často je užitečné k sestavování dotazů, které začínají s příkladem jedné až dvou a následnou úpravou podle svých požadavků. Které vám pomůžou vytvářet složitější dotazy, můžete experimentovat s následující ukázkové dotazy:

| Dotaz | Popis | 
|-------|-------------|
| ContainerInventory<br> &#124;Projekt počítače, jméno, obrázek, ImageTag, ContainerState, čas vytvoření, StartedTime, FinishedTime<br> &#124;vykreslit tabulku | Seznam všech informací o životním cyklu kontejneru| 
| KubeEvents_CL<br> &#124;kde not(isempty(Namespace_s))<br> &#124;Seřadit podle TimeGenerated desc<br> &#124;vykreslit tabulku | Události Kubernetes|
| ContainerImageInventory<br> &#124;summarize AggregatedValue = count() by bitové kopie, ImageTag, spuštění | Inventář imagí | 
| **Vyberte možnost zobrazení spojnicového grafu**:<br> Výkon<br> &#124;kde ObjectName == "K8SContainer" a hodnota CounterName == "cpuUsageNanoCores" &#124; shrnout AvgCPUUsageNanoCores = avg(CounterValue) podle bin (TimeGenerated, 30 min), InstanceName | Procesoru kontejneru | 
| **Vyberte možnost zobrazení spojnicového grafu**:<br> Výkon<br> &#124;kde ObjectName == "K8SContainer" a hodnota CounterName == "memoryRssBytes" &#124; shrnout AvgUsedRssMemoryBytes = avg(CounterValue) podle bin (TimeGenerated, 30 min), InstanceName | Paměti kontejneru |
| InsightsMetrics<br> &#124;kde name = = "requests_count"<br> &#124;Shrnutí: Val = any (Val) by TimeGenerated = bin (TimeGenerated, 1m)<br> &#124;Seřadit podle TimeGenerated ASC<br> &#124;Project RequestsPerMinute = Val-předchozí (Val), TimeGenerated <br> &#124;BarChart vykreslování  | Žádosti za minutu s vlastními metrikami |

## <a name="query-prometheus-metrics-data"></a>Data metrik Prometheus dotazů

V následujícím příkladu je dotaz Prometheus metriky ukazující čtení disku za sekundu na jeden disk na jeden uzel.

```
InsightsMetrics
| where Namespace == 'container.azm.ms/diskio'
| where TimeGenerated > ago(1h)
| where Name == 'reads'
| extend Tags = todynamic(Tags)
| extend HostName = tostring(Tags.hostName), Device = Tags.name
| extend NodeDisk = strcat(Device, "/", HostName)
| order by NodeDisk asc, TimeGenerated asc
| serialize
| extend PrevVal = iif(prev(NodeDisk) != NodeDisk, 0.0, prev(Val)), PrevTimeGenerated = iif(prev(NodeDisk) != NodeDisk, datetime(null), prev(TimeGenerated))
| where isnotnull(PrevTimeGenerated) and PrevTimeGenerated != TimeGenerated
| extend Rate = iif(PrevVal > Val, Val / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1), iif(PrevVal == Val, 0.0, (Val - PrevVal) / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1)))
| where isnotnull(Rate)
| project TimeGenerated, NodeDisk, Rate
| render timechart

```

Pokud chcete zobrazit Prometheus metriky, které jsou vyAzure Monitor filtrované podle oboru názvů, zadejte "Prometheus". Tady je ukázkový dotaz pro zobrazení metrik Prometheus z oboru názvů `default` Kubernetes.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

Data Prometheus lze také přímo dotazovat podle názvu.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>Konfigurace dotazů nebo chyby při vyřazení

Pokud chcete prozkoumat chyby konfigurace nebo vyřazení, vrátí následující ukázkový dotaz z tabulky `KubeMonAgentEvents` informativní události.

```
KubeMonAgentEvents | where Level != "Info" 
```

Ve výstupu se zobrazí výsledky podobné následujícímu:

![Protokolování výsledků dotazů informativních událostí z agenta](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>Další kroky

Azure Monitor pro kontejnery neobsahují předdefinovanou sadu výstrah. Přečtěte si téma [vytvoření výstrah výkonu s Azure monitor pro kontejnery](container-insights-alerts.md) , kde se dozvíte, jak vytvořit Doporučené výstrahy pro zajištění vysokého využití procesoru a paměti, aby podporovaly vaše DevOps nebo provozní procesy a postupy. 

---
title: Dotazování protokolů ze služby Container Insights | Microsoft Docs
description: Container Insights shromažďuje metriky a data protokolů a tento článek popisuje záznamy a obsahuje vzorové dotazy.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: c2b7331255e1109f27f89a84d66e25eb07a20569
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201375"
---
# <a name="how-to-query-logs-from-container-insights"></a>Dotazování protokolů z kontejneru Insights

Služby Container Insights shromažďují metriky výkonu, data inventáře a informace o stavu z hostitelů a kontejnerů kontejnerů. Data se shromažďují každé tři minuty a předávají se do pracovního prostoru Log Analytics v Azure Monitor. Tato data jsou k dispozici pro [dotazy](../logs/log-query-overview.md) v Azure monitor. Tato data můžete použít ve scénářích, které zahrnují plánování migrace, analýzu kapacity, zjišťování a řešení potíží s výkonem na vyžádání.

## <a name="container-records"></a>Záznamy kontejneru

V následující tabulce jsou uvedeny podrobnosti o záznamech shromážděných pomocí kontejneru Insights. Seznam popisů sloupců najdete v referenčních informacích k tabulkám [ContainerInventory](/azure/azure-monitor/reference/tables/containerinventory) a [ContainerLog](/azure/azure-monitor/reference/tables/containerlog) .

| Data | Zdroj dat | Datový typ | Pole |
|------|-------------|-----------|--------|
| Inventář kontejneru | Kubelet | `ContainerInventory` | TimeGenerated, Computer, Name, ContainerHostname, image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Protokol kontejneru | Docker | `ContainerLog` | TimeGenerated, počítač, ID image, název, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Inventář uzlů kontejneru | Rozhraní API pro Kube | `ContainerNodeInventory`| TimeGenerated, počítač, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventář lusků v clusteru Kubernetes | Rozhraní API pro Kube | `KubePodInventory` | TimeGenerated, Computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, Controller, ContainerStatus, ContainerStatusReason, ContainerID, ContainerName, název, PodLabel, obor názvů, PodStatus, název_clusteru, PodIp, SourceSystem |
| Část inventáře uzlů v clusteru Kubernetes | Rozhraní API pro Kube | `KubeNodeInventory` | TimeGenerated, Computer, název_clusteru, ClusterId, LastTransitionTimeReady, Labels, status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
|Inventarizace trvalých svazků v clusteru Kubernetes |Rozhraní API pro Kube |`KubePVInventory` | TimeGenerated, PVName, PVCapacityBytes, PVCName, PVCNamespace, PVStatus, PVAccessModes, PVType, PVTypeInfo, PVStorageClassName, PVCreationTimestamp, ClusterId, název_clusteru, _ResourceId, SourceSystem |
| Události Kubernetes | Rozhraní API pro Kube | `KubeEvents` | TimeGenerated, počítač, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, zpráva, SourceSystem | 
| Služby v clusteru Kubernetes | Rozhraní API pro Kube | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Metriky výkonu pro uzly součástí clusteru Kubernetes | Metriky využití se získávají z cAdvisor a omezení z rozhraní Kube API. | `Perf \| where ObjectName == "K8SNode"` | Počítač, ObjectName, CounterName &#40;cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes, memoryRssBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Metriky výkonu pro kontejnery část clusteru Kubernetes | Metriky využití se získávají z cAdvisor a omezení z rozhraní Kube API. | `Perf \| where ObjectName == "K8SContainer"` | CounterName &#40;cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Vlastní metriky ||`InsightsMetrics` | Počítač, název, obor názvů, počátek, SourceSystem, značky<sup>1</sup>, TimeGenerated, Type, Va, _ResourceId | 

<sup>1</sup> vlastnost *tagss* představuje [více dimenzí](../essentials/data-platform-metrics.md#multi-dimensional-metrics) pro odpovídající metriku. Další informace o metrikách shromážděných a uložených v `InsightsMetrics` tabulce a popisu vlastností záznamu naleznete v tématu [InsightsMetrics Overview](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md).

## <a name="search-logs-to-analyze-data"></a>Hledání v protokolech k analýze dat

Protokoly Azure Monitor vám můžou pomáhat při hledání trendů, diagnostikování slabých míst, předpovědi nebo korelují dat, která vám pomůžou určit, jestli aktuální konfigurace clusteru funguje optimálně. K dispozici jsou předem definovaná prohledávání protokolů, která vám umožní hned začít používat nebo k přizpůsobení, aby vraceli informace tak, jak chcete.

Data v pracovním prostoru můžete interaktivně analyzovat tak, že v rozevíracím seznamu **Zobrazit v analýze** vyberete možnost **Zobrazit protokoly událostí Kubernetes** nebo **Zobrazit protokoly kontejnerů** v podokně náhledu. Stránka pro **prohledávání protokolu** se zobrazí napravo od Azure Portal stránky, na které jste byli.

![Analýza dat v Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)

Výstup protokolu kontejnerů, který se předává do vašeho pracovního prostoru, je STDOUT a STDERR. Vzhledem k tomu, že Azure Monitor monitoruje Azure spravované Kubernetes (AKS), Kube – systém není dnes shromážděn z důvodu velkého objemu vygenerovaných dat. 

### <a name="example-log-search-queries"></a>Příklady dotazů na hledání protokolů

Často je užitečné vytvářet dotazy, které začínají s příkladem nebo dvěma, a pak je upravit tak, aby vyhovovaly vašim požadavkům. Pro lepší sestavování pokročilejších dotazů můžete experimentovat s následujícími ukázkovými dotazy:

### <a name="list-all-of-a-containers-lifecycle-information"></a>Vypsat všechny informace o životním cyklu kontejneru

```kusto
ContainerInventory
| project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime
| render table
```

### <a name="kubernetes-events"></a>Události Kubernetes

``` kusto
KubeEvents_CL
| where not(isempty(Namespace_s))
| sort by TimeGenerated desc
| render table
```
### <a name="image-inventory"></a>Inventář obrázků

``` kusto
ContainerImageInventory
| summarize AggregatedValue = count() by Image, ImageTag, Running
```

### <a name="container-cpu"></a>PROCESOR kontejneru

**Vybrat možnost zobrazení spojnicového grafu**

``` kusto
Perf
| where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" 
| summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName 
```

### <a name="container-memory"></a>Paměť kontejneru

**Vybrat možnost zobrazení spojnicového grafu**

```kusto
Perf
| where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes"
| summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName
```

### <a name="requests-per-minute-with-custom-metrics"></a>Žádosti za minutu s vlastními metrikami

```kusto
InsightsMetrics
| where Name == "requests_count"
| summarize Val=any(Val) by TimeGenerated=bin(TimeGenerated, 1m)
| sort by TimeGenerated asc<br> &#124; project RequestsPerMinute = Val - prev(Val), TimeGenerated
| render barchart 
```

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

Pokud chcete zobrazit Prometheus metriky, které jsou vyAzure Monitor filtrované podle oboru názvů, zadejte "Prometheus". Tady je ukázkový dotaz pro zobrazení metrik Prometheus z `default` oboru názvů Kubernetes.

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

Pokud chcete prozkoumat chyby konfigurace nebo vyřazení, vrátí následující ukázkový dotaz z tabulky informativní události `KubeMonAgentEvents` .

```
KubeMonAgentEvents | where Level != "Info" 
```

Výstup zobrazuje výsledky podobné následujícímu příkladu:

![Protokolování výsledků dotazů informativních událostí z agenta](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>Další kroky

Součástí kontejneru Insights není předdefinovaná sada výstrah. Podívejte se na téma [vytváření výstrah výkonu pomocí služby Container Insights](./container-insights-log-alerts.md) , kde se dozvíte, jak vytvořit Doporučené výstrahy pro zajištění vysokého využití procesoru a paměti, aby podporovaly vaše DevOps nebo provozní procesy a postupy.
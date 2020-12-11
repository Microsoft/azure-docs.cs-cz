---
title: Dotazování protokolů z Azure Monitor pro kontejnery | Microsoft Docs
description: Azure Monitor pro kontejnery shromažďuje metriky a data protokolů a tento článek popisuje záznamy a obsahuje vzorové dotazy.
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: 9bfa63a49da33289b8c811007f210e6546579d9d
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033557"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Postup dotazování protokolů z Azure Monitor pro kontejnery

Azure Monitor pro kontejnery shromažďují metriky výkonu, data inventáře a informace o stavu z hostitelů a kontejnerů kontejnerů. Data se shromažďují každé tři minuty a předávají se do pracovního prostoru Log Analytics v Azure Monitor. Tato data jsou k dispozici pro [dotazy](../log-query/log-query-overview.md) v Azure monitor. Tato data můžete použít ve scénářích, které zahrnují plánování migrace, analýzu kapacity, zjišťování a řešení potíží s výkonem na vyžádání.

## <a name="container-records"></a>Záznamy kontejneru

V následující tabulce jsou uvedeny podrobnosti o záznamech shromažďovaných Azure Monitor pro kontejnery. Seznam popisů sloupců najdete v referenčních informacích k tabulkám [ContainerInventory](/azure/azure-monitor/reference/tables/containerinventory) a [ContainerLog](/azure/azure-monitor/reference/tables/containerlog) .

| Data | Zdroj dat | Datový typ | Pole |
|------|-------------|-----------|--------|
| Inventář kontejneru | Kubelet | `ContainerInventory` | TimeGenerated, Computer, Name, ContainerHostname, image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Protokol kontejneru | Docker | `ContainerLog` | TimeGenerated, počítač, ID image, název, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Inventář uzlů kontejneru | Rozhraní API pro Kube | `ContainerNodeInventory`| TimeGenerated, počítač, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventář lusků v clusteru Kubernetes | Rozhraní API pro Kube | `KubePodInventory` | TimeGenerated, Computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, Controller, ContainerStatus, ContainerStatusReason, ContainerID, ContainerName, název, PodLabel, obor názvů, PodStatus, název_clusteru, PodIp, SourceSystem |
| Část inventáře uzlů v clusteru Kubernetes | Rozhraní API pro Kube | `KubeNodeInventory` | TimeGenerated, Computer, název_clusteru, ClusterId, LastTransitionTimeReady, Labels, status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Události Kubernetes | Rozhraní API pro Kube | `KubeEvents` | TimeGenerated, počítač, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, zpráva, SourceSystem | 
| Služby v clusteru Kubernetes | Rozhraní API pro Kube | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Metriky výkonu pro uzly součástí clusteru Kubernetes | Metriky využití se získávají z cAdvisor a omezení z rozhraní Kube API. | &#124; výkonu, kde ObjectName = = "K8SNode" | Počítač, ObjectName, CounterName &#40;cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes, memoryRssBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Metriky výkonu pro kontejnery část clusteru Kubernetes | Metriky využití se získávají z cAdvisor a omezení z rozhraní Kube API. | &#124; výkonu, kde ObjectName = = "K8SContainer" | CounterName &#40; cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Vlastní metriky ||`InsightsMetrics` | Počítač, název, obor názvů, počátek, SourceSystem, značky<sup>1</sup>, TimeGenerated, Type, Va, _ResourceId | 

<sup>1</sup> vlastnost *tagss* představuje [více dimenzí](../platform/data-platform-metrics.md#multi-dimensional-metrics) pro odpovídající metriku. Další informace o metrikách shromážděných a uložených v `InsightsMetrics` tabulce a popisu vlastností záznamu naleznete v tématu [InsightsMetrics Overview](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md).

## <a name="search-logs-to-analyze-data"></a>Hledání v protokolech k analýze dat

Protokoly Azure Monitor vám můžou pomáhat při hledání trendů, diagnostikování slabých míst, předpovědi nebo korelují dat, která vám pomůžou určit, jestli aktuální konfigurace clusteru funguje optimálně. K dispozici jsou předem definovaná prohledávání protokolů, která vám umožní hned začít používat nebo k přizpůsobení, aby vraceli informace tak, jak chcete.

Interaktivní analýzu dat v pracovním prostoru můžete provádět tak, že v rozevíracím seznamu **Zobrazit v analýze** vyberete možnost **Zobrazit protokoly událostí Kubernetes** nebo **Zobrazit protokoly kontejnerů** v podokně náhledu. Stránka pro **prohledávání protokolu** se zobrazí napravo od Azure Portal stránky, na které jste byli.

![Analýza dat v Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)

Výstup protokolu kontejnerů, který se předává do vašeho pracovního prostoru, je STDOUT a STDERR. Vzhledem k tomu, že Azure Monitor monitoruje Azure spravované Kubernetes (AKS), Kube – systém se dnes neshromáždí kvůli velkému objemu vygenerovaných dat. 

### <a name="example-log-search-queries"></a>Příklady dotazů na hledání protokolů

Často je užitečné vytvářet dotazy, které začínají s příkladem nebo dvěma, a pak je upravit tak, aby vyhovovaly vašim požadavkům. Pro lepší sestavování pokročilejších dotazů můžete experimentovat s následujícími ukázkovými dotazy:

| Dotaz | Popis | 
|-------|-------------|
| ContainerInventory<br> &#124; projektový počítač, název, obrázek, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; vykreslit tabulku | Vypsat všechny informace o životním cyklu kontejneru| 
| KubeEvents_CL<br> &#124;, kde ne (neprázdné (Namespace_s))<br> &#124; seřadit podle TimeGenerated DESC<br> &#124; vykreslit tabulku | Události Kubernetes|
| ContainerImageInventory<br> &#124; sumarizovat AggregatedValue = Count () podle image, ImageTag, spuštěného | Inventář obrázků | 
| **Vyberte možnost zobrazení spojnicového grafu**:<br> Výkon<br> &#124; kde ObjectName = = "K8SContainer" and CounterName = = "cpuUsageNanoCores" &#124; sumarizace AvgCPUUsageNanoCores = prům (CounterValue) by bin (TimeGenerated, až min), InstanceName | PROCESOR kontejneru | 
| **Vyberte možnost zobrazení spojnicového grafu**:<br> Výkon<br> &#124; kde ObjectName = = "K8SContainer" and CounterName = = "memoryRssBytes" &#124; sumarizace AvgUsedRssMemoryBytes = prům (CounterValue) by bin (TimeGenerated, až min), InstanceName | Paměť kontejneru |
| InsightsMetrics<br> &#124;, kde name = = "requests_count"<br> &#124; shrnující hodnoty Val = any (Val) by TimeGenerated = bin (TimeGenerated, 1m)<br> &#124; seřadit podle TimeGenerated ASC<br> &#124; projektu RequestsPerMinute = Val-předchozí (Val), TimeGenerated <br> &#124; BarChart vykreslování  | Žádosti za minutu s vlastními metrikami |

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

Azure Monitor pro kontejnery neobsahují předdefinovanou sadu výstrah. Přečtěte si téma [vytvoření výstrah výkonu s Azure monitor pro kontejnery](./container-insights-log-alerts.md) , kde se dozvíte, jak vytvořit Doporučené výstrahy pro zajištění vysokého využití procesoru a paměti, aby podporovaly vaše DevOps nebo provozní procesy a postupy.

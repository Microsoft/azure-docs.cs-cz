---
title: Dotazování protokolů z Azure Monitor pro kontejnery | Microsoft Docs
description: Azure Monitor pro kontejnery shromažďuje metriky a data protokolů a tento článek popisuje záznamy a obsahuje vzorové dotazy.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/12/2019
ms.openlocfilehash: c3a034776b32db57f70ddee960c1cd5fc96b170b
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555409"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Postup dotazování protokolů z Azure Monitor pro kontejnery

Azure Monitor pro kontejnery shromažďují metriky výkonu, data inventáře a informace o stavu z hostitelů a kontejnerů kontejnerů a předávají je do pracovního prostoru Log Analytics v Azure Monitor. Data se shromažďují každé tři minuty. Tato data jsou k dispozici pro [dotazy](../../azure-monitor/log-query/log-query-overview.md) v Azure monitor. Tato data můžete použít ve scénářích, které zahrnují plánování migrace, analýzu kapacity, zjišťování a řešení potíží s výkonem na vyžádání.

## <a name="container-records"></a>Záznamy kontejneru

Příklady záznamů, které jsou shromažďovány Azure Monitor pro kontejnery a typy dat, které se zobrazují ve výsledcích prohledávání protokolu, jsou uvedeny v následující tabulce:

| Data type | Datový typ v hledání v protokolu | Fields (Pole) |
| --- | --- | --- |
| Výkon pro hostitele a kontejnery | `Perf` | Počítač, ObjectName, CounterName &#40;% času procesoru, čtení z disku MB, zápisy na disk MB, využití paměti MB, počet přijatých bajtů sítě, počet bajtů pro odesílání v&#41;síti, využití procesoru sec, síť, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Inventář kontejneru | `ContainerInventory` | TimeGenerated, počítač, název kontejneru, ContainerHostname, image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Protokol kontejneru | `ContainerLog` | TimeGenerated, počítač, ID image, název kontejneru, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Inventář uzlů kontejneru | `ContainerNodeInventory`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventář lusků v clusteru Kubernetes | `KubePodInventory` | TimeGenerated, Computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, Controller, ContainerStatus,  ContainerStatusReason, ContainerID, ContainerName, název, PodLabel, obor názvů, PodStatus, název_clusteru, PodIp, SourceSystem |
| Část inventáře uzlů v clusteru Kubernetes | `KubeNodeInventory` | TimeGenerated, Computer, název_clusteru, ClusterId, LastTransitionTimeReady, Labels, status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Události Kubernetes | `KubeEvents` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, zpráva, SourceSystem | 
| Služby v clusteru Kubernetes | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Metriky výkonu pro uzly součástí clusteru Kubernetes | Výkon &#124; , kde ObjectName = = "K8SNode" | Počítač, ObjectName, CounterName &#40;CpuAllocatableBytes, MemoryAllocatableBytes, CpuCapacityNanoCores, MemoryCapacityBytes, MemoryRssBytes, CpuUsageNanoCores, MemoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Metriky výkonu pro kontejnery část clusteru Kubernetes | Výkon &#124; , kde ObjectName = = "K8SContainer" | CounterName &#40; CpuRequestNanoCores, MemoryRequestBytes, CpuLimitNanoCores, MemoryWorkingSetBytes, RestartTimeEpoch, CpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Vlastní metriky |`InsightsMetrics` | Počítač, název, obor názvů, počátek, SourceSystem, značky<sup>1</sup>, TimeGenerated, Type, VA, _ResourceId | 

<sup>1</sup> vlastnost *tagss* představuje [více dimenzí](../platform/data-platform-metrics.md#multi-dimensional-metrics) pro odpovídající metriku. Další informace o metrikách shromážděných a uložených v `InsightsMetrics` tabulce a popisu vlastností záznamu naleznete v tématu [InsightsMetrics Overview](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md).

>[!NOTE]
>Podpora pro Prometheus je ve verzi Public Preview v současnosti funkce.
>

## <a name="search-logs-to-analyze-data"></a>Hledání v protokolech k analýze dat

Protokoly Azure Monitor vám můžou pomáhat při hledání trendů, diagnostikování slabých míst, předpovědi nebo korelují dat, která vám pomůžou určit, jestli aktuální konfigurace clusteru funguje optimálně. K dispozici jsou předem definovaná prohledávání protokolů, která vám umožní hned začít používat nebo k přizpůsobení, aby vraceli informace tak, jak chcete.

Můžete provést interaktivní analýzu dat v pracovním prostoru výběrem možnosti **Zobrazit protokoly událostí Kubernetes** nebo **Zobrazit protokoly kontejnerů** v podokně náhledu. Stránka pro **prohledávání protokolu** se zobrazí napravo od Azure Portal stránky, na které jste byli.

![Analýza dat v Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)   

Výstup protokolu kontejnerů, který se předává do vašeho pracovního prostoru, je STDOUT a STDERR. Vzhledem k tomu, že Azure Monitor monitoruje Azure spravované Kubernetes (AKS), Kube – systém se dnes neshromáždí kvůli velkému objemu vygenerovaných dat. 

### <a name="example-log-search-queries"></a>Příklady dotazů na hledání protokolů

Často je užitečné vytvářet dotazy, které začínají s příkladem nebo dvěma, a pak je upravit tak, aby vyhovovaly vašim požadavkům. Pro lepší sestavování pokročilejších dotazů můžete experimentovat s následujícími ukázkovými dotazy:

| Dotaz | Popis | 
|-------|-------------|
| ContainerInventory<br> &#124;Projektový počítač, název, obrázek, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124;vykreslit tabulku | Vypsat všechny informace o životním cyklu kontejneru| 
| KubeEvents_CL<br> &#124;kde not (neprázdné (Namespace_s))<br> &#124;Seřadit podle TimeGenerated DESC<br> &#124;vykreslit tabulku | Události Kubernetes|
| ContainerImageInventory<br> &#124;shrnout AggregatedValue = Count () podle image, ImageTag, spuštěného | Inventář obrázků | 
| **Vyberte možnost zobrazení spojnicového grafu**:<br> Výkon<br> &#124;kde ObjectName = = "K8SContainer" and CounterName = = "cpuUsageNanoCores" &#124; sumarizace AvgCPUUsageNanoCores = prům (CounterValue) by bin (TimeGenerated, až min), InstanceName | PROCESOR kontejneru | 
| **Vyberte možnost zobrazení spojnicového grafu**:<br> Výkon<br> &#124;kde ObjectName = = "K8SContainer" and CounterName = = "memoryRssBytes" &#124; sumarizace AvgUsedRssMemoryBytes = prům (CounterValue) by bin (TimeGenerated, až min), InstanceName | Paměť kontejneru |
| InsightsMetrics<br> &#124;kde name = = "requests_count"<br> &#124;Shrnutí: Val = any (Val) by TimeGenerated = bin (TimeGenerated, 1m)<br> &#124;Seřadit podle TimeGenerated ASC<br> &#124;Project RequestsPerMinute = Val-předchozí (Val), TimeGenerated <br> &#124;BarChart vykreslování  | Žádosti za minutu s vlastními metrikami |

V následujícím příkladu je dotaz Prometheus metriky. Shromažďované metriky jsou počty a aby bylo možné určit, kolik chyb se v určitém časovém období vyskytlo, musíme odečíst od počtu. Tato datová sada je rozdělená podle *partitionKey*, což znamená pro každou jedinečnou sadu *názvů*, názvu *hostitele*a *typem operace OperationType*spustíme poddotaz na této sadě, který seřadí protokoly podle *TimeGenerated*, což je proces, který umožňuje vyhledá předchozí *TimeGenerated* a počet zaznamenaný pro tento čas, aby bylo možné určit sazbu.

```
let data = InsightsMetrics 
| where Namespace contains 'prometheus' 
| where Name == 'kubelet_docker_operations' or Name == 'kubelet_docker_operations_errors'    
| extend Tags = todynamic(Tags) 
| extend OperationType = tostring(Tags['operation_type']), HostName = tostring(Tags.hostName) 
| extend partitionKey = strcat(HostName, '/' , Name, '/', OperationType) 
| partition by partitionKey ( 
    order by TimeGenerated asc 
    | extend PrevVal = prev(Val, 1), PrevTimeGenerated = prev(TimeGenerated, 1) 
    | extend Rate = iif(TimeGenerated == PrevTimeGenerated, 0.0, Val - PrevVal) 
    | where isnull(Rate) == false 
) 
| project TimeGenerated, Name, HostName, OperationType, Rate; 
let operationData = data 
| where Name == 'kubelet_docker_operations' 
| project-rename OperationCount = Rate; 
let errorData = data 
| where Name == 'kubelet_docker_operations_errors' 
| project-rename ErrorCount = Rate; 
operationData 
| join kind = inner ( errorData ) on TimeGenerated, HostName, OperationType 
| project-away TimeGenerated1, Name1, HostName1, OperationType1 
| extend SuccessPercentage = iif(OperationCount == 0, 1.0, 1 - (ErrorCount / OperationCount))
```

Ve výstupu se zobrazí výsledky podobné následujícímu:

![Výsledky dotazu do protokolu pro objem příjmu dat](./media/container-insights-log-search/log-query-example-prometheus-metrics.png)

## <a name="next-steps"></a>Další kroky

Azure Monitor pro kontejnery neobsahují předdefinovanou sadu výstrah. Přečtěte si téma [vytvoření výstrah výkonu s Azure monitor pro kontejnery](container-insights-alerts.md) , kde se dozvíte, jak vytvořit Doporučené výstrahy pro zajištění vysokého využití procesoru a paměti, aby podporovaly vaše DevOps nebo provozní procesy a postupy. 

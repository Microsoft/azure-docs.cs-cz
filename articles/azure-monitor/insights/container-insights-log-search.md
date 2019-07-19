---
title: Dotazování protokolů z Azure Monitor pro kontejnery | Microsoft Docs
description: Azure Monitor pro kontejnery shromažďuje metriky a data protokolů a tento článek popisuje záznamy a obsahuje vzorové dotazy.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: d6e65331db53be5ba13a75e6b03b271f1071716d
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67989821"
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
| Vlastní metriky |`InsightsMetrics` | Počítač, název, obor názvů, počátek, SourceSystem, značky<sup>1</sup>, TimeGenerated, Type, VA, _ResourceId | 

<sup>1</sup> vlastnost *tagss* představuje [více dimenzí](../platform/data-platform-metrics.md#multi-dimensional-metrics) pro odpovídající metriku. Další informace o metrikách shromážděných a uložených v `InsightsMetrics` tabulce a popisu vlastností záznamu naleznete v tématu [InsightsMetrics Overview](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md).

>[!NOTE]
>Podpora pro Prometheus je ve verzi Public Preview v současnosti funkce.
>

## <a name="search-logs-to-analyze-data"></a>Hledání protokolů pro analýzu dat

Protokoly Azure Monitor vám můžou pomáhat při hledání trendů, diagnostikování slabých míst, předpovědi nebo korelují dat, která vám pomůžou určit, jestli aktuální konfigurace clusteru funguje optimálně. Prohledávání protokolů předem definovaných jsou k dispozici pro vás okamžitě začít využívat nebo přizpůsobit vrátit informace způsobem, jaký požadujete.

Můžete provádět interaktivní analýzu dat v pracovním prostoru tak, že vyberete **protokoly událostí Kubernetes zobrazení** nebo **zobrazit protokoly kontejneru** možnost v podokně náhledu. **Prohledávání protokolů** pravé části stránky Azure portal, který jste byli, zobrazí se stránka.

![Analýza dat v Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)   

Výstup protokolu kontejnerů, který se předává do vašeho pracovního prostoru, je STDOUT a STDERR. Protože Azure Monitor je monitorování Azure managed Kubernetes (AKS), Kube-system nejsou ještě dnes shromažďovány z důvodu velkého objemu generovaná data. 

### <a name="example-log-search-queries"></a>Příklad protokolu vyhledávacích dotazů

Často je užitečné k sestavování dotazů, které začínají s příkladem jedné až dvou a následnou úpravou podle svých požadavků. Které vám pomůžou vytvářet složitější dotazy, můžete experimentovat s následující ukázkové dotazy:

| Dotaz | Popis | 
|-------|-------------|
| ContainerInventory<br> &#124;Projekt počítače, jméno, obrázek, ImageTag, ContainerState, čas vytvoření, StartedTime, FinishedTime<br> &#124;vykreslit tabulku | Seznam všech informací o životním cyklu kontejneru| 
| KubeEvents_CL<br> &#124;kde not(isempty(Namespace_s))<br> &#124;Seřadit podle TimeGenerated desc<br> &#124;vykreslit tabulku | Události Kubernetes|
| ContainerImageInventory<br> &#124;summarize AggregatedValue = count() by bitové kopie, ImageTag, spuštění | Inventář imagí | 
| **Vyberte možnosti spojnicový graf zobrazení**:<br> Výkonu<br> &#124;kde ObjectName == "K8SContainer" a hodnota CounterName == "cpuUsageNanoCores" &#124; shrnout AvgCPUUsageNanoCores = avg(CounterValue) podle bin (TimeGenerated, 30 min), InstanceName | Procesoru kontejneru | 
| **Vyberte možnosti spojnicový graf zobrazení**:<br> Výkonu<br> &#124;kde ObjectName == "K8SContainer" a hodnota CounterName == "memoryRssBytes" &#124; shrnout AvgUsedRssMemoryBytes = avg(CounterValue) podle bin (TimeGenerated, 30 min), InstanceName | Paměti kontejneru |

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

## <a name="next-steps"></a>Další postup

Azure Monitor pro kontejnery neobsahují předdefinovanou sadu výstrah. Přečtěte si téma [vytvoření výstrah výkonu s Azure monitor pro kontejnery](container-insights-alerts.md) , kde se dozvíte, jak vytvořit Doporučené výstrahy pro zajištění vysokého využití procesoru a paměti, aby podporovaly vaše DevOps nebo provozní procesy a postupy. 
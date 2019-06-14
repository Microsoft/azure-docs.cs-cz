---
title: Jak dotazu protokolů ze služby Azure Monitor pro kontejnery | Dokumentace Microsoftu
description: Azure Monitor pro kontejnery shromažďuje data metrik a protokolů a tento článek popisuje záznamy a obsahuje ukázkové dotazy.
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
ms.date: 04/17/2019
ms.author: magoedte
ms.openlocfilehash: 66fc55d8c3dbb8487d1e796d5f30b08a94f717f6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60494762"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Jak provádět dotazy protokolů ze služby Azure Monitor pro kontejnery
Azure Monitor pro kontejnery shromažďuje metriky výkonu, data inventáře a informace o stavu z hostitelích kontejnerů a kontejnery a předá ji do pracovního prostoru Log Analytics ve službě Azure Monitor. Data jsou shromažďována každé 3 minuty. Tato data jsou k dispozici pro [dotazu](../../azure-monitor/log-query/log-query-overview.md) ve službě Azure Monitor. Tato data můžete použít scénáře, které zahrnují plánování migrace, kapacitu analýza, zjišťování a řešení potíží s výkonem na vyžádání.

## <a name="container-records"></a>Záznamy kontejneru

Příklady záznamů, které byly shromážděny sadou monitorování Azure pro kontejnery a datové typy, které se zobrazí ve výsledcích hledání protokolů, které jsou zobrazeny v následující tabulce:

| Typ dat | Datový typ v prohledávání protokolu | Fields (Pole) |
| --- | --- | --- |
| Výkon pro hostitele a kontejnery | `Perf` | Počítače, název_objektu, CounterName &#40;% času procesoru, disku přečte MB, zapíše MB, MB využití paměti, disku sítě přijatých bajtů, síť posílat bajtů, procesor doby využití, sítě&#41;, CounterValue TimeGenerated, Cesta_k_čítači, SourceSystem |
| Kontejner inventáře | `ContainerInventory` | TimeGenerated, počítače, název kontejneru, ContainerHostname, Image, ImageTag, ContainerState, ukončovací kód, EnvironmentVar, příkaz, čas vytvoření, StartedTime, FinishedTime, SourceSystem, identifikátor ContainerID, ID obrázku |
| Protokol kontejneru | `ContainerLog` | TimeGenerated, počítač, ID bitové kopie, název kontejneru, LogEntrySource LogEntry, SourceSystem, identifikátor ContainerID |
| Inventář kontejnerových uzlů | `ContainerNodeInventory`| TimeGenerated, počítače, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventář podů v clusteru Kubernetes | `KubePodInventory` | TimeGenerated, počítače, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, parametr ControllerName, ContainerStatus,  ContainerStatusReason, identifikátor ContainerID, ContainerName, název, PodLabel, Namespace, PodStatus, název clusteru, PodIp, SourceSystem |
| Seznam uzlů nepatří do clusteru Kubernetes | `KubeNodeInventory` | TimeGenerated, počítače, název clusteru, ClusterId, LastTransitionTimeReady, popisky, stav, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Události Kubernetes | `KubeEvents` | TimeGenerated, počítače, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, zprávy, SourceSystem | 
| Služby v clusteru Kubernetes | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Metriky výkonu pro uzly jsou součástí clusteru Kubernetes | Perf &#124; kde ObjectName == "K8SNode" | Počítače, název_objektu, CounterName &#40;cpuAllocatableBytes memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes, memoryRssBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated Cesta_k_čítači, SourceSystem | 
| Metriky výkonu pro kontejnery jsou součástí clusteru Kubernetes | Perf &#124; kde ObjectName == "K8SContainer" | Hodnota counterName &#40; cpuRequestNanoCores memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, CounterValue TimeGenerated, Cesta_k_čítači, SourceSystem | 
| InsightsMetrics | Počítače, název, Namespace, původu, SourceSystem, značky, TimeGenerated, typ, hodnoty. |

## <a name="search-logs-to-analyze-data"></a>Hledání protokolů pro analýzu dat
Protokoly služby Azure Monitor můžete hledat trendy, diagnostikovat problémových míst, předpovědi nebo korelovat data, která vám může pomoct určit, zda je aktuální konfiguraci clusteru optimální výkon. Prohledávání protokolů předem definovaných jsou k dispozici pro vás okamžitě začít využívat nebo přizpůsobit vrátit informace způsobem, jaký požadujete. 

Můžete provádět interaktivní analýzu dat v pracovním prostoru tak, že vyberete **protokoly událostí Kubernetes zobrazení** nebo **zobrazit protokoly kontejneru** možnost v podokně náhledu. **Prohledávání protokolů** pravé části stránky Azure portal, který jste byli, zobrazí se stránka.

![Analýza dat v Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)   

Výstup protokoly kontejneru, který je předán do pracovního prostoru jsou STDOUT a STDERR. Protože Azure Monitor je monitorování Azure managed Kubernetes (AKS), Kube-system nejsou ještě dnes shromažďovány z důvodu velkého objemu generovaná data. 

### <a name="example-log-search-queries"></a>Příklad protokolu vyhledávacích dotazů
Často je užitečné k sestavování dotazů, které začínají s příkladem jedné až dvou a následnou úpravou podle svých požadavků. Které vám pomůžou vytvářet složitější dotazy, můžete experimentovat s následující ukázkové dotazy:

| Dotaz | Popis | 
|-------|-------------|
| ContainerInventory<br> &#124;Projekt počítače, jméno, obrázek, ImageTag, ContainerState, čas vytvoření, StartedTime, FinishedTime<br> &#124;vykreslit tabulku | Seznam všech informací o životním cyklu kontejneru| 
| KubeEvents_CL<br> &#124;kde not(isempty(Namespace_s))<br> &#124;Seřadit podle TimeGenerated desc<br> &#124;vykreslit tabulku | Události Kubernetes|
| ContainerImageInventory<br> &#124;summarize AggregatedValue = count() by bitové kopie, ImageTag, spuštění | Inventář imagí | 
| **Vyberte možnosti spojnicový graf zobrazení**:<br> Výkonu<br> &#124;kde ObjectName == "K8SContainer" a hodnota CounterName == "cpuUsageNanoCores" &#124; shrnout AvgCPUUsageNanoCores = avg(CounterValue) podle bin (TimeGenerated, 30 min), InstanceName | Procesoru kontejneru | 
| **Vyberte možnosti spojnicový graf zobrazení**:<br> Výkonu<br> &#124;kde ObjectName == "K8SContainer" a hodnota CounterName == "memoryRssBytes" &#124; shrnout AvgUsedRssMemoryBytes = avg(CounterValue) podle bin (TimeGenerated, 30 min), InstanceName | Paměti kontejneru |

## <a name="next-steps"></a>Další postup
Azure Monitor pro kontejnery nezahrnuje předdefinovanou sadu výstrah. Zkontrolujte [vytvoření výstrahy související s výkonem pomocí Azure monitoru pro kontejnery](container-insights-alerts.md) se naučíte vytvořit doporučené výstrahy pro vysoké využití procesoru a paměti pro podporu DevOps nebo provozní procesy a postupy. 
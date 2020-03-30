---
title: Jak dotazovat protokoly z Azure Monitor u kontejnerů | Dokumenty společnosti Microsoft
description: Azure Monitor pro kontejnery shromažďuje metriky a data protokolu a tento článek popisuje záznamy a obsahuje ukázkové dotazy.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: ff7cbff708b794847d8be69ca8f829e622d7c7ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333478"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Jak dotazovat protokoly z Azure Monitoru pro kontejnery

Azure Monitor pro kontejnery shromažďuje metriky výkonu, data inventáře a informace o stavu z hostitelů kontejnerů a kontejnerů a předává je do pracovního prostoru Log Analytics ve službě Azure Monitor. Údaje se shromažďují každé tři minuty. Tato data jsou dostupná pro [dotaz](../../azure-monitor/log-query/log-query-overview.md) v Azure Monitoru. Tato data můžete použít na scénáře, které zahrnují plánování migrace, analýzu kapacity, zjišťování a řešení potíží s výkonem na vyžádání.

## <a name="container-records"></a>Kontejnerové záznamy

Příklady záznamů, které jsou shromažďovány službou Azure Monitor pro kontejnery a datové typy, které se zobrazují ve výsledcích hledání protokolu, se zobrazí v následující tabulce:

| Datový typ | Datový typ ve vyhledávání protokolů | Pole |
| --- | --- | --- |
| Výkon pro hostitele a kontejnery | `Perf` | Počítač, Název_objektu, _Název_čítače &#40;%Čas procesoru, Disk ový čas, MB zápisy na disku, MB využití paměti, bajty pro příjem sítě, bajty síťového odesílání, oddělení využití procesoru, síťové&#41;, hodnota_ |
| Zásoby kontejnerů | `ContainerInventory` | TimeGenerated, Počítač, název kontejneru, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Protokol kontejneru | `ContainerLog` | TimeGenerated, Počítač, ID bitové kopie, název kontejneru, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Inventář uzlů kontejneru | `ContainerNodeInventory`| TimeGenerated, Počítač, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Soupis lusků v klastru Kubernetes | `KubePodInventory` | TimeGenerated, Počítač, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus,  ContainerStatusReason, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Soupis uzlů, které jsou součástí clusteru Kubernetes | `KubeNodeInventory` | TimeGenerated, Počítač, Název clusteru, ClusterId, LastTransitionTimeReady, Popisky, Stav, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Akce Kubernetes | `KubeEvents` | TimeGenerated, Počítač, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Zpráva, SourceSystem | 
| Služby v clusteru Kubernetes | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Metriky výkonu pro uzly, které jsou součástí clusteru Kubernetes | Perf &#124; kde ObjectName == "K8SNode" | Počítač, Název_objektu, Název_čítače &#40;cpuAllocatableBajce, memoryAllocatableBytes, cpuCapacityNanoCores, cpuCapacityNoTes, memoryCapacitySBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Metriky výkonu pro kontejnery, které jsou součástí clusteru Kubernetes | Perf &#124; kde ObjectName == "K8SContainer" | CounterName &#40; cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Vlastní metriky |`InsightsMetrics` | Počítač, Název, Obor názvů, Původ, SourceSystem, Tagy<sup>1</sup>, TimeGenerated, Typ, Va, _ResourceId | 

<sup>1</sup> *Vlastnost Tags* představuje [více dimenzí](../platform/data-platform-metrics.md#multi-dimensional-metrics) pro odpovídající metriku. Další informace o metrikách shromážděných `InsightsMetrics` a uložených v tabulce a popis vlastností záznamu naleznete v [tématu Přehledmetriky přehled](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md).

## <a name="search-logs-to-analyze-data"></a>Hledat protokoly pro analýzu dat

Protokoly monitorování Azure vám můžou pomoct hledat trendy, diagnostikovat kritická místa, předpovídat nebo korelovat data, která vám můžou pomoct určit, jestli aktuální konfigurace clusteru funguje optimálně. Předdefinované vyhledávání protokolu jsou k dispozici pro okamžité spuštění nebo přizpůsobení vrátit informace požadovaným způsobem.

Interaktivní analýzu dat v pracovním prostoru můžete provést výběrem možnosti **Zobrazit protokoly událostí Kubernetes** nebo **Zobrazit protokoly kontejnerů** v podokně náhledu z rozevíracího seznamu **Zobrazení v analýze.** Stránka **Hledání protokolu** se zobrazí vpravo od stránky portálu Azure, na které jste byli.

![Analýza dat v Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)

Výstup kontejneru protokoly, který je předán do pracovního prostoru jsou STDOUT a STDERR. Vzhledem k tomu, že Azure Monitor monitoruje Azure spravované Kubernetes (AKS), kube systém není shromažďovány dnes z důvodu velkého objemu generovaných dat. 

### <a name="example-log-search-queries"></a>Příklad vyhledávacích dotazů protokolu

Často je užitečné vytvářet dotazy, které začínají s příkladem nebo dvěma a pak je upravit tak, aby vyhovovaly vašim požadavkům. Chcete-li pomoci vytvářet pokročilejší dotazy, můžete experimentovat s následujícími ukázkovými dotazy:

| Dotaz | Popis | 
|-------|-------------|
| Kontejnerinventář<br> &#124; projektu Počítač, Název, Obrázek, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; vykreslení tabulky | Seznam všech informací o životním cyklu kontejneru| 
| KubeEvents_CL<br> &#124; kde není(isempty(Namespace_s))<br> &#124; řazení podle TimeGenerated desc<br> &#124; vykreslení tabulky | Akce Kubernetes|
| ContainerImageInventory<br> &#124; shrnout AggregatedValue = count() podle obrázku, ImageTag, Spuštěno | Inventář obrázků | 
| **Vyberte možnost zobrazení spojnicového grafu**:<br> Výkon<br> &#124; kde ObjectName == "K8SContainer" a CounterName == "cpuUsageNanoCores" &#124; shrnout AvgCPUUsageNanoCores = avg(CounterValue) podle bin(TimeGenerated, 30m), InstanceName | Kontejnerový procesor | 
| **Vyberte možnost zobrazení spojnicového grafu**:<br> Výkon<br> &#124; kde ObjectName == "K8SContainer" a CounterName == "memoryRssBytes" &#124; sumarizovat AvgUsedRssMemoryBytes = avg(CounterValue) podle bin(TimeGenerated, 30m), InstanceName | Paměť kontejneru |
| InsightsMetrics<br> &#124; kde název == "requests_count"<br> &#124; shrnout Val=any(Val) podle TimeGenerated=bin(TimeGenerated, 1m)<br> &#124; řazení podle TimeGenerated asc<br> &#124; projektu RequestsPerMinute = Val - prev(Val), TimeGenerated <br> &#124; vykreslení barchart  | Požadavky na minutu s vlastnímetriky |

## <a name="query-prometheus-metrics-data"></a>Dotaz Prometheus metriky data

Následující příklad je dotaz metrik Prometheus zobrazující čtení disků za sekundu na disk na uzel.

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

Chcete-li zobrazit metriky Prometheus seškrábnuté službou Azure Monitor filtrované podle oboru názvů, zadejte "prometheus". Zde je ukázkový dotaz pro zobrazení metrik `default` y Prometheus z oboru názvů kubernetes.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

Data společnosti Prometheus lze také přímo dotazovat podle názvu.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>Chyby konfigurace dotazu nebo škrábání

Chcete-li prozkoumat všechny chyby konfigurace nebo škrábání, `KubeMonAgentEvents` následující příklad dotazu vrátí informační události z tabulky.

```
KubeMonAgentEvents | where Level != "Info" 
```

Výstup zobrazí výsledky podobné následujícímu:

![Protokolovat výsledky informačních událostí dotazu od agenta](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>Další kroky

Azure Monitor pro kontejnery neobsahuje předdefinovanou sadu výstrah. Projděte [si vytvořit výstrahy výkonu pomocí Azure Monitor pro kontejnery se dozvíte,](container-insights-alerts.md) jak vytvořit doporučené výstrahy pro vysoké využití procesoru a paměti pro podporu devOps nebo provozní procesy a postupy. 

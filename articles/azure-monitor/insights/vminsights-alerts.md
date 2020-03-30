---
title: Výstrahy z Azure Monitoru pro virtuální počítače
description: Popisuje, jak vytvořit pravidla výstrah z dat o výkonu shromážděných Azure Monitor pro virtuální počítače.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/23/2020
ms.openlocfilehash: 987537d8497b3d8f2728941334d8328320ec6997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289599"
---
# <a name="how-to-create-alerts-from-azure-monitor-for-vms"></a>Jak vytvořit výstrahy z Azure Monitoru pro virtuální počítače
[Výstrahy v Azure Monitoru](../platform/alerts-overview.md) vás proaktivně upozorňují na zajímavá data a vzory v datech monitorování. Azure Monitor pro virtuální počítače neobsahuje předem nakonfigurovaná pravidla výstrah, ale můžete vytvořit vlastní na základě dat, která shromažďuje. Tento článek obsahuje pokyny k vytváření pravidel výstrah, včetně sady ukázkových dotazů.


## <a name="alert-rule-types"></a>Typy pravidel výstrah
Azure Monitor má [různé typy pravidel výstrah](../platform/alerts-overview.md#what-you-can-alert-on) na základě dat používaných k vytvoření výstrahy. Všechna data shromážděná službou Azure Monitor pro virtuální počítače se ukládají v protokolech azure monitoru, které podporují [výstrahy protokolu](../platform/alerts-log.md). V současné době nelze použít [upozornění metriky](../platform/alerts-log.md) s daty o výkonu shromážděných z Azure Monitor pro virtuální počítače, protože data se neshromažďují do metriky Azure Monitor. Chcete-li shromažďovat data pro upozornění na [metriky,](../platform/diagnostics-extension-overview.md) nainstalujte rozšíření diagnostiky pro virtuální počítače se systémem Windows nebo [agenta Telegraf](../platform/collect-custom-metrics-linux-telegraf.md) pro virtuální počítače s Linuxem ke shromažďování dat o výkonu do metrik.

V Azure Monitoru existují dva typy výstrah protokolu:

- [Počet výstrah výsledků](../platform/alerts-unified-log.md#number-of-results-alert-rules) vytvoří jednu výstrahu, když dotaz vrátí alespoň zadaný počet záznamů. Jsou ideální pro nečíselná data, jako jsou windows a syslog události shromážděné [agentem Log Analytics](../platform/log-analytics-agent.md) nebo pro analýzu trendů výkonu ve více počítačích.
- [Upozornění na měření metrik vytvořte](../platform/alerts-unified-log.md#metric-measurement-alert-rules) samostatnou výstrahu pro každý záznam v dotazu, který má hodnotu, která překračuje prahovou hodnotu definovanou v pravidle výstrahy. Tato pravidla výstrah jsou ideální pro údaje o výkonu shromažďované Azure Monitor pro virtuální počítače, protože mohou vytvářet individuální výstrahy pro každý počítač.


## <a name="alert-rule-walkthrough"></a>Návod k pravidlu výstrahy
Tato část vás provede vytvořením pravidla upozornění na měření metriky pomocí dat o výkonu z Azure Monitoru pro virtuální počítače. Tento základní proces můžete použít s různými dotazy protokolu k upozornění na různé čítače výkonu.

Začněte vytvořením nového pravidla výstrahy podle postupu v [části Vytvoření, zobrazení a správa výstrah protokolu pomocí programu Azure Monitor](../platform/alerts-log.md). Pro **prostředek**vyberte pracovní prostor Log Analytics, který virtuální počítače Azure Monitor používá ve vašem předplatném. Vzhledem k tomu, že cílový prostředek pro pravidla výstrah protokolu je vždy pracovní prostor Log Analytics, dotaz protokolu musí obsahovat libovolný filtr pro konkrétní virtuální počítače nebo škálovací sady virtuálních počítačů. 

Pro **podmínku** pravidla výstrahy použijte jeden z dotazů v [následující části](#sample-alert-queries) jako **vyhledávací dotaz**. Dotaz musí vrátit číselnou vlastnost nazvanou *AggregatedValue*. Měla by sumarizovat data podle počítače, takže můžete vytvořit samostatnou výstrahu pro každý virtuální počítač, který překročí prahovou hodnotu.

V **logice výstrah**vyberte **metrické měření** a zadejte **prahovou hodnotu**. V **nabídce výstrahy založené na**, určete, kolikrát musí být prahová hodnota překročena před vytvořením výstrahy. Například je vám jedno, jestli procesor jednou překročí prahovou hodnotu a pak se vrátí do normálu, ale je vám jedno, jestli i nadále překračuje prahovou hodnotu při více po sobě jdoucích měřeních.

**Vyhodnoceno na** základě části definuje, jak často je dotaz spuštěn a časové okno pro dotaz. V níže uvedeném příkladu bude dotaz spuštěn každých 15 minut a vyhodnotí hodnoty výkonu shromážděné během předchozích 15 minut.


![Pravidlo upozornění na měření metriky](media/vminsights-alerts/metric-measurement-alert.png)

## <a name="sample-alert-queries"></a>Ukázkové dotazy s výstrahami
Následující dotazy lze použít s pravidlem upozornění metriky měření pomocí dat o výkonu shromážděných Azure Monitor pro virtuální počítače. Každý shrnuje data podle počítače tak, aby výstraha byla vytvořena pro každý počítač s hodnotou, která překračuje prahovou hodnotu.

### <a name="cpu-utilization"></a>Využití procesoru

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-mb"></a>Dostupná paměť v MB

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-percentage"></a>Dostupná paměť v procentech

```kusto
InsightsMetrics 
| where Origin == "vm.azm.ms" 
| where Namespace == "Memory" and Name == "AvailableMB" 
| extend TotalMemory = toreal(todynamic(Tags)["vm.azm.ms/memorySizeMB"])
| extend AvailableMemoryPercentage = (toreal(Val) / TotalMemory) * 100.0 
| summarize AggregatedValue = avg(AvailableMemoryPercentage) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---all-disks-on-each-computer"></a>Použitý logický disk - všechny disky v každém počítači

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---individual-disks"></a>Použitý logický disk - jednotlivé disky

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, Disk
```

### <a name="logical-disk-iops"></a>Vipos logického disku

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "TransfersPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) ), Computer, _ResourceId, Disk
```

### <a name="logical-disk-data-rate"></a>Rychlost dat logického disku

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "BytesPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) , Computer, _ResourceId, Disk
```

### <a name="network-interfaces-bytes-received---all-interfaces"></a>Přijaté bajty síťových rozhraní - všechna rozhraní

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="network-interfaces-bytes-received---individual-interfaces"></a>Přijaté bajty síťových rozhraní - jednotlivá rozhraní

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="network-interfaces-bytes-sent---all-interfaces"></a>Odeslané bajty síťových rozhraní - všechna rozhraní

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="network-interfaces-bytes-sent---individual-interfaces"></a>Odeslané bajty síťových rozhraní - jednotlivá rozhraní

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="virtual-machine-scale-set"></a>Škálovací sada virtuálních počítačů
Upravte pomocí ID předplatného, skupiny prostředků a názvu škálovací sady virtuálních strojů.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/my-vm-scaleset" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="specific-virtual-machine"></a>Konkrétní virtuální počítač
Upravte pomocí ID předplatného, skupiny prostředků a názvu virtuálního počítače.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId =~ "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m)
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-subscription"></a>Využití procesoru pro všechny výpočetní prostředky v předplatném
Upravte to pomocí ID předplatného.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/")
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-resource-group"></a>Využití procesoru pro všechny výpočetní prostředky ve skupině prostředků
Upravte pomocí ID předplatného a skupiny prostředků.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/"
or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId

```

## <a name="next-steps"></a>Další kroky

- Další informace o [výstrahách v Azure Monitoru](../platform/alerts-overview.md).
- Další informace o [dotazech protokolu pomocí dat z Azure Monitor u virtuálních počítačů](vminsights-log-search.md).

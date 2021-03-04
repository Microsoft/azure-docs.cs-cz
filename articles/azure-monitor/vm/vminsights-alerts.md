---
title: Výstrahy z virtuálního počítače Insights
description: Popisuje, jak vytvořit pravidla upozornění z údajů o výkonu shromážděných službou VM Insights.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/10/2020
ms.openlocfilehash: e3b5f49d9a4ed7af40afba5b267ba0c7bb9cd73a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704051"
---
# <a name="how-to-create-alerts-from-vm-insights"></a>Jak vytvořit výstrahy z VM Insights
[Výstrahy v Azure monitor](../alerts/alerts-overview.md) proaktivně upozorňují na zajímavá data a vzory v datech monitorování. V rámci virtuálních počítačů nejsou pravidla výstrah předem nakonfigurovaná, můžete je ale vytvářet na základě dat, která shromažďuje. Tento článek obsahuje pokyny týkající se vytváření pravidel výstrah, včetně sady ukázkových dotazů.

> [!IMPORTANT]
> Výstrahy popsané v tomto článku jsou založené na dotazech protokolu z shromážděných dat z kolekce VM Insights. To se liší od výstrah vytvořených [Azure monitor pro stav hosta virtuálního počítače](vminsights-health-overview.md) , který je aktuálně ve verzi Public Preview. Vzhledem k tomu, že tato funkce je blízko obecné dostupnosti, se budou konsolidovat pokyny pro upozorňování.


## <a name="alert-rule-types"></a>Typy pravidel výstrah
Azure Monitor má [různé typy pravidel upozornění](../alerts/alerts-overview.md#what-you-can-alert-on) na základě dat použitých k vytvoření výstrahy. Všechna data shromážděná službou VM Insights jsou uložená v protokolech Azure Monitor, které podporují [výstrahy protokolu](../alerts/alerts-log.md). V současné době nemůžete používat [Upozornění na metriky](../alerts/alerts-log.md) s daty o výkonu shromážděnými z virtuálních počítačů Insights, protože data nejsou shromažďována do Azure monitor metrik. Pokud chcete shromažďovat data pro upozornění na metriky, nainstalujte [diagnostické rozšíření](../agents/diagnostics-extension-overview.md) pro virtuální počítače s Windows nebo [agenta telegraf](../essentials/collect-custom-metrics-linux-telegraf.md) pro virtuální počítače se systémem Linux, abyste mohli shromažďovat údaje o výkonu do metrik.

Existují dva typy upozornění protokolu v Azure Monitor:

- [Počet výstrah výsledků](../alerts/alerts-unified-log.md#count-of-the-results-table-rows) vytvoří jednu výstrahu, když dotaz vrátí alespoň zadaný počet záznamů. Ty jsou ideální pro nečíselná data, například pro události Windows a syslog shromažďované [agentem Log Analytics](../agents/log-analytics-agent.md) nebo pro analýzu trendů výkonu napříč více počítači.
- [Výstrahy měření metriky](../alerts/alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) vytvoří samostatnou výstrahu pro každý záznam v dotazu, který má hodnotu, která překračuje prahovou hodnotu definovanou v pravidle výstrahy. Tato pravidla výstrah jsou ideální pro data o výkonu shromážděná službou VM Insights, protože můžou vytvářet jednotlivé výstrahy pro každý počítač.


## <a name="alert-rule-walkthrough"></a>Návod pro pravidlo upozornění
Tato část vás provede vytvořením pravidla výstrahy měření metriky pomocí údajů o výkonu ze služby VM Insights. Tento základní postup můžete použít u nejrůznějších dotazů protokolu pro upozornění na různé čítače výkonu.

Začněte vytvořením nového pravidla výstrahy podle postupu v části [Vytvoření, zobrazení a správa výstrah protokolu pomocí Azure monitor](../alerts/alerts-log.md). V případě **prostředku** vyberte pracovní prostor Log Analytics, který Azure monitor virtuální počítače ve vašem předplatném používat. Vzhledem k tomu, že je cílový prostředek pro pravidla upozornění protokolu vždycky Log Analytics pracovním prostorem, dotaz protokolu musí zahrnovat jakýkoliv filtr pro konkrétní virtuální počítače nebo sady škálování virtuálních počítačů. 

Pro **podmínku** pravidla výstrahy použijte jeden z dotazů v [níže uvedené části](#sample-alert-queries) jako **vyhledávací dotaz**. Dotaz musí vracet číselnou vlastnost s názvem *AggregatedValue*. Měla by data sumarizovat podle počítačů, abyste mohli vytvořit samostatnou výstrahu pro každý virtuální počítač, který překračuje prahovou hodnotu.

V **logice výstrahy** vyberte **měření metriky** a pak zadejte **prahovou hodnotu**. V okně **Výstraha aktivační události na základě** zadejte, kolikrát musí být prahová hodnota překročena, než bude vytvořena výstraha. Například se vám pravděpodobně nemusíte starat, pokud procesor překročí prahovou hodnotu jednou a potom se vrátí na normální, ale budete mít pozor, pokud bude nadále překračovat prahovou hodnotu nad několik po sobě jdoucích měření.

**Vyhodnocený** oddíl definuje, jak často se dotaz spouští, a časový interval pro dotaz. V níže uvedeném příkladu se dotaz spustí každých 15 minut a vyhodnotí se hodnoty výkonu shromážděné během předchozích 15 minut.


![Pravidlo upozornění na měření metriky](media/vminsights-alerts/metric-measurement-alert.png)

## <a name="sample-alert-queries"></a>Ukázkové dotazy na výstrahy
Následující dotazy lze použít s pravidlem výstrahy měření metriky pomocí údajů o výkonu shromažďovaných službou VM Insights. Každý shrnuje data podle počítače tak, aby byla pro každý počítač vytvořena výstraha s hodnotou, která překračuje prahovou hodnotu.

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

### <a name="logical-disk-used---all-disks-on-each-computer"></a>Použitý logický disk – všechny disky v každém počítači

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---individual-disks"></a>Použitý logický disk – jednotlivé disky

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, Disk
```

### <a name="logical-disk-iops"></a>IOPS logického disku

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "TransfersPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) ), Computer, _ResourceId, Disk
```

### <a name="logical-disk-data-rate"></a>Rychlost přenosu dat logického disku

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "BytesPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) , Computer, _ResourceId, Disk
```

### <a name="network-interfaces-bytes-received---all-interfaces"></a>Přijatá síťová rozhraní bajtů – všechna rozhraní

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="network-interfaces-bytes-received---individual-interfaces"></a>Přijatá síťová rozhraní bajtů – jednotlivá rozhraní

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="network-interfaces-bytes-sent---all-interfaces"></a>Síťová rozhraní – odeslané bajty – všechna rozhraní

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="network-interfaces-bytes-sent---individual-interfaces"></a>Síťová rozhraní – odeslané bajty – jednotlivá rozhraní

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="virtual-machine-scale-set"></a>Škálovací sada virtuálních počítačů
Upravte pomocí ID předplatného, skupiny prostředků a názvu sady škálování virtuálního počítače.

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

### <a name="cpu-utilization-for-all-compute-resources-in-a-subscription"></a>Využití procesoru u všech výpočetních prostředků v rámci předplatného
Upravte své ID předplatného.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/")
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-resource-group"></a>Využití procesoru u všech výpočetních prostředků ve skupině prostředků
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

- Přečtěte si další informace o [výstrahách v Azure monitor](../alerts/alerts-overview.md).
- Přečtěte si další informace o [dotazech protokolu pomocí dat z Cloud Insights](vminsights-log-search.md).

---
title: Agregace v dotazech protokolu Azure Monitor| Dokumenty společnosti Microsoft
description: Popisuje funkce agregace v dotazech protokolu Azure Monitor, které nabízejí užitečné způsoby analýzy dat.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: d164c53e7e2be55f3cede389901a256ba388808d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670300"
---
# <a name="aggregations-in-azure-monitor-log-queries"></a>Agregace v dotazech protokolu Azure Monitor

> [!NOTE]
> Před dokončením této lekce byste měli dokončit [Začínáme s portálem Analytics](get-started-portal.md) a [Začínáme s dotazy.](get-started-queries.md)

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Tento článek popisuje funkce agregace v dotazech protokolu Azure Monitor, které nabízejí užitečné způsoby analýzy dat. Tyto funkce všechny `summarize` práce s operátorem, který vytváří tabulku s agregované výsledky vstupní tabulky.

## <a name="counts"></a>Počítá

### <a name="count"></a>count
Spočítejte počet řádků v sadě výsledků po použití filtrů. Následující příklad vrátí celkový počet řádků v tabulce _Perf_ z posledních 30 minut. Výsledek je vrácen ve sloupci s názvem *count_* pokud mu nepřiřadíte určitý název:


```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

Vizualizace časového diagramu může být užitečná pro zobrazení trendu v průběhu času:

```Kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

Výstup z tohoto příkladu zobrazuje spojnice trendu počtu záznamů perf v intervalech 5 minut:

![Počítat trend](media/aggregations/count-trend.png)


### <a name="dcount-dcountif"></a>dcount, dcountif
Použijte `dcount` `dcountif` a spočítejte odlišné hodnoty v určitém sloupci. Následující dotaz vyhodnocuje, kolik různých počítačů odeslalo prezenční signály za poslední hodinu:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

Chcete-li počítat pouze počítače s `dcountif`Linuxem, které odeslaly prezenční signály, použijte :

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>Vyhodnocení podskupin
Chcete-li provést počet nebo jiné agregace v podskupinách v datech, použijte `by` klíčové slovo. Chcete-li například spočítat počet různých počítačů s Linuxem, které odeslaly prezenční signály v každé zemi nebo oblasti:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry
```

|RemoteIPCountry  | distinct_computers  |
------------------|---------------------|
|Spojené státy    | 19                  |
|Kanada           | 3                   |
|Irsko          | 0                   |
|Spojené království   | 0                   |
|Nizozemsko      | 2                   |


Chcete-li analyzovat ještě menší podskupiny dat, `by` přidejte do oddílu další názvy sloupců. Můžete například počítat odlišné počítače z každé země nebo oblasti podle typu OSType:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```

## <a name="percentiles-and-variance"></a>Percentily a rozptyl
Při vyhodnocování číselných hodnot je `summarize avg(expression)`běžnou praxí jejich použití . Průměry jsou ovlivněny extrémními hodnotami, které charakterizují pouze několik případů. Chcete-li tento problém vyřešit, můžete `median` `variance`použít méně citlivé funkce, jako je například nebo .

### <a name="percentile"></a>Percentil
Chcete-li najít střední `percentile` hodnotu, použijte funkci s hodnotou k určení percentilu:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

Můžete také zadat různé percentily, abyste získali agregovaný výsledek pro každý:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

To by mohlo ukázat, že některé procesory počítačů mají podobné střední hodnoty, ale zatímco některé jsou stabilní kolem mediánu, jiné počítače vykázaly mnohem nižší a vyšší hodnoty PROCESORU, což znamená, že došlo k špičkám.

### <a name="variance"></a>Rozptyl
Chcete-li přímo vyhodnotit rozptyl hodnoty, použijte metody směrodatné odchylky a rozptylu:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

Dobrým způsobem, jak analyzovat stabilitu využití procesoru, je kombinovat stdev s mediánem výpočtu:

```Kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

Podívejte se na další lekce pro používání [dotazovacího jazyka Kusto](/azure/kusto/query/) s daty protokolu Azure Monitor:

- [Operace s řetězci](string-operations.md)
- [Operace s datem a časem](datetime-operations.md)
- [Pokročilé agregace](advanced-aggregations.md)
- [JSON a datové struktury](json-data-structures.md)
- [Psaní rozšířených dotazů](advanced-query-writing.md)
- [Spojení](joins.md)
- [Grafy](charts.md)

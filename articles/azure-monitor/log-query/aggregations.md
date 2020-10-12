---
title: Agregace v Azure Monitorch dotazech protokolu | Microsoft Docs
description: Popisuje agregační funkce v Azure Monitorch dotazech protokolů, které nabízejí užitečné způsoby analýzy dat.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: d164c53e7e2be55f3cede389901a256ba388808d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77670300"
---
# <a name="aggregations-in-azure-monitor-log-queries"></a>Agregace v Azure Monitorch dotazech protokolu

> [!NOTE]
> Před dokončením této lekce byste měli dokončit Začínáme [s portálem pro analýzu](get-started-portal.md) a [začít s dotazy](get-started-queries.md) .

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Tento článek popisuje agregační funkce v Azure Monitorch dotazech protokolů, které nabízejí užitečné způsoby analýzy dat. Všechny tyto funkce fungují s `summarize` operátorem, který vytváří tabulku s agregovanými výsledky vstupní tabulky.

## <a name="counts"></a>Počty

### <a name="count"></a>count
Spočítá počet řádků v sadě výsledků po použití libovolných filtrů. Následující příklad vrátí celkový počet řádků v tabulce _perf_ za posledních 30 minut. Výsledek se vrátí do sloupce s názvem *count_* , pokud mu nepřiřadíte konkrétní název:


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

Vizualizace timechart může být užitečná pro zobrazení trendu v průběhu času:

```Kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

Výstup z tohoto příkladu ukazuje trend počtu záznamů výkonu v intervalu 5 minut:

![Trend počtu](media/aggregations/count-trend.png)


### <a name="dcount-dcountif"></a>DCount, dcountif
Pomocí `dcount` a `dcountif` můžete spočítat jedinečné hodnoty v určitém sloupci. Následující dotaz vyhodnocuje, kolik různých počítačů odeslalo prezenční signály za poslední hodinu:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

Chcete-li spočítat pouze počítače se systémem Linux, které odesílají prezenční signály, použijte `dcountif` :

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>Vyhodnocování podskupin
K provedení počtu nebo jiných agregací pro podskupiny ve vašich datech použijte `by` klíčové slovo. Pokud například chcete spočítat počet různých počítačů se systémem Linux, které odeslaly prezenční signály v každé zemi nebo oblasti:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry
```

|RemoteIPCountry  | distinct_computers  |
------------------|---------------------|
|USA    | 19                  |
|Kanada           | 3                   |
|Irsko          | 0                   |
|Spojené království   | 0                   |
|Nizozemsko      | 2                   |


Pokud chcete analyzovat i menší podskupiny vašich dat, přidejte do oddílu Další názvy sloupců `by` . Můžete například chtít spočítat samostatné počítače z každé země nebo oblasti na OSType:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```

## <a name="percentiles-and-variance"></a>Percentily a odchylky
Při vyhodnocování numerických hodnot je běžné zvyklosti použít průměrně `summarize avg(expression)` . Na průměry jsou ovlivněny extrémní hodnoty, které charakterizují pouze několik případů. Chcete-li tento problém vyřešit, můžete použít méně citlivé funkce, například `median` nebo `variance` .

### <a name="percentile"></a>Percentil
Chcete-li najít medián hodnoty, použijte `percentile` funkci s hodnotou k určení percentilu:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

Můžete také zadat různé percentily pro získání agregovaného výsledku pro každou z těchto hodnot:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

To může ukazovat na to, že některé procesory počítačů mají podobné mediány, ale zatímco některé z nich jsou u mediánu konstantní, další počítače oznámily mnohem nižší a vyšší hodnoty procesoru, což znamená, že se v nich vyskytly špičky

### <a name="variance"></a>Variance
Chcete-li přímo vyhodnotit odchylku hodnoty, použijte metody směrodatné odchylky a odchylky:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

Dobrým způsobem, jak analyzovat stabilitu využití CPU, je kombinovat STDEV s výpočtem mediánu:

```Kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

Podívejte se na další lekce týkající se používání [dotazovacího jazyka Kusto](/azure/kusto/query/) s využitím dat protokolu Azure monitor:

- [Operace s řetězci](string-operations.md)
- [Operace s datem a časem](datetime-operations.md)
- [Pokročilé agregace](advanced-aggregations.md)
- [JSON a datové struktury](json-data-structures.md)
- [Psaní rozšířených dotazů](advanced-query-writing.md)
- [Spojení](joins.md)
- [Grafy](charts.md)

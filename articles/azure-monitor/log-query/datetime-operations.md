---
title: Práce s hodnotami data a času v Azure Monitorch dotazech protokolu | Microsoft Docs
description: Popisuje, jak pracovat s daty data a času v Azure Monitorch dotazech protokolu.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: ea7c98a1b5b4059c5fea0cf1e8ea2ff5ef08d9d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77655374"
---
# <a name="working-with-date-time-values-in-azure-monitor-log-queries"></a>Práce s hodnotami data a času v Azure Monitorch dotazech protokolu

> [!NOTE]
> Před dokončením této lekce byste měli dokončit Začínáme [s portálem pro analýzu](get-started-portal.md) a [začít s dotazy](get-started-queries.md) .

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Tento článek popisuje, jak pracovat s daty data a času v Azure Monitorch dotazech protokolu.


## <a name="date-time-basics"></a>Základy data a času
Dotazovací jazyk Kusto má dva hlavní datové typy spojené s daty a časy: DateTime a TimeSpan. Všechna data jsou vyjádřena ve formátu UTC. I když je podporováno více formátů data a času, je preferovaný formát ISO8601. 

Časové intervaly se vyjadřují jako desetinné číslo následované časovou jednotkou:

|zkrácený   | Časová jednotka    |
|:---|:---|
|d           | day          |
|h           | hour         |
|m           | minute       |
|s           | second       |
|Arial          | komponentu  |
|úrovni mikrosekund | úrovni mikrosekund  |
|libovoln        | nanosekund   |

Hodnoty DateTime lze vytvořit přetypováním řetězce pomocí `todatetime` operátoru. Chcete-li například zkontrolovat prezenční signály virtuálních počítačů odeslaných v určitém časovém období, použijte `between` operátor k zadání časového rozsahu.

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Dalším běžným scénářem je porovnávání hodnoty DateTime s aktuální hodnotou. Například chcete-li zobrazit všechny prezenční signály za poslední dvě minuty, můžete `now` operátor použít společně s časovým intervalem, který představuje dvě minuty:

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

K dispozici je také zástupce pro tuto funkci:
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

Nejkratší a nejvíc čitelná metoda sice používá `ago` operátor:
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Předpokládejme, že místo znalosti času začátku a konce znáte čas spuštění a dobu trvání. Dotaz můžete přepsat následujícím způsobem:

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>Převádění časových jednotek
Můžete chtít vyjádřit hodnoty DateTime nebo TimeSpan v jiné než výchozí časové jednotce. Například pokud prohlížíte události chyb za posledních 30 minut a potřebujete počítaný sloupec, který ukazuje, jak dlouho proběhla událost:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

`timeAgo`Sloupec obsahuje hodnoty, jako například: "00:09:31.5118992", což znamená, že jsou formátovány jako hh: mm: ss. fffffff. Pokud chcete tyto hodnoty naformátovat na `numver` minuty od počátečního času, vydělte tuto hodnotu hodnotou "1 minuty":

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Agregace a zablokování podle časových intervalů
Dalším běžným scénářem je nutnost získat statistiku v určitém časovém období v určitou dobu. V tomto scénáři `bin` může být operátor použit jako součást klauzule sumarizace.

K získání počtu událostí, ke kterým došlo každých 5 minut během poslední poloviny hodiny, použijte následující dotaz:

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

Tento dotaz vytvoří následující tabulku:  

|TimeGenerated (UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

Dalším způsobem, jak vytvořit sady výsledků, je používat funkce, jako například `startofday` :

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Tento dotaz vytvoří následující výsledky:

|časové razítko|count_|
|--|--|
|2018-07-28T00:00:00.000|7 136|
|2018-07-29T00:00:00.000|12 315|
|2018-07-30T00:00:00.000|16 847|
|2018-07-31T00:00:00.000|12 616|
|2018-08-01T00:00:00.000|5 416|


## <a name="time-zones"></a>Časová pásma
Vzhledem k tomu, že všechny hodnoty DateTime jsou vyjádřeny ve standardu UTC, je často užitečné tyto hodnoty převést do místního časového pásma. Například pomocí tohoto výpočtu převeďte čas UTC na formát PST:

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Související funkce

| Kategorie | Funkce |
|:---|:---|
| Převod datových typů | [ToDateTime](/azure/kusto/query/todatetimefunction)  [ToTimeSpan](/azure/kusto/query/totimespanfunction)  |
| Zaokrouhlit hodnotu na velikost přihrádky | [bin](/azure/kusto/query/binfunction) |
| Získat konkrétní datum nebo čas | [ago](/azure/kusto/query/agofunction) [ještě](/azure/kusto/query/nowfunction) před   |
| Získat část hodnoty | [datetime_part](/azure/kusto/query/datetime-partfunction) [GetMonth](/azure/kusto/query/getmonthfunction) [monthofyear](/azure/kusto/query/monthofyearfunction) [GetYear](/azure/kusto/query/getyearfunction) [DayOfMonth](/azure/kusto/query/dayofmonthfunction) [DayOfWeek](/azure/kusto/query/dayofweekfunction) [DAYOFYEAR](/azure/kusto/query/dayofyearfunction) [WeekOfYear](/azure/kusto/query/weekofyearfunction) |
| Získání relativní hodnoty data  | [endofday](/azure/kusto/query/endofdayfunction) [endofweek](/azure/kusto/query/endofweekfunction) [ENDOFMONTH](/azure/kusto/query/endofmonthfunction) [ENDOFYEAR](/azure/kusto/query/endofyearfunction) [startofday](/azure/kusto/query/startofdayfunction) [startofweek](/azure/kusto/query/startofweekfunction) [STARTOFMONTH](/azure/kusto/query/startofmonthfunction) [STARTOFYEAR](/azure/kusto/query/startofyearfunction) |

## <a name="next-steps"></a>Další kroky
Podívejte se na další lekce týkající se používání [dotazovacího jazyka Kusto](/azure/kusto/query/) s využitím dat protokolu Azure monitor:

- [Operace s řetězci](string-operations.md)
- [Agregační funkce](aggregations.md)
- [Pokročilé agregace](advanced-aggregations.md)
- [JSON a datové struktury](json-data-structures.md)
- [Psaní rozšířených dotazů](advanced-query-writing.md)
- [Spojení](joins.md)
- [Grafy](charts.md)

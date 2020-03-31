---
title: Práce s hodnotami data času v dotazech protokolu Azure Monitor| Dokumenty společnosti Microsoft
description: Popisuje, jak pracovat s daty data a času v dotazech protokolu Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: ea7c98a1b5b4059c5fea0cf1e8ea2ff5ef08d9d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655374"
---
# <a name="working-with-date-time-values-in-azure-monitor-log-queries"></a>Práce s hodnotami data času v dotazech protokolu Azure Monitor

> [!NOTE]
> Před dokončením této lekce byste měli dokončit [Začínáme s portálem Analytics](get-started-portal.md) a [Začínáme s dotazy.](get-started-queries.md)

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Tento článek popisuje, jak pracovat s daty data a času v dotazech protokolu Azure Monitor.


## <a name="date-time-basics"></a>Základy časového času
Dotazovací jazyk Kusto má dva hlavní datové typy přidružené k datům a časům: datetime a timespan. Všechna data jsou vyjádřena v UTC. Zatímco více formátů datetime jsou podporovány, formát ISO8601 je upřednostňován. 

Časové rozpětí jsou vyjádřeny jako desetinné číslo následované časovou jednotkou:

|Zkratka   | časová jednotka    |
|:---|:---|
|d           | day          |
|h           | hour         |
|m           | minute       |
|s           | second       |
|Paní          | Milisekund  |
|mikrosekunda | mikrosekunda  |
|Zaškrtněte        | Nanosecond   |

Datetimes lze vytvořit přetypování `todatetime` řetězec pomocí operátoru. Chcete-li například zkontrolovat prezenční signály virtuálního `between` počítače odeslané v určitém časovém rámci, použijte operátor k určení časového rozsahu.

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Dalším běžným scénářem je porovnání datatime do současnosti. Chcete-li například zobrazit všechny prezenční signály `now` za poslední dvě minuty, můžete operátor použít společně s časovým rozpětím, které představuje dvě minuty:

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Zkratka je také k dispozici pro tuto funkci:
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

Nejkratší a nejčitelnější metoda však `ago` používá operátor:
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Předpokládejme, že místo znalosti počátečního a koncového času znáte čas zahájení a dobu trvání. Dotaz můžete přepsat následujícím způsobem:

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>Převod časových jednotek
Můžete chtít vyjádřit datetime nebo časový čas v časové jednotce než ve výchozím nastavení. Pokud například kontrolujete chybové události za posledních 30 minut a potřebujete počítaný sloupec s uvedením, jak je událost před dlouhou dobou:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

Sloupec `timeAgo` obsahuje hodnoty, jako například: "00:09:31.5118992", což znamená, že jsou formátovány jako hh:mm:ss.fffffff. Pokud chcete formátovat tyto `numver` hodnoty na minuty od počátečního času, vydělte tuto hodnotu "1 minuta":

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Agregace a bucketing podle časových intervalů
Dalším běžným scénářem je potřeba získat statistiky za určité časové období v určitém časovém období. Pro tento scénář `bin` operátor lze použít jako součást sumarizovat klauzuli.

Pomocí následujícího dotazu můžete získat počet událostí, ke kterým došlo každých 5 minut během poslední půlhodiny:

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

Tento dotaz vytvoří následující tabulku:  

|TimeGenerated(UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

Dalším způsobem, jak vytvořit segmenty výsledků, `startofday`je použití funkcí, jako jsou :

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Tento dotaz přináší následující výsledky:

|časové razítko|count_|
|--|--|
|2018-07-28T00:00:00.000|7,136|
|2018-07-29T00:00:00.000|12,315|
|2018-07-30T00:00:00.000|16,847|
|2018-07-31T00:00:00.000|12,616|
|2018-08-01T00:00:00.000|5,416|


## <a name="time-zones"></a>Časová pásma
Vzhledem k tomu, že všechny hodnoty datetime jsou vyjádřeny v utc, je často užitečné převést tyto hodnoty do místníčasové pásmo. Tento výpočet použijte například k převodu času UTC na časy PST:

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Související funkce

| Kategorie | Funkce |
|:---|:---|
| Převod datových typů | [todatetime](/azure/kusto/query/todatetimefunction)  [totimespan](/azure/kusto/query/totimespanfunction)  |
| Zaokrouhlená hodnota na velikost přihrádky | [Přihrádky](/azure/kusto/query/binfunction) |
| Získání konkrétního data nebo času | [před](/azure/kusto/query/agofunction) [nyní](/azure/kusto/query/nowfunction)   |
| Získat část hodnoty | [datetime_part](/azure/kusto/query/datetime-partfunction) [získat měsíc](/azure/kusto/query/getmonthfunction) měsíc [rok](/azure/kusto/query/monthofyearfunction) [weekofyear](/azure/kusto/query/weekofyearfunction) [getyear](/azure/kusto/query/getyearfunction) [denměsícdenvtýdně](/azure/kusto/query/dayofmonthfunction) [dayofweek](/azure/kusto/query/dayofweekfunction) [denroku roku](/azure/kusto/query/dayofyearfunction) |
| Získat relativní hodnotu data  | [endofday](/azure/kusto/query/endofdayfunction) [endofweek](/azure/kusto/query/endofweekfunction) endofweek endofyear endofyear [startofday startofweek](/azure/kusto/query/startofweekfunction) startofmonth startofyear endofday [endofweek](/azure/kusto/query/endofmonthfunction) [endofweek endofyear endofday endofday](/azure/kusto/query/endofyearfunction) [startofday](/azure/kusto/query/startofdayfunction) [startofweek startofyear endofday](/azure/kusto/query/startofmonthfunction) [endofweek](/azure/kusto/query/startofyearfunction) |

## <a name="next-steps"></a>Další kroky
Podívejte se na další lekce pro používání [dotazovacího jazyka Kusto](/azure/kusto/query/) s daty protokolu Azure Monitor:

- [Operace s řetězci](string-operations.md)
- [Agregační funkce](aggregations.md)
- [Pokročilé agregace](advanced-aggregations.md)
- [JSON a datové struktury](json-data-structures.md)
- [Psaní rozšířených dotazů](advanced-query-writing.md)
- [Spojení](joins.md)
- [Grafy](charts.md)

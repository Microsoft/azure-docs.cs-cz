---
title: Práce s hodnotami data a času v dotazech protokolu Azure Monitor | Dokumentace Microsoftu
description: Popisuje, jak pracovat s daty datum a čas v dotazů na protokoly Azure monitoru.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: 8350524e51d8ced45586d085fe1b49274aa6db9d
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56269973"
---
# <a name="working-with-date-time-values-in-azure-monitor-log-queries"></a>Práce s hodnotami data a času v dotazů na protokoly Azure monitoru

> [!NOTE]
> By se měla Dokončit [začít používat portál Analytics](get-started-portal.md) a [Začínáme s dotazy](get-started-queries.md) před dokončením v této lekci.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Tento článek popisuje, jak pracovat s daty datum a čas v dotazů na protokoly Azure monitoru.


## <a name="date-time-basics"></a>Datum čas základy
Dotazovací jazyk Kusto má dva hlavní datové typy související s daty a časy: datetime a timespan. Všechna data jsou vyjádřeny ve standardu UTC. I když jsou podporované více formátů data a času, formát ISO8601 je upřednostňována. 

Časových rozpětí jsou vyjádřeny jako desítkové číslo, za nímž následuje časovou jednotku:

|Zkrácený tvar vlastností   | Časová jednotka    |
|:---|:---|
|d           | den          |
|hod.           | hodina         |
|min.           | minuta       |
|s           | sekunda       |
|ms          | Milisekundy  |
|úrovni mikrosekund | úrovni mikrosekund  |
|značky        | nanosekund   |

Data a času mohou vytvořit řetězec pomocí přetypování `todatetime` operátor. Například zkontrolujte prezenční signály virtuálního počítače odesílají v konkrétním časovém rámci, použijte `between` operátor zadat časový rozsah.

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Další z typických možností je porovnávání datetime až po současnost. Například pokud chcete zobrazit všechny prezenční signály za posledních dvou minut, můžete použít `now` operátor spolu s časový interval představující dvě minuty:

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Zástupce je také k dispozici pro tuto funkci:
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

I když pomocí metody co nejkratší a nejvíce čitelné `ago` operátor:
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Předpokládejme, že místo znalost počáteční a koncový čas, znát počáteční čas a dobu trvání. Dotaz je možné přepsat následujícím způsobem:

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>Převádění časových jednotek
Můžete chtít express datum a čas nebo časový interval v jiné než výchozí časovou jednotku. Například pokud revize chybové události z posledních 30 minut a potřebujete počítaný sloupec zobrazuje dobu k události došlo:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

`timeAgo` Sloupec obsahuje hodnoty jako například: "00:09:31.5118992", což znamená, že jste ve formátu jako hh:mm:ss.fffffff. Pokud chcete naformátovat tyto hodnoty `numver` minut od počáteční čas dělení tuto hodnotu "1 minuta":

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Agregace a kontejnery pomocí časových intervalů
Další z typických možností je potřeba získat statistiku za určité časové období v konkrétní časový interval. V tomto scénáři `bin` operátor může sloužit jako součást klauzule summarize.

Použijte tento dotaz se získat počet událostí, ke kterým došlo během poslední půlhodiny každých 5 minut:

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

Tento dotaz vytvoří v následující tabulce:  
|TimeGenerated(UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

Dalším způsobem, jak vytvořit kbelíků výsledků je použití funkcí, jako například `startofday`:

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Tento dotaz vytvoří se následující výsledky:

|časové razítko|count_|
|--|--|
|2018-07-28T00:00:00.000|7,136|
|2018-07-29T00:00:00.000|12,315|
|2018-07-30T00:00:00.000|16,847|
|2018-07-31T00:00:00.000|12,616|
|2018-08-01T00:00:00.000|5,416|


## <a name="time-zones"></a>Časová pásma
Protože všechny hodnoty data a času jsou vyjádřeny ve standardu UTC, často je užitečné k převedení těchto hodnot do místní časové pásmo. Tento výpočet například použijte k převodu času UTC na PST časy:

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Související funkce

| Kategorie | Funkce |
|:---|:---|
| Převést datové typy | [ToDateTime](/azure/kusto/query/todatetimefunction)[totimespan](/azure/kusto/query/totimespanfunction)  |
| Kruhové hodnota, která má velikost přihrádky | [Koš](/azure/kusto/query/binfunction) |
| Určité datum a čas | [před](/azure/kusto/query/agofunction) [nyní](/azure/kusto/query/nowfunction)   |
| Získat část hodnoty | [datetime_part](/azure/kusto/query/datetime-partfunction) [getmonth](/azure/kusto/query/getmonthfunction) [monthofyear](/azure/kusto/query/monthofyearfunction) [getyear](/azure/kusto/query/getyearfunction) [dayofmonth](/azure/kusto/query/dayofmonthfunction) [dayofweek](/azure/kusto/query/dayofweekfunction) [dayofyear](/azure/kusto/query/dayofyearfunction) [weekofyear](/azure/kusto/query/weekofyearfunction) |
| Získá hodnotu relativního data  | [endofday](/azure/kusto/query/endofdayfunction) [endofweek](/azure/kusto/query/endofweekfunction) [endofmonth](/azure/kusto/query/endofmonthfunction) [endofyear](/azure/kusto/query/endofyearfunction) [startofday](/azure/kusto/query/startofdayfunction) [startofweek](/azure/kusto/query/startofweekfunction) [startofmonth](/azure/kusto/query/startofmonthfunction) [startofyear](/azure/kusto/query/startofyearfunction) |

## <a name="next-steps"></a>Další postup
Zobrazit další lekce pro použití [Kusto dotazovací jazyk](/azure/kusto/query/) službou Azure Monitor můžete vytvářet protokoly dat:

- [Operace s řetězci](string-operations.md)
- [Agregační funkce](aggregations.md)
- [Pokročilé agregace](advanced-aggregations.md)
- [JSON a datových struktur](json-data-structures.md)
- [Zápis rozšířeného dotazu](advanced-query-writing.md)
- [Spojení](joins.md)
- [Grafy](charts.md)
---
title: Rozšířené agregace v dotazech protokolu Azure Monitor| Dokumenty společnosti Microsoft
description: Popisuje některé pokročilejší možnosti agregace, které jsou k dispozici pro dotazy protokolu Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: e5dc290a40342e0797001dde6cab90e12dd5cf39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662174"
---
# <a name="advanced-aggregations-in-azure-monitor-log-queries"></a>Pokročilá agregace v dotazech protokolu Azure Monitor

> [!NOTE]
> Před dokončením této lekce byste měli dokončit [agregace v dotazech Azure Monitoru.](./aggregations.md)

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Tento článek popisuje některé pokročilejší možnosti agregace, které jsou k dispozici pro dotazy Azure Monitoru.

## <a name="generating-lists-and-sets"></a>Generování seznamů a sad
Data můžete `makelist` použít k otočení dat podle pořadí hodnot v určitém sloupci. Můžete například chtít prozkoumat nejběžnější události objednávky, které se odehrávají na vašich počítačích. Data můžete v podstatě otáčet podle pořadí ID událostí na každém počítači. 

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

|Počítač|list_EventID|
|---|---|
| počítač1 | [704,701,1501,1500,1085,704,704,701] |
| počítač2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist`vygeneruje seznam v pořadí, v jakém do něj byla data předána. Chcete-li seřadit události `asc` od nejstarších `desc`po nejnovější, použijte v příkazu objednávky namísto . 

Je také užitečné vytvořit seznam pouze odlišné hodnoty. To se nazývá _Set_ a mohou `makeset`být generovány s :

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

|Počítač|list_EventID|
|---|---|
| počítač1 | [704,701,1501,1500,1085] |
| počítač2 | [326,105,302,301,300,102] |
| ... | ... |

Stejně jako `makelist`, `makeset` také pracuje s objednanými daty a bude generovat pole na základě pořadí řádků, které jsou do něj předány.

## <a name="expanding-lists"></a>Rozbalení seznamů
Inverzní operace `makelist` `makeset` nebo `mvexpand`je , který rozbalí seznam hodnot na samostatné řádky. Může expandovat v libovolném počtu dynamických sloupců, json a pole. V tabulce *Prezenční signál* můžete například zkontrolovat řešení odesílající data z počítačů, které odeslali prezenční signál za poslední hodinu:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

| Počítač | Řešení | 
|--------------|----------------------|
| počítač1 | "zabezpečení", "aktualizace", "changeTracking" |
| počítač2 | "zabezpečení", "aktualizace" |
| počítač3 | "antiMalware", "changeTracking" |
| ... | ... |

Slouží `mvexpand` k zobrazení každé hodnoty v samostatném řádku namísto seznamu odděleného čárkami:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
```

| Počítač | Řešení | 
|--------------|----------------------|
| počítač1 | "bezpečnost" |
| počítač1 | "aktualizace" |
| počítač1 | "changeTracking" |
| počítač2 | "bezpečnost" |
| počítač2 | "aktualizace" |
| počítač3 | "antiMalware" |
| počítač3 | "changeTracking" |
| ... | ... |


Potom můžete `makelist` znovu seskupit položky dohromady a tentokrát zobrazit seznam počítačů podle řešení:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

|Řešení | list_Computer |
|--------------|----------------------|
| "bezpečnost" | ["computer1", "computer2"] |
| "aktualizace" | ["computer1", "computer2"] |
| "changeTracking" | ["computer1", "computer3"] |
| "antiMalware" | ["počítač3"] |
| ... | ... |

## <a name="handling-missing-bins"></a>Zpracování chybějících přihrádek
Užitečnou aplikací `mvexpand` je potřeba vyplnit výchozí hodnoty chybějících přihrádek. Předpokládejme například, že hledáte dobu využití konkrétního počítače tím, že zkoumáte jeho prezenční signál. Chcete také zobrazit zdroj prezenčního signálu, který je ve _sloupci kategorie._ Za normálních okolností bychom použít jednoduchý souhrn prohlášení takto:

```Kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```

| Kategorie | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Přímý agent | 2017-06-06T17:00:00Z | 15 |
| Přímý agent | 2017-06-06T18:00:00Z | 60 |
| Přímý agent | 2017-06-06T20:00:00Z | 55 |
| Přímý agent | 2017-06-06T21:00:00Z | 57 |
| Přímý agent | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

V těchto výsledcích však chybí kontejner přidružený k "2017-06-06T19:00:00Z", protože pro tuto hodinu neexistují žádná data prezenčního signálu. Pomocí `make-series` funkce přiřaďte výchozí hodnotu prázdným blokům. Tím se vygeneruje řádek pro každou kategorii se dvěma dalšími sloupci pole, jeden pro hodnoty a jeden pro odpovídající časové intervaly:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Kategorie | count_ | TimeGenerated |
|---|---|---|
| Přímý agent | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000z","2017-06-06T18:00:00.0000000z","2017-06-06T19:0 0:00.0000000Z","2017-06-06T20:00:00.000000Z","2017-06-06T21:00.00.000000Z",...] |
| ... | ... | ... |

Třetí prvek *pole count_* je 0 podle očekávání a je odpovídající časové razítko "2017-06-06T19:00.0000000Z" v _poli TimeGenerated._ Tento formát pole je obtížné číst ačkoli. Slouží `mvexpand` k rozšíření polí a vytvoření stejného výstupu `summarize`formátu, jako je generováno :

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mvexpand TimeGenerated, count_
| project Category, TimeGenerated, count_
```

| Kategorie | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Přímý agent | 2017-06-06T17:00:00Z | 15 |
| Přímý agent | 2017-06-06T18:00:00Z | 60 |
| Přímý agent | 2017-06-06T19:00:00Z | 0 |
| Přímý agent | 2017-06-06T20:00:00Z | 55 |
| Přímý agent | 2017-06-06T21:00:00Z | 57 |
| Přímý agent | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



## <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Zúžení výsledků na `let`sadu `makeset` `toscalar`prvků: , , ,`in`
Běžným scénářem je vybrat názvy některých konkrétních entit na základě sady kritérií a potom filtrovat jinou sadu dat pro tuto sadu entit. Můžete například najít počítače, o kterých je známo, že mají chybějící aktualizace, a identifikovat adresy IP, na které tyto počítače volaly:


```Kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="next-steps"></a>Další kroky

Podívejte se na další lekce pro používání [dotazovacího jazyka Kusto](/azure/kusto/query/) s daty protokolu Azure Monitor:

- [Operace s řetězci](string-operations.md)
- [Operace s datem a časem](datetime-operations.md)
- [Agregační funkce](aggregations.md)
- [Pokročilé agregace](advanced-aggregations.md)
- [JSON a datové struktury](json-data-structures.md)
- [Spojení](joins.md)
- [Grafy](charts.md)

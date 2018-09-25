---
title: Pokročilé agregace v dotazech Azure Log Analytics | Dokumentace Microsoftu
description: Popisuje některé pokročilejší možnosti agregace, která je k dispozici pro dotazy Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 288af0eae50634f44d6af8c787b56112bb3119ff
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46998589"
---
# <a name="advanced-aggregations-in-log-analytics-queries"></a>Pokročilé agregace v dotazy Log Analytics

> [!NOTE]
> By se měla Dokončit [agregace v dotazy Log Analytics](./aggregations.md) před dokončením v této lekci.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Tento článek popisuje některé pokročilejší možnosti agregace, která je k dispozici pro dotazy Log Analytics.

## <a name="generating-lists-and-sets"></a>Generování seznamy a sad
Můžete použít `makelist` kontingenční data pořadím hodnot v určitém sloupci. Můžete třeba prozkoumat nejběžnější pořadí událostí se provádějí na počítačích. V podstatě můžete přesouvat data pořadím identifikátory EventID v rozmezí na každém počítači. 

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```
|Počítač|list_EventID|
|---|---|
| POČÍTAČ1 | [704,701,1501,1500,1085,704,704,701] |
| POČÍTAČ2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist` vygeneruje seznam v pořadí, ve kterém dat byl předán do něj. Chcete-li seřadit události od nejstarších k nejnovějším, použijte `asc` v příkazu pořadí místo `desc`. 

Je také užitečné vytvořit seznam pouze jedinečné hodnoty. Tento postup se nazývá _nastavit_ a dá vygenerovat pomocí `makeset`:

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```
|Počítač|list_EventID|
|---|---|
| POČÍTAČ1 | [704,701,1501,1500,1085] |
| POČÍTAČ2 | [326,105,302,301,300,102] |
| ... | ... |

Stejně jako `makelist`, `makeset` také pracuje s seřazených dat a bude generovat pole na základě pořadí řádky, které jsou předány do něj.

## <a name="expanding-lists"></a>Rozbalování seznamů
Inverzní operace k `makelist` nebo `makeset` je `mvexpand`, který rozbalí seznam hodnot k oddělení řádků. Můžete rozbalit napříč libovolným počtem dynamické sloupce, JSON a pole. Například můžete zkontrolovat *prezenčního signálu* tabulky pro odesílání dat z počítačů, které odeslaly prezenční signál za poslední hodinu řešení:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

| Počítač | Řešení | 
|--------------|----------------------|
| POČÍTAČ1 | "zabezpečení", "aktualizace", "sledování změn ve" |
| POČÍTAČ2 | "zabezpečení", "aktualizace" |
| POČÍTAČ3 | "Antimalwarové", "sledování změn ve" |
| ... | ... | ... |

Použití `mvexpand` zobrazíte každá hodnota na samostatném řádku namísto seznam oddělený čárkami:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
```

| Počítač | Řešení | 
|--------------|----------------------|
| POČÍTAČ1 | "zabezpečení" |
| POČÍTAČ1 | "aktualizace" |
| POČÍTAČ1 | "sledování změn ve" |
| POČÍTAČ2 | "zabezpečení" |
| POČÍTAČ2 | "aktualizace" |
| POČÍTAČ3 | "Antimalwarové" |
| POČÍTAČ3 | "sledování změn ve" |
| ... | ... | ... |


Můžete pak použít `makelist` znovu k seskupení položek najednou a tentokrát naleznete v seznamu počítačů podle řešení:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```
|Řešení | list_Computer |
|--------------|----------------------|
| "zabezpečení" | ["Počítač1", "počítač2"] |
| "aktualizace" | ["Počítač1", "počítač2"] |
| "sledování změn ve" | ["Počítač1", "POČÍTAČ3"] |
| "Antimalwarové" | ["POČÍTAČ3"] |
| ... | ... |

## <a name="handling-missing-bins"></a>Chybějící přihrádek zpracování
Užitečné použití `mvexpand` je potřeba vyplnit výchozí hodnoty pro chybějící přihrádky. Předpokládejme například, že se díváte provozuschopnost konkrétní počítač, ve kterých prezenčního signálu. Chcete také zobrazit prezenčního signálu, který je ve zdroji _kategorie_ sloupce. Za normálních okolností bychom použili jednoduchý příkaz shrnout takto:

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

V těchto výsledků ale sady přidružené k "2017-06-06T19:00:00Z" chybí, protože není k dispozici žádná data prezenčního signálu pro určitou hodinu. Použití `make-series` funkci přiřadí výchozí hodnotu prázdných kbelíků. Tím se vygeneruje řádek pro každou kategorii se dvěma sloupci další pole, jeden pro hodnoty a jeden pro odpovídající časovým intervalům:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Kategorie | count_ | TimeGenerated |
|---|---|---|
| Přímý agent | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

Třetího prvku pole *count_* pole má hodnotu 0, podle očekávání a neexistuje odpovídající razítko "2017-06-06T19:00:00.0000000Z" v _TimeGenerated_ pole. Formát tohoto pole je obtížné číst ale. Použití `mvexpand` rozbalte pole a vytvořit stejný formát výstupu vygenerovanými `summarize`:

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



## <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Zúžení výsledků pro sadu elementů: `let`, `makeset`, `toscalar`, `in`
Běžný scénář, kdy je výběr názvy některé konkrétní entity založené na sadě kritérií a potom filtrování jinou sadu dat do této sady entit. Například můžete najít počítače, na kterých je známo, že jim chybí aktualizace a identifikovat IP adresy, které uvádějí tyto počítače k:


```Kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="next-steps"></a>Další postup

Zobrazit další lekce pro používání dotazovací jazyk Log Analytics:

- [Operace s řetězci](string-operations.md)
- [Datum a čas operace](datetime-operations.md)
- [Agregační funkce](aggregations.md)
- [Pokročilé agregace](advanced-aggregations.md)
- [JSON a datových struktur](json-data-structures.md)
- [Spojení](joins.md)
- [Grafy](charts.md)
---
title: Rozšířené agregace v Azure Monitorch dotazech protokolu | Microsoft Docs
description: Popisuje některé pokročilejší možnosti agregace, které jsou k dispozici pro Azure Monitor dotazy protokolu.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: dba058dce09e958a2ae769d927a5569fb3e42113
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87324552"
---
# <a name="advanced-aggregations-in-azure-monitor-log-queries"></a>Pokročilé agregace v Azure Monitorch dotazech protokolu

> [!NOTE]
> Před dokončením této lekce byste měli dokončit [agregace v Azure monitor dotazů](./aggregations.md) .

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Tento článek popisuje některé pokročilejší možnosti agregace dostupné pro Azure Monitor dotazů.

## <a name="generating-lists-and-sets"></a>Generování seznamů a sad
Můžete použít `makelist` k pivotování dat podle pořadí hodnot v určitém sloupci. Můžete například chtít prozkoumat nejběžnější události pořadí na vašich počítačích. Data můžete v podstatě pivotovat podle pořadí identifikátory EventID rozmezí na každém počítači. 

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

|Počítač|list_EventID|
|---|---|
| Počítač1 | [704, 701, 1501, 1500, 1085, 704, 704, 701] |
| počítač2 | [326 105 302 301 300 102] |
| ... | ... |

`makelist` vygeneruje seznam v pořadí, do kterého byla data předána. Chcete-li události seřadit od nejstarších k nejnovějším, použijte `asc` příkaz v příkazu Order místo `desc` . 

Je také užitečné vytvořit seznam jenom různých hodnot. Tato metoda se nazývá _sada_ a je možné ji vygenerovat `makeset` :

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

|Počítač|list_EventID|
|---|---|
| Počítač1 | [704, 701, 1501, 1500, 1085] |
| počítač2 | [326 105 302 301 300 102] |
| ... | ... |

Stejně `makelist` tak `makeset` funguje také se seřazenými daty a vygeneruje pole na základě pořadí řádků, které jsou do ní předány.

## <a name="expanding-lists"></a>Rozbalování seznamů
Inverzní operace `makelist` nebo `makeset` je `mvexpand` , která rozšiřuje seznam hodnot na samostatné řádky. Může se rozšířit na libovolný počet dynamických sloupců, JSON i Array. Můžete například zaškrtnout tabulku *prezenčního signálu* pro řešení odesílající data z počítačů, které odeslaly prezenční signál za poslední hodinu:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

| Počítač | Řešení | 
|--------------|----------------------|
| Počítač1 | "zabezpečení", "aktualizace", "sledování změn ve" |
| počítač2 | "zabezpečení", "aktualizace" |
| computer3 | "antimalware", "sledování změn ve" |
| ... | ... |

Slouží `mvexpand` k zobrazení každé hodnoty v samostatném řádku namísto čárkami odděleného seznamu:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
```

| Počítač | Řešení | 
|--------------|----------------------|
| Počítač1 | bezpečnost |
| Počítač1 | aktualizovány |
| Počítač1 | Sledování změn ve |
| počítač2 | bezpečnost |
| počítač2 | aktualizovány |
| computer3 | Antimalwarové |
| computer3 | Sledování změn ve |
| ... | ... |


Pak můžete znovu použít `makelist` k seskupení položek dohromady a tentokrát se zobrazí seznam počítačů na řešení:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

|Řešení | list_Computer |
|--------------|----------------------|
| bezpečnost | ["Počítač1"; "POČÍTAČ2"] |
| aktualizovány | ["Počítač1"; "POČÍTAČ2"] |
| Sledování změn ve | ["Počítač1"; "computer3"] |
| Antimalwarové | ["computer3"] |
| ... | ... |

## <a name="handling-missing-bins"></a>Zpracování chybějících přihrádek
Užitečnou aplikací `mvexpand` je nutnost vyplnit výchozí hodnoty v části pro chybějící přihrádky. Předpokládejme například, že hledáte dobu provozu konkrétního počítače prozkoumáním jeho prezenčního signálu. Také chcete zobrazit zdroj prezenčního signálu, který je ve sloupci _kategorie_ . V normálním případě bychom použili jednoduchý příkaz sumarizace následujícím způsobem:

```Kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```

| Kategorie | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Přímý Agent | 2017-06-06T17:00:00Z | 15 |
| Přímý Agent | 2017-06-06T18:00:00Z | 60 |
| Přímý Agent | 2017-06-06T20:00:00Z | 55 |
| Přímý Agent | 2017-06-06T21:00:00Z | 57 |
| Přímý Agent | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

V takovém případě se v důsledku chybějícího kontejneru přidruženého k "2017-06-06T19:00:00Z" nezobrazí žádná data prezenčního signálu za tuto hodinu. Použijte `make-series` funkci k přiřazení výchozí hodnoty k prázdným kontejnerům. Tím se vygeneruje řádek pro každou kategorii se dvěma dalšími sloupci pole, jeden pro hodnoty a jeden pro porovnání časových intervalů:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Kategorie | count_ | TimeGenerated |
|---|---|---|
| Přímý Agent | [15, 60, 0, 55, 60, 57, 60,...] | ["2017-06-06T17:00:00.0000000 Z", "2017-06-06T18:00:00.0000000 Z", "2017-06-06T19:00:00.0000000 Z", "2017-06-06T20:00:00.0000000 Z", "2017-06-06T21:00:00.0000000 Z",...] |
| ... | ... | ... |

Třetí prvek *count_* pole je 0 podle očekávání a v poli _TimeGenerated_ je k dispozici párové časové razítko "2017-06-06T19:00:00.0000000 z". Tento formát pole je obtížné přečíst i v takovém případě. Použijte `mvexpand` k rozšíření polí a vytvoření stejného výstupu formátu generovaného pomocí `summarize` :

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mvexpand TimeGenerated, count_
| project Category, TimeGenerated, count_
```

| Kategorie | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Přímý Agent | 2017-06-06T17:00:00Z | 15 |
| Přímý Agent | 2017-06-06T18:00:00Z | 60 |
| Přímý Agent | 2017-06-06T19:00:00Z | 0 |
| Přímý Agent | 2017-06-06T20:00:00Z | 55 |
| Přímý Agent | 2017-06-06T21:00:00Z | 57 |
| Přímý Agent | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



## <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Zúžení výsledků do sady elementů: `let` , `makeset` , `toscalar` , `in`
Běžným scénářem je výběr názvů některých konkrétních entit na základě sady kritérií a následného filtrování různých datových sad na tuto sadu entit. Můžete například najít počítače, u kterých se ví, že mají chybějící aktualizace, a identifikovat IP adresy, na které tyto počítače zavolaly:


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

Podívejte se na další lekce týkající se používání [dotazovacího jazyka Kusto](/azure/kusto/query/) s využitím dat protokolu Azure monitor:

- [Operace s řetězci](string-operations.md)
- [Operace s datem a časem](datetime-operations.md)
- [Agregační funkce](aggregations.md)
- [Pokročilé agregace]()
- [JSON a datové struktury](json-data-structures.md)
- [Spojení](joins.md)
- [Grafy](charts.md)


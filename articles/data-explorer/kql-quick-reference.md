---
title: Stručná reference KQL
description: Seznam užitečných funkcí KQL a jejich definic s příklady syntaxe.
author: orspod
ms.author: orspodek
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: e7196ccccb1203ce56b2e53fa358d091374cd3f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139075"
---
# <a name="kql-quick-reference"></a>Stručná reference KQL

V tomto článku se zobrazí seznam funkcí a jejich popisů, které vám pomohou začít používat kusto dotazovací jazyk.

| Operátor/funkce                               | Popis                           | Syntaxe                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**Filtr/Hledat/Podmínka**                      |**_Vyhledání relevantních dat filtrováním nebo vyhledáváním_** |                      |
| [where](/azure/kusto/query/whereoperator)                      | Filtry na konkrétním predikátu           | `T | where Predicate`                         |
| [kde obsahuje/má](/azure/kusto/query/whereoperator)        | `Contains`: Hledá všechny shody podřetězce. <br> `Has`: Hledá konkrétní slovo (lepší výkon)  | `T | where col1 contains/has "[search term]"`|
| [Hledat](/azure/kusto/query/searchoperator)                    | Vyhledá hodnotu ve všech sloupcích v tabulce. | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [vzít](/azure/kusto/query/takeoperator)                        | Vrátí zadaný počet záznamů. Použití k testování dotazu<br>**_Poznámka_** `_take`: `_limit`_ a _ jsou synonyma. | `T | take NumberOfRows` |
| [Případě](/azure/kusto/query/casefunction)                        | Přidá příkaz podmínky, podobně jako if/then/elseif v jiných systémech. | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [distinct](/azure/kusto/query/distinctoperator)                | Vytvoří tabulku s výraznou kombinací poskytnutých sloupců vstupní tabulky. | `distinct [ColumnName], [ColumnName]` |
| **Datum a čas**                                   |**_Operace, které používají funkce data a času_**               |                          |
|[před](/azure/kusto/query/agofunction)                           | Vrátí posun času vzhledem k době spuštění dotazu. Například `ago(1h)` je jedna hodina před čtením aktuálníhodiny. | `ago(a_timespan)` |
| [format_datetime](/azure/kusto/query/format-datetimefunction)  | Vrátí data v [různých formátech kalendářních dat](/azure/kusto/query/format-datetimefunction#supported-formats). | `format_datetime(datetime , format)` |
| [Přihrádky](/azure/kusto/query/binfunction)                          | Zaokrouhlí všechny hodnoty v časovém rámci a seskupí je. | `bin(value,roundTo)` |
| **Vytvořit nebo odebrat sloupce**                   |**_Přidání nebo odebrání sloupců v tabulce_** |                                                    |
| [Tisk](/azure/kusto/query/printoperator)                      | Výstup y jednoho řádku s jedním nebo více skalárními výrazy | `print [ColumnName =] ScalarExpression [',' ...]` |
| [Projektu](/azure/kusto/query/projectoperator)                  | Vybere sloupce, které chcete zahrnout do zadaného pořadí. | `T | project ColumnName [= Expression] [, ...]` <br> Nebo <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [projekt-pryč](/azure/kusto/query/projectawayoperator)         | Vybere sloupce, které chcete vyloučit z výstupu. | `T | project-away ColumnNameOrPattern [, ...]` |
| [Rozšířit](/azure/kusto/query/extendoperator)                    | Vytvoří počítaný sloupec a přidá jej do sady výsledků. | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **Řazení a agregace datové sady**                 |**_Restrukturalizovat data tak, že je seřadíte nebo seskupíte smysluplným způsobem_**|                  |
| [Řazení](/azure/kusto/query/sortoperator)                        | Seřadí řádky vstupní tabulky podle jednoho nebo více sloupců vzestupně nebo sestupně. | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [Top](/azure/kusto/query/topoperator)                          | Vrátí první N řádky datové sady při řazení datové sady pomocí`by` | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [Sumarizovat](/azure/kusto/query/summarizeoperator)              | Seskupí `by` řádky podle sloupců skupiny a vypočítá agregace v jednotlivých skupinách. | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [count](/azure/kusto/query/countoperator)                       | Počítá záznamy ve vstupní tabulce (například T)<br>Tento operátor je zkratkou pro`summarize count() `| `T | count` |
| [Připojit](/azure/kusto/query/joinoperator)                        | Sloučí řádky dvou tabulek a vytvoří novou tabulku porovnáním hodnot zadaného sloupce (sloupců) z každé tabulky. Podporuje celou řadu typů `flouter`spojení: `innerunique` `leftanti`, `leftantisemi` `inner`, `leftsemi` `rightanti`, `rightantisemi` `rightouter`, `leftouter`, , , , , ,`rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [Unie](/azure/kusto/query/unionoperator)                      | Trvá dvě nebo více tabulek a vrátí všechny jejich řádky | `[T1] | union [T2], [T3], …` |
| [Rozsah](/azure/kusto/query/rangeoperator)                      | Generuje tabulku s aritmetické řady hodnot | `range columnName from start to stop step step` |
| **Formát dat**                                 | **_Restrukturalizace dat na výstup užitečným způsobem_** | |
| [Vyhledávání](/azure/kusto/query/lookupoperator)                    | Rozšíří sloupce tabulky faktů o hodnoty vyhledávaných v tabulce dimenzí. | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [mv-expand](/azure/kusto/query/mvexpandoperator)               | Změní dynamická pole na řádky (rozšíření s více hodnotami) | `T | mv-expand Column` |
| [Analyzovat](/azure/kusto/query/parseoperator)                      | Vyhodnotí řetězcový výraz a analyzuje jeho hodnotu do jednoho nebo více počítaných sloupců. Používá se pro strukturování nestrukturovaných dat. | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [řada make-series](/azure/kusto/query/make-seriesoperator)          | Vytvoří řadu zadaných agregovaných hodnot podél zadané osy. | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [Nechat](/azure/kusto/query/letstatement)                         | Sváže název s výrazy, které mohou odkazovat na jeho vázanou hodnotu. Hodnoty mohou být lambda výrazy k vytvoření ad-hoc funkce jako součást dotazu. Slouží `let` k vytváření výrazů nad tabulkami, jejichž výsledky vypadají jako nová tabulka. | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **Obecné**                                     | **_Různé operace a funkce_** | |
| [Vyvolat](/azure/kusto/query/invokeoperator)                    | Spustí funkci v tabulce, která obdrží jako vstup. | `T | invoke function([param1, param2])` |
| [vyhodnotit pluginName](/azure/kusto/query/evaluateoperator)     | Vyhodnocuje rozšíření dotazovacího jazyka (pluginy) | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **Vizualizace**                               | **_Operace, které zobrazují data v grafickém formátu_** | |
| [Vykreslení](/azure/kusto/query/renderoperator) | Vykreslí výsledky jako grafický výstup | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |

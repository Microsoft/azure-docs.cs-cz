---
title: Rychlý přehled KQL
description: Seznam užitečných funkcí KQL a jejich definicí s příklady syntaxe.
author: orspod
ms.author: orspodek
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: e7196ccccb1203ce56b2e53fa358d091374cd3f8
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2020
ms.locfileid: "79139075"
---
# <a name="kql-quick-reference"></a>Rychlý přehled KQL

Tento článek ukazuje seznam funkcí a jejich popisy, které vám pomůžou začít používat dotazovací jazyk Kusto.

| Operator/– funkce                               | Popis                           | Syntaxe                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**Filtr/hledání/podmínka**                      |**_Hledání relevantních dat filtrováním nebo hledáním_** |                      |
| [,](/azure/kusto/query/whereoperator)                      | Filtry pro určitý predikát           | `T | where Predicate`                         |
| [kde obsahuje/má](/azure/kusto/query/whereoperator)        | `Contains`: vyhledá všechny shody podřetězců. <br> `Has`: vyhledá konkrétní slovo (lepší výkon).  | `T | where col1 contains/has "[search term]"`|
| [nápovědě](/azure/kusto/query/searchoperator)                    | Vyhledá hodnotu v všech sloupcích v tabulce. | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [nezbytná](/azure/kusto/query/takeoperator)                        | Vrátí zadaný počet záznamů. Použijte k otestování dotazu.<br>**_Poznámka_** : `_take`_ a `_limit`_ jsou synonyma. | `T | take NumberOfRows` |
| [tom](/azure/kusto/query/casefunction)                        | Přidá příkaz podmínky, podobně jako if/then/ElseIf v jiných systémech. | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [znak](/azure/kusto/query/distinctoperator)                | Vytvoří tabulku s jedinečnou kombinací zadaných sloupců vstupní tabulky. | `distinct [ColumnName], [ColumnName]` |
| **Datum a čas**                                   |**_Operace, které používají funkce pro datum a čas_**               |                          |
|[datem](/azure/kusto/query/agofunction)                           | Vrátí časový posun relativně k době spuštění dotazu. `ago(1h)` je například jedna hodina před čtením aktuálního času. | `ago(a_timespan)` |
| [format_datetime](/azure/kusto/query/format-datetimefunction)  | Vrátí data v [různých formátech data](/azure/kusto/query/format-datetimefunction#supported-formats). | `format_datetime(datetime , format)` |
| [bin](/azure/kusto/query/binfunction)                          | Zaokrouhlí všechny hodnoty v určitém časovém období a seskupí je. | `bin(value,roundTo)` |
| **Vytvořit nebo odebrat sloupce**                   |**_Přidání nebo odebrání sloupců v tabulce_** |                                                    |
| [Tisk](/azure/kusto/query/printoperator)                      | Vytvoří výstup jednoho řádku s jedním nebo více skalárními výrazy. | `print [ColumnName =] ScalarExpression [',' ...]` |
| [projektem](/azure/kusto/query/projectoperator)                  | Vybere sloupce, které se mají zahrnout do zadaného pořadí. | `T | project ColumnName [= Expression] [, ...]` <br> Nebo <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [projekt – pryč](/azure/kusto/query/projectawayoperator)         | Vybere sloupce, které se mají vyloučit z výstupu. | `T | project-away ColumnNameOrPattern [, ...]` |
| [zvětšení](/azure/kusto/query/extendoperator)                    | Vytvoří počítaný sloupec a přidá ho do sady výsledků dotazu. | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **Seřadit a agregovat datovou sadu**                 |**_Restrukturování dat jejich řazením nebo seskupením smysluplných způsobů_**|                  |
| [druhu](/azure/kusto/query/sortoperator)                        | Seřadí řádky vstupní tabulky podle jednoho nebo více sloupců ve vzestupném nebo sestupném pořadí. | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [vrchol](/azure/kusto/query/topoperator)                          | Vrátí prvních N řádků datové sady, když je datová sada seřazena pomocí `by` | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [Souhrn](/azure/kusto/query/summarizeoperator)              | Seskupí řádky podle sloupců skupin `by` a vypočítá agregace pro každou skupinu. | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [count](/azure/kusto/query/countoperator)                       | Spočítá záznamy ve vstupní tabulce (například T).<br>Tento operátor je zkrácený pro `summarize count() `| `T | count` |
| [join](/azure/kusto/query/joinoperator)                        | Sloučí řádky dvou tabulek a vytvoří novou tabulku tak, že bude odpovídat hodnotám zadaného sloupce z každé tabulky. Podporuje celou řadu typů spojení: `flouter`, `inner`, `innerunique`, `leftanti`, `leftantisemi`, `leftouter`, `leftsemi`, `rightanti`, `rightantisemi`, `rightouter`, `rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [sjednocovací](/azure/kusto/query/unionoperator)                      | Provede dvě nebo více tabulek a vrátí všechny jejich řádky. | `[T1] | union [T2], [T3], …` |
| [oblasti](/azure/kusto/query/rangeoperator)                      | Vygeneruje tabulku s aritmetickou řadou hodnot. | `range columnName from start to stop step step` |
| **Formátování dat**                                 | **_Vhodným způsobem přestrukturování dat na výstup_** | |
| [prohledávání](/azure/kusto/query/lookupoperator)                    | Rozšiřuje sloupce tabulky faktů o vyhledaných hodnotách v tabulce dimenzí. | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [MV – rozbalit](/azure/kusto/query/mvexpandoperator)               | Převede dynamická pole na řádky (rozšíření s více hodnotami). | `T | mv-expand Column` |
| [Analýza](/azure/kusto/query/parseoperator)                      | Vyhodnotí řetězcový výraz a analyzuje jeho hodnotu do jednoho nebo více počítaných sloupců. Slouží ke strukturování nestrukturovaných dat. | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [vytvořit řadu](/azure/kusto/query/make-seriesoperator)          | Vytvoří řadu zadaných agregovaných hodnot podél zadané osy. | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [aplikaci](/azure/kusto/query/letstatement)                         | Vytvoří vazbu názvu na výrazy, které mohou odkazovat na jeho vázanou hodnotu. Hodnoty mohou být výrazy lambda pro vytvoření ad hoc funkcí v rámci dotazu. Pomocí `let` můžete vytvářet výrazy nad tabulkami, jejichž výsledky vypadají jako nová tabulka. | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **Obecné**                                     | **_Různé operace a funkce_** | |
| [Zavolejte](/azure/kusto/query/invokeoperator)                    | Spustí funkci v tabulce, kterou přijme jako vstup. | `T | invoke function([param1, param2])` |
| [vyhodnotit plugin](/azure/kusto/query/evaluateoperator)     | Vyhodnocuje jazykové rozšíření dotazu (moduly plug-in). | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **Vizualizac**                               | **_Operace, které zobrazují data v grafickém formátu_** | |
| [činit](/azure/kusto/query/renderoperator) | Vykreslí výsledky jako grafický výstup. | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |

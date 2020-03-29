---
title: Dotaz protokolu Splunk to Azure Monitor | Dokumenty společnosti Microsoft
description: Nápověda pro uživatele, kteří jsou obeznámeni s Splunk při učení dotazů protokolu Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: 6346055f1169bfa533d5dbfe441ecf27fb0d78a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75397744"
---
# <a name="splunk-to-azure-monitor-log-query"></a>Dotaz protokolu Splunk to Azure Monitor

Tento článek je určen k pomoci uživatelům, kteří jsou obeznámeni s Splunk naučit kusto dotazovací jazyk psát dotazy protokolu v Azure Monitor. Přímé srovnání jsou prováděny mezi těmito dvěma pochopit klíčové rozdíly a také podobnosti, kde můžete využít své stávající znalosti.

## <a name="structure-and-concepts"></a>Struktura a koncepce

Následující tabulka porovnává koncepty a datové struktury mezi protokoly Splunk a Azure Monitor.

 | Koncept  | Splunk | Azure Monitor |  Poznámka
 | --- | --- | --- | ---
 | Jednotka nasazení  | cluster |  cluster |  Azure Monitor umožňuje libovolné dotazy napříč clustery. Splunk nemá. |
 | Datové mezipaměti |  Kbelíky  |  Zásady ukládání do mezipaměti a uchovávání informací |  Řídí úroveň období a ukládání dat do mezipaměti. Toto nastavení má přímý vliv na výkon dotazů a náklady na nasazení. |
 | Logické rozdělení dat  |  index  |  database  |  Umožňuje logické oddělení dat. Obě implementace umožňují sjednocení a připojení napříč těmito oddíly. |
 | Metadata strukturovaných událostí | Není dostupné. | tabulka |  Splunk nemá koncept vystavený vyhledávacímu jazyku metadat událostí. Protokoly Azure Monitor má koncept tabulky, která má sloupce. Každá instance události je mapována na řádek. |
 | Datový záznam | event | Řádku |  Terminologie se mění pouze. |
 | Atribut datového záznamu | pole |  sloupec |  Ve službě Azure Monitor je to předdefinováno jako součást struktury tabulky. V Splunk, každá událost má svou vlastní sadu polí. |
 | Typy | Datatype |  Datatype |  Datové typy Azure Monitor jsou více explicitní, protože jsou nastaveny na sloupce. Oba mají schopnost dynamicky pracovat s datovými typy a zhruba ekvivalentní sadu datových typů, včetně podpory JSON. |
 | Dotaz a vyhledávání  | search | query |  Koncepty jsou v podstatě stejné mezi Azure Monitor a Splunk. |
 | Čas připožití události | Systémový čas | ingestion_time() |  V Splunk každá událost získá časové razítko systému času, který byla indexována událost. Ve službě Azure Monitor můžete definovat zásadu nazvanou ingestion_time, která zveřejňuje systémový sloupec, na který lze odkazovat prostřednictvím funkce ingestion_time(). |

## <a name="functions"></a>Funkce

Následující tabulka určuje funkce v Azure Monitoru, které jsou ekvivalentní funkcím Splunk.

|Splunk | Azure Monitor |Poznámka
|---|---|---
|strcat | strcat()| (1) |
|split  | split() | (1) |
|if     | iff()   | (1) |
|točíslo | todouble()<br>tolong()<br>toint() | (1) |
|Horní<br>Nižší |toupper()<br>tolower()|(1) |
| Nahradit | Replace() | (1)<br> Všimněte si `replace()` také, že zatímco trvá tři parametry v obou produktech, parametry se liší. |
| podřízený | podřetězec() | (1)<br>Všimněte si také, že Splunk používá jednozaložené indexy. Azure Monitor zaznamenává indexy s nulovým základě. |
| Tolower |  tolower() | (1) |
| Toupper | toupper() | (1) |
| match | odpovídá regulárnímu výrazu |  (2)  |
| Regex | odpovídá regulárnímu výrazu | V Splunk, `regex` je operátor. Ve službě Azure Monitor je to relační operátor. |
| vyhledávací shoda | == | V Splunk, `searchmatch` umožňuje hledání přesného řetězce.
| Náhodné | rand()<br>rand(n) | Funkce Splunk vrátí číslo od nuly do 2<sup>31</sup>-1. Azure Monitor' vrátí číslo mezi 0.0 a 1.0 nebo pokud je k dispozici parametr mezi 0 a n-1.
| nyní | now() | (1)
| relative_time | totimespan() | (1)<br>V Azure Monitor ekvivalent Splunk relative_time (datetimeVal, offsetVal) je datetimeVal + totimespan(offsetVal).<br>Například <code>search &#124; eval n=relative_time(now(), "-1d@d")</code> se <code>...  &#124; extend myTime = now() - totimespan("1d")</code>změní na .

(1) V Splunk, funkce je `eval` vyvolána s operátorem. Ve službě Azure Monitor se `extend` používá `project`jako součást nebo .<br>(2) V Splunk, funkce je `eval` vyvolána s operátorem. Ve službě Azure Monitor jej `where` lze použít s operátorem.


## <a name="operators"></a>Operátory

V následujících částech jsou uvedeny příklady použití různých operátorů mezi Splunk a Azure Monitor.

> [!NOTE]
> Pro účely následujícího příkladu _se pravidlo_ pole Splunk mapuje na tabulku ve službě Azure Monitor a výchozí časové razítko Splunk se mapuje na sloupec _Ingestion_time()._

### <a name="search"></a>Search
V splunk, můžete vynechat `search` klíčové slovo a zadat nekótovaný řetězec. V Azure Monitor musíte spustit `find`každý dotaz s , nekotovaný řetězec je název sloupce a vyhledávací hodnota musí být řetězec v uvozovkách. 

| |  | |
|:---|:---|:---|
| Splunk | **Hledat** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Azure Monitor | **find** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |
| | |

### <a name="filter"></a>Filtr
Dotazy protokolu Azure Monitor spustit z tabulkové sady výsledků, kde filtr. V Splunk filtrování je výchozí operace na aktuální index. Můžete také `where` použít operátor v Splunk, ale to se nedoporučuje.

| |  | |
|:---|:---|:---|
| Splunk | **Hledat** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Azure Monitor | **where** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |
| | |


### <a name="getting-n-eventsrows-for-inspection"></a>Získání n událostí/řádků pro kontrolu 
Dotazy protokolu Azure Monitor `take` také podporují `limit`jako alias pro . V Splunk, pokud jsou `head` výsledky seřazeny, vrátí první n výsledky. V Azure Monitor limit není objednané, ale vrátí první n řádky, které jsou nalezeny.

| |  | |
|:---|:---|:---|
| Splunk | **Hlavy** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Azure Monitor | **Limit** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |
| | |



### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Získání prvních n událostí/řádků uspořádaných podle pole/sloupce
Pro dolní výsledky, v Splunk používáte `tail`. V Azure Monitor můžete určit `asc`směr řazení s .

| |  | |
|:---|:---|:---|
| Splunk | **Hlavy** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Azure Monitor | **Top** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |
| | |




### <a name="extending-the-result-set-with-new-fieldscolumns"></a>Rozšíření sady výsledků o nová pole/sloupce
Splunk má `eval` také funkci, která nemá být `eval` srovnatelná s operátorem. `eval` Operátor v Splunk a `extend` operátor ve službě Azure Monitor podporují pouze skalární funkce a aritmetické operátory.

| |  | |
|:---|:---|:---|
| Splunk | **Eval** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Azure Monitor | **Rozšířit** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |
| | |


### <a name="rename"></a>Přejmenovat 
Azure Monitor `project-rename` používá operátor přejmenovat pole. `project-rename`umožňuje dotazu využít všechny indexy předem sestavené pro pole. Splunk má `rename` operátora, který má udělat totéž.

| |  | |
|:---|:---|:---|
| Splunk | **Přejmenovat** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Azure Monitor | **přejmenování projektu** | <code>Office_Hub_OHubBGTaskError<br>&#124; project-rename exception = Date_Exception</code> |
| | |




### <a name="format-resultsprojection"></a>Formátování výsledků/projekce
Zdá se, že Splunk nemá `project-away`operátor podobný . Pomocí ui můžete odfiltrovat pole.

| |  | |
|:---|:---|:---|
| Splunk | **Tabulka** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Azure Monitor | **Projektu**<br>**projekt-pryč** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |
| | |



### <a name="aggregation"></a>Agregace
Podívejte se na [agregace v dotazech protokolu Azure Monitor](aggregations.md) pro různé funkce agregace.

| |  | |
|:---|:---|:---|
| Splunk | **Statistiky** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Azure Monitor | **Sumarizovat** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |
| | |



### <a name="join"></a>Spojit
Join in Splunk má významná omezení. Poddotaz má limit 10000 výsledků (nastavených v konfiguračním souboru nasazení) a omezený počet příchutí spojení.

| |  | |
|:---|:---|:---|
| Splunk | **Připojit** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias \| join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| Azure Monitor | **Připojit** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |
| | |



### <a name="sort"></a>Seřadit
Chcete-li třídit ve vzestupném pořadí, musíte `reverse` použít operátor. Azure Monitor také podporuje definování, kam umístit null, na začátku nebo na konci.

| |  | |
|:---|:---|:---|
| Splunk | **Řazení** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Azure Monitor | **pořadí podle** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |
| | |



### <a name="multivalue-expand"></a>Rozšíření více hodnot
Toto je podobný operátor v Obou Splunk a Azure Monitor.

| |  | |
|:---|:---|:---|
| Splunk | **mvexpand** |  `mvexpand foo` |
| Azure Monitor | **mvexpand** | `mvexpand foo` |
| | |




### <a name="results-facets-interesting-fields"></a>Omezující aspekty výsledků, zajímavá pole
V Log Analytics na webu Azure portal je vystavena pouze první sloupec. Všechny sloupce jsou k dispozici prostřednictvím rozhraní API.

| |  | |
|:---|:---|:---|
| Splunk | **Pole** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Azure Monitor | **Aspekty** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |
| | |




### <a name="de-duplicate"></a>Odstranění duplikátu
Místo `summarize arg_min()` toho můžete obrátit pořadí, které záznam získá vybrán.

| |  | |
|:---|:---|:---|
| Splunk | **dedup** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Azure Monitor | **shrnout arg_max()** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |
| | |




## <a name="next-steps"></a>Další kroky

- Projděte si lekci [o dotazech protokolu zápisu v Azure Monitoru](get-started-queries.md).

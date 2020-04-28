---
title: Dotaz na Splunk do protokolu pro Azure Monitor | Microsoft Docs
description: Nápovědu pro uživatele, kteří znají Splunk ve výukových dotazech Azure Monitor protokolu
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: 6346055f1169bfa533d5dbfe441ecf27fb0d78a7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75397744"
---
# <a name="splunk-to-azure-monitor-log-query"></a>Dotaz Splunk na Azure Monitor protokolu

Tento článek je určený pro pomoc uživatelům, kteří jsou obeznámeni s Splunk, a Naučte se, jak Kusto dotazovací jazyk pro zápis dotazů protokolu v Azure Monitor. Mezi těmito dvěma porovnávacími se provedou přímá porovnání s cílem pochopit klíčové rozdíly a také podobnosti, kde můžete využít stávající poznatky.

## <a name="structure-and-concepts"></a>Struktura a koncepty

Následující tabulka porovnává koncepty a datové struktury mezi protokoly Splunk a Azure Monitor.

 | Koncept  | Splunk | Azure Monitor |  Poznámka
 | --- | --- | --- | ---
 | Jednotka nasazení  | cluster |  cluster |  Azure Monitor umožňuje libovolné dotazy mezi clustery. Splunk není. |
 | Mezipaměti dat |  intervalů  |  Ukládání do mezipaměti a zásady uchovávání informací |  Řídí období a úroveň ukládání dat do mezipaměti. Toto nastavení má přímý vliv na výkon dotazů a nákladů na nasazení. |
 | Logický oddíl dat  |  index  |  database  |  Umožňuje logické oddělení dat. Obě implementace umožňují sjednocení a spojování mezi těmito oddíly. |
 | Metadata strukturovaných událostí | – | tabulka |  Splunk nemá k dispozici koncept hledaného jazyka metadat události. Protokoly Azure Monitor mají koncept tabulky, která obsahuje sloupce. Každá instance události je namapována na řádek. |
 | Záznam dat | event | řadě |  Pouze změny terminologie. |
 | Atribut záznamu dat | pole |  sloupec |  V Azure Monitor je tato předdefinovaná jako součást struktury tabulky. V Splunk každá událost má svou vlastní sadu polí. |
 | Typy | programátor |  programátor |  Azure Monitor DataTypes jsou lépe explicitní, jak jsou nastaveny na sloupcích. Obě mají schopnost pracovat dynamicky s datovými typy a přibližně ekvivalentní sadou datových typů, včetně podpory JSON. |
 | Dotazování a hledání  | search | query |  Koncepty jsou v podstatě stejné mezi Azure Monitor i Splunk. |
 | Čas příjmu události | Systémový čas | ingestion_time() |  V Splunk každá událost získá systémové časové razítko času, kdy byla událost indexována. V Azure Monitor můžete definovat zásadu nazvanou ingestion_time, která zveřejňuje systémový sloupec, na který se dá odkazovat pomocí funkce ingestion_time (). |

## <a name="functions"></a>Functions

Následující tabulka uvádí funkce v Azure Monitor, které jsou ekvivalentní funkcím Splunk.

|Splunk | Azure Monitor |Poznámka
|---|---|---
|strcat | strcat()| první |
|split  | split() | první |
|if     | Pokud ()   | první |
|tonumber | todouble()<br>tolong()<br>toint() | první |
|umístit<br>malým |toupper()<br>tolower()|první |
| náhrady | replace() | první<br> Všimněte si také, `replace()` že při přebírání obou parametrů v obou produktech se parametry liší. |
| substr – | substring() | první<br>Všimněte si také, že Splunk používá indexy založené na jednom. Azure Monitor poznámky založené na nule. |
| ToLower |  tolower() | první |
| ToUpper | toupper() | první |
| match | odpovídá regulárnímu výrazu |  (2)  |
| regulární | odpovídá regulárnímu výrazu | V Splunk `regex` je operátor. V Azure Monitor se jedná o relační operátor. |
| searchmatch | == | V Splunk `searchmatch` umožňuje vyhledávání přesný řetězec.
| vybraných | rand()<br>Rand (n) | Funkce Splunk vrací číslo od nuly do 2<sup>31</sup>-1. Azure Monitor ' vrátí číslo mezi 0,0 a 1,0, nebo pokud byl zadán parametr, mezi 0 a n-1.
| nyní | now() | první
| relative_time | totimespan() | první<br>V Azure Monitor ekvivalent relative_time (datetimeVal, offsetVal) z Splunk je datetimeVal + ToTimeSpan (offsetVal).<br>Například <code>search &#124; eval n=relative_time(now(), "-1d@d")</code> se bude <code>...  &#124; extend myTime = now() - totimespan("1d")</code>jednat o.

(1) v Splunk je funkce vyvolána s `eval` operátorem. V Azure Monitor slouží jako součást `extend` nebo. `project`<br>(2) v Splunk je funkce vyvolána s `eval` operátorem. V Azure Monitor lze použít s `where` operátorem.


## <a name="operators"></a>Operátory

V následujících oddílech jsou uvedeny příklady použití různých operátorů mezi Splunk a Azure Monitor.

> [!NOTE]
> Pro účely následujícího příkladu se _pravidlo_ pole Splunk mapuje na tabulku v Azure monitor a výchozí časové razítko Splunk se mapuje na sloupec log Analytics _ingestion_time ()_ .

### <a name="search"></a>Search
V Splunk můžete `search` klíčové slovo vynechat a zadat řetězec, který není v uvozovkách. V Azure Monitor musíte spustit každý dotaz s `find`, řetězec bez uvozovek je název sloupce a hodnota vyhledávání musí být řetězec v uvozovkách. 

| |  | |
|:---|:---|:---|
| Splunk | **nápovědě** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Azure Monitor | **find** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |
| | |

### <a name="filter"></a>Filtr
Azure Monitor dotazy protokolu začínají z tabulkové sady výsledků, kde filtr. V Splunk je filtrování výchozí operací na aktuálním indexu. V Splunk můžete také `where` použít operátor, ale nedoporučuje se.

| |  | |
|:---|:---|:---|
| Splunk | **nápovědě** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Azure Monitor | **,** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |
| | |


### <a name="getting-n-eventsrows-for-inspection"></a>Získání n událostí/řádků pro kontrolu 
Azure Monitor dotazy protokolu také podporují `take` jako alias pro `limit`. V Splunk se při objednání `head` výsledků vrátí prvních n výsledků. V Azure Monitor limit není uspořádaný, ale vrací prvních n řádků, které jsou nalezeny.

| |  | |
|:---|:---|:---|
| Splunk | **záhlaví** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Azure Monitor | **počtu** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |
| | |



### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Získání prvních n událostí/řádků seřazených podle pole nebo sloupce
V případě dolních výsledků ve Splunk, `tail`které používáte. V Azure Monitor můžete určit směr řazení pomocí `asc`.

| |  | |
|:---|:---|:---|
| Splunk | **záhlaví** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Azure Monitor | **vrchol** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |
| | |




### <a name="extending-the-result-set-with-new-fieldscolumns"></a>Rozšíření sady výsledků s novými poli/sloupci
Splunk má také `eval` funkci, kterou není možné porovnat s `eval` operátorem. `eval` Operátor v Splunk i `extend` operátor v Azure monitor podporují pouze skalární funkce a aritmetické operátory.

| |  | |
|:---|:---|:---|
| Splunk | **platnost** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Azure Monitor | **zvětšení** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |
| | |


### <a name="rename"></a>přejmenování 
Azure Monitor používá `project-rename` operátor k přejmenování pole. `project-rename`umožňuje dotazu využít všechny indexy předem připravené pro pole. Splunk má `rename` operátor ke stejnému.

| |  | |
|:---|:---|:---|
| Splunk | **Změňte** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Azure Monitor | **projekt – přejmenování** | <code>Office_Hub_OHubBGTaskError<br>&#124; project-rename exception = Date_Exception</code> |
| | |




### <a name="format-resultsprojection"></a>Formátování výsledků/projekce
Splunk zřejmě nemá operátor podobný `project-away`. Pomocí uživatelského rozhraní můžete vyfiltrovat pole pryč.

| |  | |
|:---|:---|:---|
| Splunk | **stolní** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Azure Monitor | **projektem**<br>**projekt – pryč** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |
| | |



### <a name="aggregation"></a>Agregace
Podívejte se na [agregace v Azure monitor dotazy protokolu](aggregations.md) pro různé agregační funkce.

| |  | |
|:---|:---|:---|
| Splunk | **statistické** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Azure Monitor | **Souhrn** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |
| | |



### <a name="join"></a>Spojit
Spojení v Splunk má významná omezení. Poddotaz má omezení 10000 výsledků (nastaveno v konfiguračním souboru nasazení) a existuje omezený počet charakterů spojení.

| |  | |
|:---|:---|:---|
| Splunk | **zúčastnit** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias \| join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| Azure Monitor | **zúčastnit** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |
| | |



### <a name="sort"></a>Seřadit
Chcete-li v Splunk seřadit ve vzestupném pořadí, je nutné `reverse` použít operátor. Azure Monitor také podporuje definování umístění, kam mají být vloženy hodnoty null, na začátku nebo na konci.

| |  | |
|:---|:---|:---|
| Splunk | **druhu** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Azure Monitor | **Řadit podle** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |
| | |



### <a name="multivalue-expand"></a>Roztáhnout rozbalení
Toto je podobný operátor v Splunk i Azure Monitor.

| |  | |
|:---|:---|:---|
| Splunk | **mvexpand** |  `mvexpand foo` |
| Azure Monitor | **mvexpand** | `mvexpand foo` |
| | |




### <a name="results-facets-interesting-fields"></a>Charakteristiky výsledků, zajímavá pole
V Log Analytics v Azure Portal se zveřejňuje jenom první sloupec. Všechny sloupce jsou k dispozici prostřednictvím rozhraní API.

| |  | |
|:---|:---|:---|
| Splunk | **pole** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Azure Monitor | **omezující vlastnosti** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |
| | |




### <a name="de-duplicate"></a>De-duplikovat
`summarize arg_min()` Místo toho můžete změnit pořadí, v jakém se má záznam vybrat.

| |  | |
|:---|:---|:---|
| Splunk | **poplach** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Azure Monitor | **shrnout arg_max ()** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |
| | |




## <a name="next-steps"></a>Další kroky

- Projděte si lekci o [zápisu dotazů protokolu v Azure monitor](get-started-queries.md).

---
title: Splunk dotazu protokolu Azure Monitor | Dokumentace Microsoftu
description: Nápověda pro uživatele, kteří znají Splunk studium dotazů na protokoly Azure monitoru.
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
ms.date: 08/21/2018
ms.author: bwren
ms.openlocfilehash: d6065420b9081333fc257501ace868f64220ca63
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005246"
---
# <a name="splunk-to-azure-monitor-log-query"></a>Splunk dotazu protokolu Azure Monitor

Tento článek je určený jako pomoc uživatelům, kteří znají Splunk další dotazovací jazyk Průzkumník dat psaní dotazů protokolu ve službě Azure Monitor. Přímé porovnání mezi dvěma pochopit klíčové rozdíly a podobnosti probíhají, kde můžete využít své stávající znalosti.

## <a name="structure-and-concepts"></a>Struktura a koncepty

Následující tabulka porovnává koncepty a datových struktur mezi protokoly Splunk a Azure Monitor.

 | Koncept  | Splunk | Azure Monitor |  Poznámka
 | --- | --- | --- | ---
 | Jednotka nasazení  | Cluster |  Cluster |  Azure Monitor umožňuje libovolné různé dotazy clusteru. Splunk je nepodporuje. |
 | Mezipaměti dat |  kontejnery  |  Zásady ukládání do mezipaměti a jejich uchovávání |  Určuje období a ukládání do mezipaměti úrovně pro data. Toto nastavení přímo ovlivňuje výkon dotazů a náklady na nasazení. |
 | Logický oddíl dat  |  index  |  databáze  |  Umožňuje logické rozdělení data. Obou implementacích povolit sjednocení a propojení mezi tyto oddíly. |
 | Strukturované událost metadat | neuvedeno | tabulka |  Splunk nemá koncept vystaveni vyhledávací jazyk metadat události. Protokoly Azure monitoru nemá koncept tabulku, která má sloupce. Každá instance události je namapována na řádek. |
 | Datový záznam | událost | řádek |  Terminologie pouze změny. |
 | Atribut záznam dat | Pole |  Sloupec |  Ve službě Azure Monitor je předdefinovaná jako součást struktura tabulky. Každá událost ve Splunku, má svou vlastní sadu polí. |
 | Typy | datový typ |  datový typ |  Azure Monitor datové typy jsou více explicitní, jako jsou nastaveny na sloupce. Oba se budou moct pracovat dynamicky datových typů a zhruba ekvivalentní sadu datových typů včetně podpory JSON. |
 | Dotazy a hledání  | hledat | query |  Koncepty jsou v podstatě stejné mezi Azure Monitor a Splunk. |
 | Čas ingestování události | Systémový čas | ingestion_time() |  Každé události ve Splunku, získá systému časové razítko času, které události se indexovat. Ve službě Azure Monitor můžete definovat zásadu ingestion_time, který zpřístupňuje systémový sloupec, který může být odkazováno pomocí funkce ingestion_time(). |

## <a name="functions"></a>Functions

Následující tabulka obsahuje funkce ve službě Azure Monitor, která jsou rovnocenná Splunk funkce.

|Splunk | Azure Monitor |Poznámka
|---|---|---
|strcat – | strcat()| (1) |
|split  | split() | (1) |
|if     | iff()   | (1) |
|tonumber | todouble()<br>tolong()<br>toint() | (1) |
|horní<br>Nižší |toupper()<br>tolower()|(1) |
| nahradit | replace() | (1)<br> Všimněte si také, že při `replace()` přijímá tři parametry v obou produktů, parametry jsou různé. |
| substr – | substring() | (1)<br>Všimněte si také, že používá Splunk založen na jedničce indexy. Azure Monitor poznámky indexy od nuly. |
| ToLower |  tolower() | (1) |
| ToUpper | toupper() | (1) |
| shoda | odpovídá regulárnímu |  (2)  |
| regex | odpovídá regulárnímu | Ve Splunku `regex` je operátor. Ve službě Azure Monitor je relační operátor. |
| searchmatch | == | Ve Splunku `searchmatch` umožňuje vyhledat přesný řetězec.
| náhodná | rand()<br>rand(n) | Splunk vaší funkce vrátí číslo od nuly do 2<sup>31</sup>-1. Azure Monitor "vrátí číslo v rozsahu od 0,0 do 1,0, nebo pokud parametr zadán, mezi 0 a n-1.
| teď | now() | (1)
| relative_time | ToTimeSpan() | (1)<br>Ve službě Azure Monitor je ekvivalent na Splunk relative_time (datetimeVal, offsetVal) datetimeVal + totimespan(offsetVal).<br>Například <code>search &#124; eval n=relative_time(now(), "-1d@d")</code> stane <code>...  &#124; extend myTime = now() - totimespan("1d")</code>.

(1) ve Splunku, funkce se vyvolala s `eval` operátor. Ve službě Azure Monitor se používá jako součást `extend` nebo `project`.<br>(2) ve Splunku, funkce se vyvolala s `eval` operátor. Ve službě Azure Monitor, lze použít s `where` operátor.


## <a name="operators"></a>Operátory

V následujících částech jsou uvedeny příklady použití různých operátorů mezi Splunk a Azure Monitor.

> [!NOTE]
> Pro účely následující příklad pole Splunk _pravidlo_ mapuje na tabulku ve službě Azure Monitor a na Splunk výchozí časové razítko map pro analýzu protokolů _ingestion_time()_ sloupce.

### <a name="search"></a>Search
Ve Splunku, můžete vynechat `search` – klíčové slovo a zadejte řetězec bez uvozovek. Ve službě Azure Monitor je nutné spustit každý dotaz s `find`, bez uvozovek řetězec je název sloupce a hodnota vyhledávání musí být řetězec v uvozovkách. 

| |  | |
|:---|:---|:---|
| Splunk | **search** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Azure Monitor | **find** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |
| | |

### <a name="filter"></a>Filtr
Azure Monitor log dotazy zahájeno v tabulkovém výsledku nastavit kde filtr. Filtrování ve Splunku, je výchozí operaci v aktuální index. Můžete také použít `where` operátor v Splunk, ale nedoporučuje.

| |  | |
|:---|:---|:---|
| Splunk | **search** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Azure Monitor | **kde** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |
| | |


### <a name="getting-n-eventsrows-for-inspection"></a>Získávání n události/řádků pro kontrolu 
Azure Monitor protokolu dotazuje také podporu `take` jako alias pro `limit`. Ve Splunku, pokud jsou řazeny výsledky `head` vrátí prvních n výsledků. Ve službě Azure Monitor omezení není řazení, ale vrátí prvních n řádků, které se nacházejí.

| |  | |
|:---|:---|:---|
| Splunk | **hlavní** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Azure Monitor | **Limit** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |
| | |



### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Získat prvních n události/řádků seřazené podle pole nebo sloupec
Dolní výsledků ve Splunku použijete `tail`. Ve službě Azure Monitor můžete určit pořadí směr s `asc`.

| |  | |
|:---|:---|:---|
| Splunk | **hlavní** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Azure Monitor | **nahoru** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |
| | |




### <a name="extending-the-result-set-with-new-fieldscolumns"></a>Rozšíření výsledku sadě pomocí nového pole nebo sloupce
Splunk má také `eval` funkce, která nemá být srovnatelné s `eval` operátor. Oba `eval` operátor ve Splunku a `extend` operátor ve službě Azure Monitor se podporují jenom skalární funkce a aritmetické operátory.

| |  | |
|:---|:---|:---|
| Splunk | **(V angličtině)** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Azure Monitor | **Rozšíření** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |
| | |


### <a name="rename"></a>Přejmenovat 
Azure Monitor se používá stejný operátor přejmenovat a vytvořit nové pole. Splunk má dva samostatné operátory, `eval` a `rename`.

| |  | |
|:---|:---|:---|
| Splunk | **Přejmenovat** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Azure Monitor | **Rozšíření** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend exception = Date_Exception</code> |
| | |




### <a name="format-resultsprojection"></a>Formát výsledky/projekce
Splunk pravděpodobně nemají podobně jako operátor `project-away`. Uživatelské rozhraní můžete použít k filtrování tokeny pole.

| |  | |
|:---|:---|:---|
| Splunk | **Tabulka** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Azure Monitor | **project**<br>**project-away** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |
| | |



### <a name="aggregation"></a>Agregace
Najdete v článku [agregací ve službě Azure Monitor protokolu dotazy](aggregations.md) pro jinou agregační funkce.

| |  | |
|:---|:---|:---|
| Splunk | **Statistiky** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Azure Monitor | **shrnutí** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |
| | |



### <a name="join"></a>Spojit
Spojení ve Splunku má významné omezení. Poddotaz má limit 10000 výsledky (nastavte v konfiguračním souboru nasazení) a existuje digitálních omezený počet spojení.

| |  | |
|:---|:---|:---|
| Splunk | **join** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias | Připojte se k Client.Id maximum = 0 [vyhledat nejbližší Event.Rule="150310.0 = - 24h" Data.Hresult= 2147221040]</code> |
| Azure Monitor | **join** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |
| | |



### <a name="sort"></a>Seřadit
Ve Splunku seřadit v vzestupném pořadí, je nutné použít `reverse` operátor. Azure monitorovat také podporuje určení, kam chcete vložit hodnoty Null, na začátku nebo na konci.

| |  | |
|:---|:---|:---|
| Splunk | **Řazení** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Azure Monitor | **Řadit podle** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |
| | |



### <a name="multivalue-expand"></a>Rozbalte více hodnot
Toto je podobné operátor v Splunk a Azure Monitor.

| |  | |
|:---|:---|:---|
| Splunk | **mvexpand** |  `mvexpand foo` |
| Azure Monitor | **mvexpand** | `mvexpand foo` |
| | |




### <a name="results-facets-interesting-fields"></a>Omezující vlastnosti výsledky, zajímavé pole
V Log Analytics na portálu Azure portal je přístupný jenom v prvním sloupci. Všechny sloupce jsou k dispozici prostřednictvím rozhraní API.

| |  | |
|:---|:---|:---|
| Splunk | **Pole** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Azure Monitor | **omezující vlastnosti** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |
| | |




### <a name="de-duplicate"></a>Duplicitní zrušení
Můžete použít `summarize arg_min()` místo toho pořadí, které získá vybrali záznam.

| |  | |
|:---|:---|:---|
| Splunk | **Při odstraňování duplicitních dat** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Azure Monitor | **summarize arg_max()** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |
| | |




## <a name="next-steps"></a>Další postup

- Projděte si lekci [psaní dotazů protokolu ve službě Azure Monitor](get-started-queries.md).
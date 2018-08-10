---
title: Dotazy na typy a složení ve službě Azure Search | Dokumentace Microsoftu
description: Základní informace pro vytváření vyhledávacího dotazu ve službě Azure Search pomocí parametrů k filtrování, vyberte a řazení výsledků.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/03/2018
ms.openlocfilehash: 098718293cda1699fb07e09fa81af94a95bbdeca
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39715154"
---
# <a name="query-types-and-composition-in-azure-search"></a>Typy dotazů a skládání ve službě Azure Search

Sestavení dotazu ve službě Azure Search je úplná specifikace požadavku: odpovídat kritéria a parametry pro směrování provádění dotazů a tvarování odpovědi. Žádost o určuje pole, která chcete zahrnout, která pole se vraťte k řazení nebo filtrování a tak dále. Tento parametr zadán, spustí se dotaz všechna prohledatelná pole jako operace hledání textu v plném znění, vrací výsledek nemůže dostat skóre, nastavte v pořadí.

## <a name="introduction-by-example"></a>Úvod podle příkladu

Příklady jsou užitečné pro znázornění klíčových konceptů. Následující příklad formulovat pomocí [REST API služby Search dokumenty](https://docs.microsoft.com/rest/api/searchservice/search-documents), informuje žádost a odpověď. Ve službě Azure Search provádění dotazů je vždycky proti jeden index, se ověřují pomocí rozhraní api-key zadaný v požadavku. 

```
{  
    "queryType": "simple" 
    "search": "seattle townhouse* +\"lake\"", 
    "searchFields": "description, city",  
    "count": "true", 
    "select": "listingId, street, status, daysOnMarket, description",
    "top": "10",
    "orderby": "listingId"
 } 
```
Tento příklad ukazuje jako reprezentativní dotaz několika důležitými aspekty definice dotazu, z analyzátor vstupy tvarování sadu výsledků dotazu. Provádění dotazů je vždycky proti jeden index, se ověřují pomocí rozhraní api-key zadaný v požadavku. 

Chcete-li spustit tento dotaz, použijte [hledat explorer a index ukázku nemovitosti](search-get-started-portal.md). Vložte tento řetězec dotazu do panelu hledání v Průzkumníku: `search=seattle townhouse +lake&searchFields=description, city&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&orderby=listingId`

**Hledání rejstříku**

+ Analyzátor dotazů je volbou, nastavte prostřednictvím `queryType`. Většina vývojářů používá výchozí [jednoduchý analyzátor](search-query-simple-examples.md) pro fulltextové vyhledávání, ale [úplné Lucene](search-query-lucene-examples.md) analýza kódu se vyžaduje pro formuláře specializovaném dotazovacím například vyhledávání přibližných shod nebo regulární výrazy.
+ Nastavit kritéria shody na dokumenty v indexu pomocí `search` parametru. Hledání může být definován, stejně jako v `search=*`, ale více pravděpodobně obsahuje podmínky, fráze a operátory podobné co se zobrazuje v příkladu.
+ Rozsahem může být celý index, nebo konkrétní pole, jak je znázorněno v `searchFields`.

**Strukturování odpovědi**

Další parametry v tomto příkladu se týkají výsledků dotazu:

+ `count` je počet dokumentů odpovídajících dotazu.
+ `select` omezení pole vrácená v odpovědi.
+ `top` omezení řádků a dokumenty vrácená v odpovědi. Výchozí hodnota je 50; v příkladu, který omezuje na 10.
+ `orderby` Seřadí výsledky podle pole.

**Umožňuje operace, ať už atributy indexu**

Index návrhu a dotaz návrhu jsou úzce svázány ve službě Azure Search. Zatímco není tady zobrazené, potřebujete vědět před jeho zahájením kritickým bodem je, že *schéma indexu*, s atributy u každé pole určuje druh dotazu můžete vytvořit. Atributy indexu na pole určují povolené operace – zda je pole *prohledávatelné* v indexu, *retrievable* ve výsledcích *seřaditelné*, * Filterable*a tak dále. V tomto příkladu `"orderby": "listingId"` funguje jenom v případě listingId pole je označeno jako *seřaditelné* ve schématu indexu. Další informace o atributech indexu najdete v tématu [vytvořit Index rozhraní REST API služby](https://docs.microsoft.com/rest/api/searchservice/create-index).

Povolené operace na základě na pole jsou jen jedním způsobem, že definice indexu informuje provádění dotazu. Další možnosti povolené v indexu, patří:

+ [Synonyma](https://docs.microsoft.com/rest/api/searchservice/synonym-map-operations)
+ [Analýza textu (lingvistické)](https://docs.microsoft.com//rest/api/searchservice/language-support) a [vlastní analýzy](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)
+ [Modul pro návrhy konstrukce](https://docs.microsoft.com/rest/api/searchservice/suggesters) , povolte automatické dokončování a automatické návrhy
+ [Profily skórování](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) , přidat logiku pro řazení výsledků hledání

Výše uvedené funkce byly vykonávány při provádění dotazu, ale jsou obvykle implementovány ve vašem kódu, jako atributy pole, nikoli jako parametry v dotazu.

<a name="types-of-queries"></a>

## <a name="types-of-queries-search-and-filter"></a>Typy dotazů: vyhledávání a filtrování

Úvodní příklad parametr hledání byl identifikován jako prostředky, které vyhledáváním kritéria je předán do modulu. V praxi existují dva hlavní typy dotazů: `search` a `filter`. 

+ `search` dotazy prohledávání pro jeden nebo více výrazů ve všech *prohledávatelné* polích v indexu a funguje tak, jak by zvyklí u vyhledávacích webů, jako je Google nebo Bing. Příklady použití ÚVOD `search` parametru.

+ `filter` dotazy napříč všemi vyhodnotí logický výraz *filterable* polí v indexu. Na rozdíl od `search`, `filter` odpovídá přesný obsah pole, včetně rozlišování na pole řetězce dotazu.

Můžete použít vyhledávání a filtrování společně nebo samostatně. Filtr samostatné bez řetězce dotazu, je užitečná, pokud výraz filtru je možné k plnému určení dokumenty, které vás zajímají. Bez řetězce dotazu neexistuje žádná lexikální nebo jazyková analýza, žádné bodování a žádné řazení. Všimněte si, že se že hledaný řetězec je prázdný.

```
POST /indexes/nycjobs/docs/search?api-version=2017-11-11  
    {  
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "count": "true"
    }
```

Použijí společně, filtr je na celý index použije první, a pak provede vyhledávání na výsledcích filtru. Používání filtrů tak může být užitečné pro zlepšení výkonu dotazů zmenšením sady dokumentů, které musí dotaz vyhledávání zpracovat.

Syntaxe pro výrazy filtru je podmnožinou [jazyka filtrování OData](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search). Pro vyhledávací dotazy, můžete použít buď [zjednodušenou syntaxi](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) nebo [syntaxe dotazů Lucene](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search) kterých pojednáváme níže.


## <a name="choose-a-syntax-simple-or-full"></a>Zvolte syntaxe: jednoduché nebo úplné

Azure Search je umístěna nad Apache Lucene a vám dává na výběr mezi dvěma analyzátory dotazu pro zpracování dotazů typické a specializované. Se žádostí o hledání typické formulovat pomocí výchozí [jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search). Tato syntaxe podporuje mnoho běžných operátorů hledání včetně AND, OR, NOT, fráze, přípony a priority.

[Syntaxe dotazů Lucene](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_syntax), povolené při přidání `queryType=full` na žádost, poskytuje široce přijatých a expresivní jazyk dotazů vyvinutý jako součást [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Pomocí této syntaxe dotazů umožňuje specializované dotazy:

+ [Dotazy v rámci pole](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fields)
+ [vyhledávání přibližných shod](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fuzzy)
+ [vyhledávání blízkých výrazů](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_proximity)
+ [zvýšení skóre termínu](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_termboost)
+ [hledání regulárního výrazu](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_regex)
+ [hledání pomocí zástupných znaků](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_wildcard)

Logické operátory jsou většinou stejné v obou syntaxi pomocí dalších formátech v úplné Lucene:

+ [Logické operátory v jednoduché syntaxi](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search#operators-in-simple-search)
+ [Logické operátory v celé syntaxe Lucene](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_boolean)

## <a name="required-and-optional-elements"></a>Povinné a nepovinné prvky

Dotazy jsou vždy směrovat na jeden index. Nelze připojit k indexy nebo vytvořit vlastní nebo dočasné datové struktury jako cíl dotazu. 

Při odesílání vyhledávacích dotazů do služby Azure Search existuje několik parametrů, které lze zadat vedle vlastních slov zadaných do vyhledávacího pole vaší aplikace. Tyto parametry dotazu vám umožňují získat větší kontrolu nad [fulltextovým vyhledáváním](search-lucene-query-architecture.md).

Požadované elementy na žádost o dotazu jsou následující komponenty:

+ Služby koncového bodu a index kolekce dokumentů, vyjádřené jako adresu URL zde `https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`.
+ Verze rozhraní API REST (pouze), vyjádřené jako `api-version`
+ dotaz nebo správcem pro klíč rozhraní api, vyjádřené jako `api-key`
+ vyjádřený jako řetězec dotazu `search`, což může neurčené, pokud chcete provést prázdné vyhledávání. Můžete také odeslat pouze výraz filtru jako `$filter`.
+ `queryType`, jednoduché nebo úplné, který lze vynechat, pokud chcete použít výchozí jednoduchá syntaxe.

Všechny ostatní parametry hledání jsou volitelné.

## <a name="manage-search-results"></a>Správa výsledků hledání 

Výsledky dotazu jsou daná aplikace Streamovat jako dokumenty JSON do rozhraní REST API, i když používáte rozhraní API pro .NET, můžete je součástí serializace. Můžete je tvarovat výsledky po nastavení parametrů u dotazu, výběr konkrétního pole pro výsledek

Parametry v dotazu umožňuje strukturování daného výsledku nastavit následujícími způsoby:

+ Omezení nebo dávkování počet dokumentů ve výsledcích (50 ve výchozím nastavení)
+ Výběr polí pro zahrnutí do výsledků
+ Nastavení pořadí řazení
+ Přidávání položek zvýraznění dojde k přitažení pozornosti ke odpovídající podmínky v těle výsledky hledání

### <a name="tips-for-unexpected-results"></a>Tipy pro neočekávané výsledky

V některých případech neočekávané látku a není struktury výsledky. Pokud výsledky dotazu není co byste měli vidět, můžete vyzkoušet tyto změny dotazu a zjistěte, jestli zlepšit výsledky:

+ Změna `searchMode=any` (ve výchozím nastavení) `searchMode=all` tak, aby vyžadovala odpovídajících položek na všechna kritéria namísto některé z kritérií. To platí zejména když logické operátory jsou zahrnuty dotazu.

+ Techniky dotazu změňte, pokud text nebo provést lexikální analýzu, je nezbytné, ale typ dotazu vylučuje jazykové zpracování. Ve fulltextovém vyhledávání text nebo provést lexikální analýzu automaticky opravuje pravopisné chyby, tvary slov plural jednotném čísle a dokonce nestandardní příkazy nebo podstatná jména. U některých dotazů, jako přibližných shod nebo hledání pomocí zástupných znaků, analýzu textu není součástí dotazu parsování kanálu. Regulární výrazy byly použity pro některé scénáře, jako alternativní řešení. 

### <a name="paging-results"></a>Výsledky stránkování
Služba Azure Search umožňuje snadnou implementaci stránkování výsledků vyhledávání. Pomocí parametrů `top` a `skip` můžete plynule vydávat vyhledávací požadavky, které vám umožní získat úplnou sadu výsledků vyhledávání ve spravovatelných, seřazených podmnožinách, které umožňují dobré postupy v uživatelském rozhraní vyhledávání. Při získávání těchto menších podmnožin výsledků můžete také získat počet dokumentů v úplné sadě výsledků vyhledávání.

Další informace o stránkování výsledků vyhledávání naleznete v článku [Stránkování výsledků vyhledávání ve službě Azure Search](search-pagination-page-layout.md).

### <a name="ordering-results"></a>Řazení výsledků
Když přijímáte výsledky vyhledávacího dotazu, můžete požadovat, aby služba Azure Search vracela výsledky seřazené podle hodnot v určitém poli. Ve výchozím nastavení služba Azure Search řadí výsledky podle skóre vyhledávání každého dokumentu, které je odvozeno z [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Chcete-li, aby služba Azure Search vracela výsledky seřazené podle hodnoty místo skóre vyhledávání, můžete použít parametr vyhledávání `orderby`. Můžete zadat hodnotu parametru `orderby`, chcete-li zahrnout názvy polí a volání [funkce `geo.distance()`](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search) pro geoprostorové hodnoty. U každého výrazu může následovat `asc` k určení, že výsledky mají být ve vzestupném pořadí, nebo `desc` k určení, že výsledky mají být v sestupném pořadí. Ve výchozím nastavení se používá vzestupné pořadí.


### <a name="hit-highlighting"></a>Zvýrazňování položek
Ve službě Azure Search je zvýrazňování přesné části výsledků vyhledávání, která odpovídá vyhledávacímu dotazu, umožněno pomocí parametrů `highlight`, `highlightPreTag` a `highlightPostTag`. Můžete určit, u jakých *prohledávatelných* polí má být odpovídající text zvýrazněný, stejně tak můžete zadat značky řetězce, které se mají připojit k začátku a ke konci odpovídajícího text vráceného službou Azure Search.

## <a name="apis-and-tools-for-testing"></a>Rozhraní API a nástroje pro testování

V následující tabulce jsou uvedeny rozhraní API a detekce založené na nástroj pro zadávání dotazů.

| Metodologie | Popis |
|-------------|-------------|
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Klient, který můžete použít k dotazování indexu Azure Search.  <br/>[Další informace](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Hledání dokumentů (REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | V indexu pomocí parametrů dotazu další vstupní metody POST nebo GET.  |
| [Fiddler, Postman nebo jiný nástroj pro testování protokolu HTTP](search-fiddler.md) | Vysvětluje, jak vytvořit žádost o hlavička a tělo zprávy při odesílání dotazů do služby Azure Search.  |
| [Průzkumník služby Search na webu Azure portal](search-explorer.md) | Poskytuje panel hledání a možnosti pro výběr index a verze api-version. Výsledky jsou vráceny jako dokumenty JSON. <br/>[Další informace](search-get-started-portal.md#query-index) | 

## <a name="see-also"></a>Další informace najdete v tématech

+ [Jak funguje fulltextové vyhledávání ve službě Azure Search (dotaz analýza architektury](search-lucene-query-architecture.md)
+ [Průzkumník služby Search](search-explorer.md)
+ [Jak provádět dotazy v .NET](search-query-dotnet.md)
+ [Jak provádět dotazy v REST](search-query-rest-api.md)

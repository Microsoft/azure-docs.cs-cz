---
title: Sestavení a typy dotazů
titleSuffix: Azure Cognitive Search
description: Základy pro vytváření vyhledávacího dotazu v Azure Cognitive Search pomocí parametrů pro filtrování, výběr a řazení výsledků.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 902f3628235cc8a4524ddc4dd8a5327592fe47e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282819"
---
# <a name="query-types-and-composition-in-azure-cognitive-search"></a>Typy dotazů a složení v Azure Cognitive Search

V Azure Cognitive Search je dotaz úplnou specifikací operace s odezvou. Parametry v požadavku poskytují kritéria shody pro hledání dokumentů v indexu, která pole zahrnout nebo vyloučit, pokyny pro spuštění předány modulu a direktivy pro formování odpovědi. Nespecifikovaný`search=*`( ), dotaz se spustí proti všem prohledávatelným polím jako operace fulltextového vyhledávání a vrátí sadu výsledků bez hodnocení v libovolném pořadí.

Následující příklad je reprezentativní dotaz vytvořený v [rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents). Tento příklad cílí na [demo index hotelů](search-get-started-portal.md) a obsahuje běžné parametry.

```
{
    "queryType": "simple" 
    "search": "+New York +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

+ **`queryType`** nastaví analyzátor, což je [buď výchozí jednoduchý analyzátor dotazů](search-query-simple-examples.md) (optimální pro fulltextové vyhledávání), nebo úplný [analyzátor dotazů Lucene](search-query-lucene-examples.md) používaný pro pokročilé návrhy dotazů, jako jsou regulární výrazy, hledání bez kontaktní chod, přibližné a zástupné vyhledávání, abychom jmenovali jen několik.

+ **`search`** poskytuje kritéria shody, obvykle text, ale často doprovázený logickými operátory. Jednotlivé samostatné termíny jsou *termínové* dotazy. Vícedílné dotazy uzavřené v uvozovkách jsou klíčové dotazy *na fráze.* Hledání může být nedefinované, jako v **`search=*`**, ale spíše se skládá z termínů, frází a operátorů podobných tomu, co se zobrazí v příkladu.

+ **`searchFields`** omezuje provádění dotazů na konkrétní pole. Pro tento parametr je kandidátem jakékoli pole, které je přiřazeno jako *prohledávatelné* ve schématu indexu.

Odpovědi jsou také utvářeny parametry, které zahrnete do dotazu. V příkladu se sada výsledků skládá z **`select`** polí uvedených v příkazu. V příkazu $select lze použít pouze pole označená jako *retrievable.* Navíc jsou vráceny **`top`** pouze 10 přístupů **`count`** v tomto dotazu, zatímco vám řekne, kolik dokumentů odpovídá celkově, což může být více než co jsou vráceny. V tomto dotazu jsou řádky seřazeny podle hodnocení v sestupném pořadí.

V Azure Cognitive Search spuštění dotazu je vždy proti jeden index, ověřené pomocí klíče rozhraní api k dispozici v požadavku. V REST jsou oba k dispozici v hlavičkách požadavku.

### <a name="how-to-run-this-query"></a>Jak spustit tento dotaz

Chcete-li tento dotaz spustit, použijte [Průzkumník vyhledávání a ukázkový index hotelů](search-get-started-portal.md). 

Tento řetězec dotazu můžete vložit do vyhledávacího panelu průzkumníka:`search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

## <a name="how-query-operations-are-enabled-by-the-index"></a>Jak jsou operace dotazů povoleny indexem

Návrh indexu a návrh dotazů jsou pevně spojeny v Azure Cognitive Search. Základním faktem vědět předem je, že *schéma indexu*, s atributy na každé pole, určuje druh dotazu, který můžete sestavit. 

Atributy indexu v poli nastavují povolené operace – zda lze pole *prohledávat* v indexu, *lze je vyhledat* ve výsledcích, *seřaditelné*, *filtrovatelné*atd. V řetězci ukázkového dotazu funguje pouze proto, `"$orderby": "Rating"` že pole Hodnocení je ve schématu indexu označeno jako *seřaditelné.* 

![Definice indexu pro ukázku hotelu](./media/search-query-overview/hotel-sample-index-definition.png "Definice indexu pro ukázku hotelu")

Výše uvedený snímek obrazovky je částečný seznam atributů indexu pro ukázku hotelů. Můžete zobrazit celé schéma indexu na portálu. Další informace o atributech indexu naleznete v [tématu Create Index REST API](https://docs.microsoft.com/rest/api/searchservice/create-index).

> [!Note]
> Některé funkce dotazu je povolena index široký spíše než na základě pole. Mezi tyto možnosti patří: [mapy synonym](search-synonyms.md), [vlastní analyzátory](index-add-custom-analyzers.md), [konstrukce návrhu (pro automatické dokončování a navrhované dotazy)](index-add-suggesters.md), [logika hodnocení pro výsledky hodnocení](index-add-scoring-profiles.md).

## <a name="elements-of-a-query-request"></a>Prvky požadavku na dotaz

Dotazy jsou vždy směrovány na jeden index. Indexy nelze spojit ani vytvořit vlastní nebo dočasné datové struktury jako cíl dotazu. 

Požadované prvky v požadavku na dotaz zahrnují následující součásti:

+ Kolekce dokumentů koncového bodu služby a indexu, vyjádřená jako adresa URL obsahující pevné a uživatelem definované součásti:**`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`**(pouze REST) je nezbytné, protože více než jedna verze rozhraní API je k dispozici po celou dobu. 
+ **`api-key`**, buď dotaz nebo správce api-key, ověří požadavek na vaši službu.
+ **`queryType`**, jednoduché nebo úplné, které lze vynechat, pokud používáte předdefinovanou výchozí jednoduchou syntaxi.
+ **`search`** nebo **`filter`** poskytuje kritéria shody, která mohou být nespecifikována, pokud chcete provést prázdné vyhledávání. Oba typy dotazů jsou popsány z hlediska jednoduchéanalyzátor, ale i rozšířené dotazy vyžadují parametr hledání pro předávání složitých výrazů dotazu.

Všechny ostatní parametry vyhledávání jsou volitelné. Úplný seznam atributů naleznete v tématu [Vytvoření indexu (REST).](https://docs.microsoft.com/rest/api/searchservice/create-index) Podrobnější informace o tom, jak se parametry používají při zpracování, najdete v tématu [Jak fulltextové vyhledávání funguje v Azure Cognitive Search](search-lucene-query-architecture.md).

## <a name="choose-apis-and-tools"></a>Zvolte api a nástroje

V následující tabulce jsou uvedena api a přístupy založené na nástrojích pro odesílání dotazů.

| Metodologie | Popis |
|-------------|-------------|
| [Průzkumník hledání (portál)](search-explorer.md) | Poskytuje vyhledávací panel a možnosti pro výběry indexů a verzí rozhraní API. Výsledky jsou vráceny jako dokumenty JSON. Doporučeno pro průzkum, testování a ověřování. <br/>[Další informace](search-get-started-portal.md#query-index) | 
| [Pošťák nebo jiné rest nástroje](search-get-started-postman.md) | Nástroje pro webové testování jsou vynikající volbou pro formulování volání REST. Rozhraní REST API podporuje všechny možné operace v Azure Cognitive Search. V tomto článku se dozvíte, jak nastavit hlavičku a text požadavku HTTP pro odesílání požadavků do Azure Cognitive Search.  |
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Klient, který lze použít k dotazování indexu Azure Cognitive Search.  <br/>[Další informace](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Hledat dokumenty (ROZHRANÍ REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | GET nebo POST metody na indexu, pomocí parametrů dotazu pro další vstup.  |

## <a name="choose-a-parser-simple--full"></a>Vyberte si analyzátor: jednoduchý | Plné

Azure Cognitive Search je na vrcholu Apache Lucene a poskytuje možnost volby mezi dvěma analyzátory dotazů pro zpracování typické a specializované dotazy. Požadavky používající jednoduchý analyzátor jsou formulovány pomocí [jednoduché syntaxe dotazu](query-simple-syntax.md), která je vybrána jako výchozí pro jeho rychlost a účinnost v dotazech volného textu. Tato syntaxe podporuje řadu běžných vyhledávacích operátorů včetně operátorů AND, OR, NOT, frází, příponou a prioritou.

[Úplná syntaxe dotazu Lucene](query-Lucene-syntax.md#bkmk_syntax), povolená při přidání `queryType=full` do požadavku, zveřejňuje široce přijatý a expresivní dotazovací jazyk vyvinutý jako součást Apache [Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Úplná syntaxe rozšiřuje jednoduchou syntaxi. Všechny dotazy, které napíšete pro jednoduchou syntaxi, se spustí pod úplným analyzátorem Lucene. 

Následující příklady ilustrují bod: stejný dotaz, ale s různými queryType nastavení, výnos různé výsledky. V prvním dotazu `^3` `historic` after je považován za součást hledaného výrazu. Top-zařadil výsledek pro tento dotaz je "Markýz Plaza & Suites", který má *oceán* ve svém popisu

```
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

Stejný dotaz pomocí úplné lucene analyzátor `^3` interpretuje jako in-field termín booster. Přepínání analyzátorů změní pořadí, přičemž výsledky obsahují termín *historický* pohybující se na vrchol.

```
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Typy dotazů

Azure Cognitive Search podporuje širokou škálu typů dotazů. 

| Typ dotazu | Využití | Příklady a další informace |
|------------|--------|-------------------------------|
| Vyhledávání textu ve volné formě | Vyhledávací parametr a buď analyzátor| Fulltextové vyhledávání vyhledává jeden nebo více výrazů ve všech *prohledávatelných* polích v indexu a funguje tak, jak byste očekávali, že vyhledávač, jako je Google nebo Bing, bude fungovat. Příkladem v úvodu je fulltextové vyhledávání.<br/><br/>Fulltextové vyhledávání prochází textovou analýzou pomocí standardního analyzátoru Lucene (ve výchozím nastavení) pro malá písmena všech termínů, odstraňte stop slova jako "the". Výchozí nastavení můžete přepsat [pomocí neanglických analyzátorů](index-add-language-analyzers.md#language-analyzer-list) nebo [specializovaných analyzátorů bez jazykového jazyka,](index-add-custom-analyzers.md#AnalyzerTable) které upravují analýzu textu. Příkladem je [klíčové slovo,](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) které považuje celý obsah pole za jeden token. To je užitečné pro data, jako jsou PSČ, ID a některé názvy produktů. | 
| Filtrované hledání | [Výraz filtru OData](query-odata-filter-orderby-syntax.md) a buď analyzátor | Dotazy filtru vyhodnocují logický výraz ve všech *filtrovatelných* polích v indexu. Na rozdíl od hledání odpovídá dotaz filtru přesnému obsahu pole, včetně rozlišování velkých a malých písmen u řetězcových polí. Dalším rozdílem je, že dotazy filtru jsou vyjádřeny v syntaxi OData. <br/>[Příklad výrazu filtru](search-query-simple-examples.md#example-3-filter-queries) |
| Geografické vyhledávání | [Typ Edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) v poli, výraz filtru a analyzátor | Souřadnice uložené v poli, které má Edm.GeographyPoint se používají pro "najít v mém okolí" nebo mapové ovládací prvky vyhledávání. <br/>[Příklad geografického vyhledávání](search-query-simple-examples.md#example-5-geo-search)|
| Vyhledávání rozsahu | výraz filtru a jednoduchý analyzátor | V Azure Cognitive Search, rozsah dotazy jsou sestaveny pomocí parametru filtru. <br/>[Příklad filtru rozsahu](search-query-simple-examples.md#example-4-range-filters) | 
| [Vyhledávání v poli](query-lucene-syntax.md#bkmk_fields) | Vyhledávací parametr a úplný analyzátor | Vytvořte složený výraz dotazu, který cílí na jedno pole. <br/>[Příklad vyhledávání v poli](search-query-lucene-examples.md#example-2-fielded-search) |
| [přibližné hledání](query-lucene-syntax.md#bkmk_fuzzy) | Vyhledávací parametr a úplný analyzátor | Shoduje se s podmínkami, které mají podobnou konstrukci nebo pravopis. <br/>[Příklad přibližné hledání](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [bezkontaktní vyhledávání](query-lucene-syntax.md#bkmk_proximity) | Vyhledávací parametr a úplný analyzátor | Vyhledá termíny, které jsou blízko sebe v dokumentu. <br/>[Příklad vyhledávání bezkontaktní chod](search-query-lucene-examples.md#example-4-proximity-search) |
| [termín posílení](query-lucene-syntax.md#bkmk_termboost) | Vyhledávací parametr a úplný analyzátor | Zařadí dokument výše, pokud obsahuje posílený termín, vzhledem k ostatním, které jej neobsahují. <br/>[Příklad pro zvýšení termínu](search-query-lucene-examples.md#example-5-term-boosting) |
| [hledání regulárních výrazů](query-lucene-syntax.md#bkmk_regex) | Vyhledávací parametr a úplný analyzátor | Odpovídá na základě obsahu regulárního výrazu. <br/>[Příklad regulárního výrazu](search-query-lucene-examples.md#example-6-regex) |
|  [hledání se zástupnými symboly nebo předponami](query-lucene-syntax.md#bkmk_wildcard) | Vyhledávací parametr a úplný analyzátor | Odpovídá na základě předpony a`~`tildy`?`( ) nebo jednoho znaku ( ). <br/>[Příklad hledání zástupných symbolů](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>Správa výsledků hledání 

Výsledky dotazu jsou streamovány jako dokumenty JSON v rozhraní REST API, i když pokud používáte rozhraní API .NET, serializace je integrovaná. Výsledky můžete tvarovat nastavením parametrů v dotazu a výběrem konkrétních polí pro odpověď.

Parametry dotazu lze použít k strukturování sady výsledků následujícími způsoby:

+ Omezení nebo dávkování počtu dokumentů ve výsledcích (ve výchozím nastavení 50)
+ Výběr polí, která mají být zahrnuta do výsledků
+ Nastavení pořadí řazení
+ Přidání zvýraznění přístupů, které upozorňují na odpovídající termíny v těle výsledků vyhledávání

### <a name="tips-for-unexpected-results"></a>Tipy pro neočekávané výsledky

Příležitostně, látka a ne struktura výsledků jsou neočekávané. Pokud výsledky dotazu nejsou to, co očekáváte, můžete vyzkoušet tyto změny dotazu a zjistit, zda se výsledky zlepší:

+ Změna **`searchMode=any`** (výchozí) **`searchMode=all`** vyžadovat shody na všechna kritéria namísto některého z kritérií. To platí zejména v případě, že jsou zahrnuty logické operátory dotazu.

+ Změňte techniku dotazu, pokud je text nebo lexikální analýza nezbytná, ale typ dotazu vylučuje jazykové zpracování. Při fulltextovém vyhledávání automaticky opravuje automatické opravy pravopisných chyb, tvarů slov v jednotném čísle a dokonce i nepravidelných sloves nebo podstatná podstatná nastavení. U některých dotazů, jako je například přibližné nebo zástupné hledání, není analýza textu součástí kanálu analýzy dotazu. Pro některé scénáře byly jako alternativní řešení použity regulární výrazy. 

### <a name="paging-results"></a>Výsledky stránkování
Azure Cognitive Search usnadňuje implementaci stránkování výsledků hledání. Pomocí parametrů **`top`** **`skip`** a můžete plynule vydávat požadavky na vyhledávání, které umožňují přijímat celkovou sadu výsledků hledání v spravovatelných, uspořádaných podmnožinech, které snadno umožňují osvědčené postupy rozhraní pro vyhledávání. Při získávání těchto menších podmnožin výsledků můžete také získat počet dokumentů v úplné sadě výsledků vyhledávání.

Další informace o stránkování výsledků hledání v článku [Jak na stránce výsledky hledání v Azure Cognitive Search](search-pagination-page-layout.md).

### <a name="ordering-results"></a>Řazení výsledků
Při příjmu výsledků pro vyhledávací dotaz můžete požádat, aby Azure Cognitive Search slouží výsledky seřazené podle hodnot v určitém poli. Ve výchozím nastavení azure cognitive search seřadí výsledky hledání na základě pořadí každého dokumentu skóre hledání, který je odvozen z [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Pokud chcete, aby Azure Cognitive Search vrátil výsledky seřazené podle **`orderby`** jiné hodnoty, než je skóre vyhledávání, můžete použít parametr vyhledávání. Můžete určit hodnotu **`orderby`** parametru, která bude obsahovat názvy polí a volání [** `geo.distance()` funkce**](query-odata-filter-orderby-syntax.md) pro geoprostorové hodnoty. Každý výraz může `asc` být následován, což označuje, že **`desc`** výsledky jsou požadovány vzestupně, a označuje, že výsledky jsou požadovány v sestupném pořadí. Ve výchozím nastavení se používá vzestupné pořadí.


### <a name="hit-highlighting"></a>Zvýrazňování položek
V Azure Cognitive Search s důrazem na přesnou část výsledků hledání, **`highlight`** **`highlightPreTag`** které **`highlightPostTag`** odpovídají vyhledávacídotaz je snadné pomocí , a parametry. Můžete určit, která *prohledávatelná* pole by měla mít jejich odpovídající text zvýrazněný, stejně jako zadání přesné značky řetězců připojit k začátku a na konci odpovídající text, který vrátí Azure Cognitive Search.

## <a name="see-also"></a>Viz také

+ [Jak fulltextové vyhledávání funguje v Azure Cognitive Search (architektura analýzy dotazů)](search-lucene-query-architecture.md)
+ [Průzkumník hledání](search-explorer.md)
+ [Jak dotazovat v rozhraní .NET](search-query-dotnet.md)
+ [Jak dotazovat v REST](search-create-index-rest-api.md)

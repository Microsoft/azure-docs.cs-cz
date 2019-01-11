---
title: Typy dotazů a skládání – Azure Search
description: Základní informace pro vytváření vyhledávacího dotazu ve službě Azure Search pomocí parametrů k filtrování, vyberte a řazení výsledků.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/03/2018
ms.custom: seodec2018
ms.openlocfilehash: 9b682b9cd17c174363dcd04707a11075e30cc8e1
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214823"
---
# <a name="query-types-and-composition-in-azure-search"></a>Typy dotazů a skládání ve službě Azure Search

Dotaz ve službě Azure Search je úplná specifikace operaci round-trip. Parametry zadejte kritéria shody pro hledání dokumentů v indexu, provozním pokynům pro modul a direktivy pro strukturování odpovědi. Přesněji řečeno můžete určit, která pole jsou v oboru, jak hledat, která pole se vraťte k řazení nebo filtrování a tak dále. Tento parametr zadán, spustí se dotaz všechna prohledatelná pole jako operace hledání textu v plném znění, vrací výsledek nemůže dostat skóre, nastavte v pořadí.

## <a name="a-first-look-at-query-requests"></a>První pohled na požadavků na dotazy

Příklady jsou užitečné pro zavedení nových konceptů. Jak reprezentativní dotaz vytvořený v [rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents), tento příklad cíle [nemovitosti ukázku index](search-get-started-portal.md) , včetně společných parametrů.

```
{  
    "queryType": "simple" 
    "search": "seattle townhouse* +\"lake\"", 
    "searchFields": "description, city",  
    "count": "true", 
    "select": "listingId, street, status, daysOnMarket, description",
    "top": "10",
    "orderby": "daysOnMarket"
 } 
```

+ **`queryType`** Nastaví analyzátor, který může být ve službě Azure Search [výchozí jednoduchý analyzátor dotazů](search-query-simple-examples.md) (optimální pro fulltextové vyhledávání), nebo [kompletní analyzátor dotazů Lucene](search-query-lucene-examples.md) používá pro konstrukty na rozšířený dotaz jako regulární výrazy , vyhledávání blízkých výrazů, přibližných shod a hledání pomocí zástupných znaků, pár.

+ **`search`** poskytuje porovnávání kritéria, obvykle textu, ale často připojí logické operátory. Jediným samostatným podmínky jsou *termín* dotazy. Jsou uzavřené v uvozovkách vícedílný dotazy *klíčových frází* dotazy. Hledání může být definován, stejně jako v **`search=*`**, ale spíše se skládá z podmínek, fráze a operátory podobný co se zobrazuje v příkladu.

+ **`searchFields`** je volitelný, použita pro omezení dotazu provádění konkrétních polí.

Odpovědi jsou ve tvaru, parametry, které jsou v dotazu. V tomto příkladu sadu výsledků dotazu se skládá z polí uvedených v **`select`** příkazu. V tomto dotazu jsou vráceny pouze horní 10 přístupů, ale **`count`** zjistíte, kolik dokumenty odpovídá celkové. V tomto dotazu jsou řádky seřazeny podle daysOnMarket.

Ve službě Azure Search provádění dotazů je vždycky proti jeden index, se ověřují pomocí rozhraní api-key zadaný v požadavku. V klidu jak jsou k dispozici v záhlaví požadavku.

### <a name="how-to-run-this-query"></a>Spuštění tohoto dotazu

Ke spuštění tohoto dotazu, použijte [hledat explorer a index ukázku nemovitosti](search-get-started-portal.md). 

Vložte tento řetězec dotazu do panelu hledání v Průzkumníku: `search=seattle townhouse +lake&searchFields=description, city&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket`

### <a name="how-query-operations-are-enabled-by-the-index"></a>Jak jsou povoleny operace dotazu podle indexu

Index návrhu a dotaz návrhu jsou úzce svázány ve službě Azure Search. Základní fakt vědět před jeho zahájením je, že *schéma indexu*, s atributy u každé pole určuje druh dotazu můžete vytvořit. 

Atributy indexu na pole nastavit povolené operace -, zda je pole *prohledávatelné* v indexu, *retrievable* ve výsledcích *seřaditelné*,  *Filterable*a tak dále. V řetězci dotazu příklad `"$orderby": "daysOnMarket"` pouze funguje, protože daysOnMarket pole je označeno jako *seřaditelné* ve schématu indexu. 

![Pro ukázku nemovitosti definici indexu](./media/search-query-overview/realestate-sample-index-definition.png "definici pro ukázku nemovitosti indexu")

Výše uvedeném snímku obrazovky je částečný seznam atributů indexu pro ukázku nemovitostí. Celý index schéma můžete zobrazit na portálu. Další informace o atributech indexu najdete v tématu [vytvořit Index rozhraní REST API služby](https://docs.microsoft.com/rest/api/searchservice/create-index).

> [!Note]
> Některé funkce dotazu je povoleno celý index spíše než na základě na pole. Mezi tyto možnosti patří: [mapy synonym](https://docs.microsoft.com/rest/api/searchservice/synonym-map-operations), [vlastní analyzátory](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search), [konstruktory modulu pro návrhy (pro automatické dokončování a pro automatické návrhy)](https://docs.microsoft.com/rest/api/searchservice/suggesters), [vyhodnocování logiku pro řazení výsledků](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index).

## <a name="elements-of-a-query-request"></a>Elementy dotazu požadavku

Dotazy jsou vždy směrovat na jeden index. Nelze připojit k indexy nebo vytvořit vlastní nebo dočasné datové struktury jako cíl dotazu. 

Požadované elementy na žádost o dotazu jsou následující komponenty:

+ Služby koncového bodu a index kolekci documents, vyjádřené jako adresu URL obsahující pevné a uživatelem definované součásti: **`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`** (Pouze rozhraní REST) je potřeba, protože více než jednu verzi rozhraní API je k dispozici po celou dobu. 
+ **`api-key`**, dotazu nebo správce klíč api-key, ověří se tato žádost do služby.
+ **`queryType`**, jednoduché nebo úplné, který lze vynechat, pokud používáte integrované výchozí jednoduchá syntaxe.
+ **`search`** nebo **`filter`** poskytuje porovnávání kritéria, která může neurčené, pokud chcete provést prázdné vyhledávání. Oba typy dotazů jsou popisována v pojmech jednoduchý analyzátor, ale i pokročilé dotazy vyžadují parametr hledání pro předávání výrazy složitých dotazů.

Všechny ostatní parametry hledání jsou volitelné. Úplný seznam atributů najdete v tématu [vytvoření indexu (REST)](https://docs.microsoft.com/rest/api/searchservice/create-index). Bližší pohled na použití parametrů během zpracování, naleznete v tématu [jak funguje fulltextové vyhledávání ve službě Azure Search](search-lucene-query-architecture.md).

## <a name="choose-a-parser-simple--full"></a>Vyberte analyzátor: jednoduché | úplné

Azure Search je umístěna nad Apache Lucene a vám dává na výběr mezi dvěma analyzátory dotazu pro zpracování dotazů typické a specializované. Požadavky pomocí jednoduchý analyzátor jsou formulovat pomocí [jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search), vybrané jako výchozí pro rychlost a efektivitu v dotazech textu volného tvaru. Tato syntaxe podporuje mnoho běžných operátorů hledání včetně AND, OR, NOT, fráze, přípony a priority.

[Úplná syntaxe dotazů Lucene](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_syntax), povolené při přidání `queryType=full` na žádost, poskytuje široce přijatých a expresivní jazyk dotazů vyvinutý jako součást [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Úplná syntaxe rozšiřuje možnosti jednoduché syntaxi. Jakýkoli dotaz, který napíšete pro jednoduchá syntaxe běží pod úplné analyzátor Lucene. 

Tento bod ilustrovali následující příklady: stejný dotaz, ale s nastavením jiné queryType přinést různé výsledky. V první dotaz `^3` je považován za součást hledaný termín.

```
queryType=simple&search=mountain beach garden ranch^3&searchFields=description&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket
```

Stejný dotaz znovu pomocí úplné analyzátor Lucene interpretuje nárůst v poli na "ranch", což zvyšuje pořadí hledání výsledky obsahující tento konkrétní výraz.

```
queryType=full&search=mountain beach garden ranch^3&searchFields=description&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Typy dotazů

Služba Azure Search podporuje širokou škálu typů dotazů. 

| Typ dotazu | Využití | Další informace a příklady |
|------------|--------|-------------------------------|
| Hledání v textu volného tvaru | Parametr hledání a buď analyzátoru| Fulltextové vyhledávání vyhledá jeden nebo více výrazů ve všech *prohledávatelné* polích v indexu a funguje tak, jak by zvyklí u vyhledávacích webů, jako je Google nebo Bing. V příkladu v úvodu se fulltextové vyhledávání.<br/><br/>Fulltextové vyhledávání projde Analýza textu s využitím standardní analyzátor Lucene (ve výchozím nastavení) na malé všechny podmínky, odebrat nevýznamová slova jako "the". Můžete přepsat výchozí s [jiné než anglické jazykové analyzátory](https://docs.microsoft.com/rest/api/searchservice/language-support#analyzer-list) nebo [specializované analyzátory](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable) , které mění analýzu textu. Příkladem je [– klíčové slovo](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) jeden token, který považuje veškerý obsah pole. To je užitečné pro data, jako jsou PSČ, ID a některé názvy produktů. | 
| Filtrované vyhledávání | [Výraz filtru OData](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search) a buď analyzátoru | Filtrování vyhodnotí logický výraz napříč všemi *filterable* polí v indexu. Na rozdíl od vyhledávání odpovídá filtru dotazu přesný obsah pole, včetně rozlišování na pole řetězců. Další rozdíl je, že filtrovací dotazy jsou vyjádřeny syntaxe OData. <br/>[Příklad výrazu filtru](search-query-simple-examples.md#example-3-filter-queries) |
| Geografické vyhledávání | [Typu Edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) na pole, výraz filtru a buď analyzátoru | Souřadnice, které jsou uloženy v poli s Edm.GeographyPoint se používá pro "najít poblíž" nebo mapovou hledat ovládací prvky. <br/>[Příklad geografické vyhledávání](search-query-simple-examples.md#example-5-geo-search)|
| Oblast hledání | výraz filtru a jednoduchý analyzátor | Ve službě Azure Search se vytvářejí dotazy na rozsah, pomocí parametru filtru. <br/>[Příklad filtr rozsahu](search-query-simple-examples.md#example-4-range-filters) | 
| [Uvnitř pole filtrování](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fields) | Parametr hledání a úplné analyzátoru | Sestavte výraz složeného dotazu cílí na jedno pole. <br/>[Příklad uvnitř pole filtrování](search-query-lucene-examples.md#example-2-intra-field-filtering) |
| [vyhledávání přibližných shod](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fuzzy) | Parametr hledání a úplné analyzátoru | Odpovídá na podmínky mají podobné konstrukce nebo pravopis. <br/>[Příklad vyhledávání přibližných shod](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [vyhledávání blízkých výrazů](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_proximity) | Parametr hledání a úplné analyzátoru | Najde podmínky, které jsou blízko sebe v dokumentu. <br/>[Příklad vyhledávání blízkých výrazů](search-query-lucene-examples.md#example-4-proximity-search) |
| [zvýšení skóre termínu](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_termboost) | Parametr hledání a úplné analyzátoru | Určuje pořadí vyšší dokumentu obsahuje Posílený termín relativně k ostatním, které ji nemají. <br/>[Příklad zvýšení skóre termínu](search-query-lucene-examples.md#example-5-term-boosting) |
| [hledání regulárního výrazu](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_regex) | Parametr hledání a úplné analyzátoru | Na základě obsahu regulárního výrazu shody. <br/>[Příklad regulárního výrazu](search-query-lucene-examples.md#example-6-regex) |
|  [zástupný znak nebo předpona vyhledávání](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_wildcard) | Parametr hledání a úplné analyzátoru | Shody na základě předpony a tilda (`~`) nebo jeden znak (`?`). <br/>[Příklad prohledávání zástupných znaků](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>Správa výsledků hledání 

Výsledky dotazu jsou daná aplikace Streamovat jako dokumenty JSON do rozhraní REST API, i když používáte rozhraní API pro .NET, můžete je součástí serializace. Obrazce výsledky po nastavení parametrů u dotazu, výběr konkrétního pole pro odpověď.

Parametry v dotazu umožňuje strukturování daného výsledku nastavit následujícími způsoby:

+ Omezení nebo dávkování počet dokumentů ve výsledcích (50 ve výchozím nastavení)
+ Výběr polí pro zahrnutí do výsledků
+ Nastavení pořadí řazení
+ Přidávání položek zvýraznění dojde k přitažení pozornosti ke odpovídající podmínky v těle výsledky hledání

### <a name="tips-for-unexpected-results"></a>Tipy pro neočekávané výsledky

V některých případech neočekávané látku a není struktury výsledky. Pokud výsledky dotazu není co byste měli vidět, můžete vyzkoušet tyto změny dotazu a zjistěte, jestli zlepšit výsledky:

+ Změna **`searchMode=any`** (ve výchozím nastavení) **`searchMode=all`** tak, aby vyžadovala odpovídajících položek na všechna kritéria namísto některé z kritérií. To platí zejména když logické operátory jsou zahrnuty dotazu.

+ Techniky dotazu změňte, pokud text nebo provést lexikální analýzu, je nezbytné, ale typ dotazu vylučuje jazykové zpracování. Ve fulltextovém vyhledávání text nebo provést lexikální analýzu automaticky opravuje pravopisné chyby, tvary slov plural jednotném čísle a dokonce nestandardní příkazy nebo podstatná jména. U některých dotazů, jako přibližných shod nebo hledání pomocí zástupných znaků, analýzu textu není součástí dotazu parsování kanálu. Regulární výrazy byly použity pro některé scénáře, jako alternativní řešení. 

### <a name="paging-results"></a>Výsledky stránkování
Služba Azure Search umožňuje snadnou implementaci stránkování výsledků vyhledávání. S použitím **`top`** a **`skip`** parametry, můžete plynule vydávat vyhledávací požadavky, které vám umožní získat úplnou sadu výsledků vyhledávání ve spravovatelných, seřazených podmnožiny, který snadno povolte hledání dobré postupy v uživatelském rozhraní. Při získávání těchto menších podmnožin výsledků můžete také získat počet dokumentů v úplné sadě výsledků vyhledávání.

Další informace o stránkování výsledků vyhledávání naleznete v článku [Stránkování výsledků vyhledávání ve službě Azure Search](search-pagination-page-layout.md).

### <a name="ordering-results"></a>Řazení výsledků
Když přijímáte výsledky vyhledávacího dotazu, můžete požadovat, aby služba Azure Search vracela výsledky seřazené podle hodnot v určitém poli. Ve výchozím nastavení služba Azure Search řadí výsledky podle skóre vyhledávání každého dokumentu, které je odvozeno z [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Pokud chcete Azure Search vracela výsledky seřazené podle hodnoty místo skóre vyhledávání, můžete použít **`orderby`** parametr hledání. Můžete zadat hodnotu **`orderby`** parametr zahrnout názvy polí a volání [  **`geo.distance()` funkce** ](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search) pro geoprostorové hodnoty. Každý výraz může být následován `asc` k označení, že výsledky jsou požadovány ve vzestupném pořadí, a **`desc`** k označení, že výsledky jsou požadovány v sestupném pořadí. Ve výchozím nastavení se používá vzestupné pořadí.


### <a name="hit-highlighting"></a>Zvýrazňování položek
Ve službě Azure Search je zvýrazňování přesné části výsledků vyhledávání, která odpovídá vyhledávacímu dotazu je snadné pomocí **`highlight`**, **`highlightPreTag`**, a **`highlightPostTag`** parametry. Můžete určit, u jakých *prohledávatelných* polí má být odpovídající text zvýrazněný, stejně tak můžete zadat značky řetězce, které se mají připojit k začátku a ke konci odpovídajícího text vráceného službou Azure Search.

## <a name="apis-and-tools-for-testing"></a>Rozhraní API a nástroje pro testování

V následující tabulce jsou uvedeny rozhraní API a detekce založené na nástroj pro zadávání dotazů.

| Metodologie | Popis |
|-------------|-------------|
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Klient, který můžete použít k dotazování indexu Azure Search.  <br/>[Další informace](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Hledání dokumentů (REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | V indexu pomocí parametrů dotazu další vstupní metody POST nebo GET.  |
| [Fiddler, Postman nebo jiný nástroj pro testování protokolu HTTP](search-fiddler.md) | Vysvětluje, jak vytvořit žádost o hlavička a tělo zprávy při odesílání dotazů do služby Azure Search.  |
| [Průzkumník služby Search na webu Azure portal](search-explorer.md) | Poskytuje panel hledání a možnosti pro výběr index a verze api-version. Výsledky jsou vráceny jako dokumenty JSON. <br/>[Další informace](search-get-started-portal.md#query-index) | 

## <a name="see-also"></a>Další informace najdete v tématech

+ [Jak funguje fulltextové vyhledávání ve službě Azure Search (dotaz parsování architektura)](search-lucene-query-architecture.md)
+ [Průzkumník služby Search](search-explorer.md)
+ [Jak provádět dotazy v .NET](search-query-dotnet.md)
+ [Jak provádět dotazy v REST](search-query-rest-api.md)

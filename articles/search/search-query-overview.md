---
title: Typy a kompozice dotazů – Azure Search
description: Základní informace o vytváření vyhledávacích dotazů v Azure Search pomocí parametrů pro filtrování, výběr a řazení výsledků.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/13/2019
ms.custom: seodec2018
ms.openlocfilehash: c2d40865857bc3ea8367199ed29e0220a0e7c557
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882596"
---
# <a name="how-to-compose-a-query-in-azure-search"></a>Postup vytvoření dotazu v Azure Search

V Azure Search je dotaz kompletní specifikací operace Round-Trip. Parametry v žádosti poskytují kritéria shody pro hledání dokumentů v indexu, prováděcích pokynů pro modul a direktiv pro tvarování odpovědi. 

Požadavek na dotaz je bohatou konstrukcí, která určuje, která pole jsou v oboru, jak hledat, která pole se mají vrátit, ať už je seřadit nebo filtrovat, a tak dále. Neurčeno, dotaz se spustí pro všechna hledaná pole jako operace fulltextového vyhledávání a vrátí sadu výsledků bez skóre v libovolném pořadí.

## <a name="apis-and-tools-for-testing"></a>Rozhraní API a nástroje pro testování

Následující tabulka uvádí rozhraní API a postupy založené na nástrojích pro odesílání dotazů.

| Metodologie | Popis |
|-------------|-------------|
| [Průzkumník vyhledávání (portál)](search-explorer.md) | Poskytuje panel hledání a možnosti pro indexování a výběry verzí rozhraní API. Výsledky se vrátí jako dokumenty JSON. <br/>[Další informace](search-get-started-portal.md#query-index) | 
| [Post nebo Fiddler](search-get-started-postman.md) | Nástroje pro testování webu jsou vynikající volbou pro formulování volání REST. REST API podporuje všechny možné operace v Azure Search. V tomto článku se dozvíte, jak nastavit hlavičku a text požadavku HTTP pro odesílání požadavků do Azure Search.  |
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Klient, který se dá použít k dotazování indexu Azure Search.  <br/>[Další informace](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Hledat dokumenty (REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | Metoda GET nebo POST pro index s použitím parametrů dotazu pro další vstup.  |

## <a name="a-first-look-at-query-requests"></a>První pohled na požadavky na dotaz

Příklady jsou užitečné pro představení nových konceptů. V rámci reprezentativního dotazu vytvořeného v [REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)tento příklad cílí na [ukázkový index reálné nemovitosti](search-get-started-portal.md) a obsahuje společné parametry.

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

+ **`queryType`** Nastaví analyzátor, který v Azure Search může být [výchozím jednoduchým analyzátorem dotazů](search-query-simple-examples.md) (optimální pro fulltextové vyhledávání), nebo [kompletním analyzátorem dotazů Lucene](search-query-lucene-examples.md) použitým pro pokročilé konstrukce dotazů, jako jsou regulární výrazy, vyhledávání blízkosti, přibližné a zástupné znaky. Vyhledejte několik názvů.

+ **`search`** poskytuje kritéria shody, obvykle text, ale často spolu s logickými operátory. Jednou samostatnou pojmem jsou dotazy. Citace – uzavřené dotazy s více částmi jsou *klíčové fráze* dotazů. Hledání může být nedefinované, jako v **`search=*`** , ale pravděpodobněji se skládá z pojmů, frází a operátorů, které jsou podobné tomu, co se zobrazuje v příkladu.

+ **`searchFields`** je volitelná, slouží k omezení provádění dotazů na konkrétní pole.

Odpovědi jsou také ve tvaru podle parametrů, které zahrnete do dotazu. V příkladu se sada výsledků skládá z polí uvedených v **`select`** příkazu. V tomto dotazu se vrátí jenom 10 nejčastějších přístupů, ale **`count`** sdělí, kolik dokumentů se celkově shoduje. V tomto dotazu jsou řádky seřazeny podle daysOnMarket.

V Azure Search je provádění dotazů vždy proti jednomu indexu ověřeno pomocí klíče rozhraní API, který je v požadavku k dispozici. V části REST jsou v hlavičkách žádostí k dispozici obě.

### <a name="how-to-run-this-query"></a>Jak spustit tento dotaz

Chcete-li spustit tento dotaz, použijte [Průzkumníka aplikace Search a ukázkový index skutečné nemovitosti](search-get-started-portal.md). 

Tento řetězec dotazu můžete vložit do panelu hledání v Průzkumníkovi:`search=seattle townhouse +lake&searchFields=description, city&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket`

## <a name="how-query-operations-are-enabled-by-the-index"></a>Jak index povoluje operace dotazů

Návrh indexu a návrh dotazu jsou úzce spojeny v Azure Search. Základní fakt, jak předem zjistit, je, že *schéma indexu*s atributy v jednotlivých polích určuje druh dotazu, který můžete sestavit. 

Atributy indexu v poli nastavily povolené operace – určuje, jestli je pole možné *Prohledávat* v indexu, získat ve výsledcích, *seřaditelné*, *filtrovatelné*a tak dále. V příkladu řetězce dotazu funguje pouze `"$orderby": "daysOnMarket"` to, protože pole daysOnMarket je označeno jako *seřaditelné* ve schématu indexu. 

![Definice indexu pro ukázku reálné nemovitosti](./media/search-query-overview/realestate-sample-index-definition.png "Definice indexu pro ukázku reálné nemovitosti")

Výše uvedený snímek obrazovky je částečný seznam atributů indexu pro ukázku reálného majetku. Celé schéma indexu můžete zobrazit na portálu. Další informace o atributech indexu naleznete v tématu [Create index REST API](https://docs.microsoft.com/rest/api/searchservice/create-index).

> [!Note]
> Některé funkce dotazů jsou povoleny v rámci indexu, nikoli podle jednotlivých polí. Mezi tyto možnosti patří: [mapy synonym](search-synonyms.md), [vlastní analyzátory](index-add-custom-analyzers.md), [konstrukce návrhu (pro automatické dokončování a navrhované dotazy)](index-add-suggesters.md), [logika bodování pro výsledky řazení](index-add-scoring-profiles.md).

## <a name="elements-of-a-query-request"></a>Prvky žádosti o dotaz

Dotazy jsou vždy směrovány na jeden index. Nemůžete spojit indexy nebo vytvářet vlastní nebo dočasné datové struktury jako cíl dotazu. 

Požadované prvky pro požadavek na dotaz obsahují následující komponenty:

+ Kolekce koncových dokumentů a dokumentů v rámci služby vyjádřené jako adresa URL obsahující pevné a uživatelem definované komponenty: **`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`** (Pouze REST) je nutné, protože je vždy k dispozici více než jedna verze rozhraní API. 
+ **`api-key`** , buď dotaz nebo rozhraní API pro správu – klíč ověří požadavek vaší služby.
+ **`queryType`** , a to buď jednoduché, nebo úplné, což může být vynecháno, pokud používáte vestavěnou výchozí jednoduchou syntaxi.
+ **`search`** nebo **`filter`** poskytuje kritéria shody, která je možné neurčit, pokud chcete provést prázdné vyhledávání. Oba typy dotazů jsou popsány v souvislosti s jednoduchým analyzátorem, ale i pokročilé dotazy vyžadují pro předávání složitých výrazů dotazů parametr Search.

Všechny ostatní parametry hledání jsou volitelné. Úplný seznam atributů najdete v tématu [vytvoření indexu (REST)](https://docs.microsoft.com/rest/api/searchservice/create-index). Bližší informace o tom, jak se používají parametry během zpracování, najdete [v tématu Jak funguje fulltextové vyhledávání v Azure Search](search-lucene-query-architecture.md).

## <a name="choose-a-parser-simple--full"></a>Zvolit analyzátor: jednoduché | kompletní

Azure Search se nachází na Apache Lucene a dává vám možnost volby mezi dvěma analyzátory dotazů pro zpracování typických a specializovaných dotazů. Požadavky využívající jednoduchý analyzátor jsou formulovány pomocí [jednoduché syntaxe dotazu](query-simple-syntax.md), která je vybrána jako výchozí pro rychlost a efektivitu v textových dotazech bezplatného formuláře. Tato syntaxe podporuje řadu běžných operátorů hledání, včetně operátorů a, nebo, NOT, fráze, přípony a priority.

[Úplná syntaxe dotazů Lucene](query-Lucene-syntax.md#bkmk_syntax), která je povolená, `queryType=full` když přidáváte žádost, zveřejňuje široce přijatý a exprese dotazový jazyk vyvinutý jako součást [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Úplná syntaxe rozšiřuje jednoduchou syntaxi. Každý dotaz, který zapíšete pro jednoduchou syntaxi, se spustí s úplným analyzátorem Lucene. 

Následující příklady ilustrují bod: stejný dotaz, ale s jiným nastavením dotazu, poskytují různé výsledky. V prvním dotazu `^3` je považován za součást hledaného termínu.

```
queryType=simple&search=mountain beach garden ranch^3&searchFields=description&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket
```

Stejný dotaz, který používá úplný analyzátor Lucene, interpretuje zvyšování v rámci pole na "Ranch", což zvyšuje pořadí hledání výsledků, které obsahují konkrétní podmínky.

```
queryType=full&search=mountain beach garden ranch^3&searchFields=description&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Typy dotazů

Azure Search podporuje širokou škálu typů dotazů. 

| Typ dotazu | Použití | Příklady a další informace |
|------------|--------|-------------------------------|
| Hledání textu bezplatného formuláře | Vyhledávací parametr a buď analyzátor| Fulltextové vyhledávání vyhledává jeden nebo více výrazů ve všech *prohledávatelných* polích v indexu a funguje tak, jak byste očekávali, že vyhledávací stroj, jako je Google nebo Bing, funguje. Příkladem v úvodu je fulltextové vyhledávání.<br/><br/>Fulltextové vyhledávání odkazuje na analýzu textu pomocí standardního analyzátoru Lucene (ve výchozím nastavení), aby se snížila velikost písmen, jako je "a". Můžete přepsat výchozí pomocí analyzátorů, které [nejsou v angličtině](index-add-language-analyzers.md#language-analyzer-list) , nebo [specializované nezávislá analyzátory jazyka](index-add-custom-analyzers.md#AnalyzerTable) , které upraví analýzu textu. Příkladem je [klíčové slovo](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) , které zachází s celým obsahem pole jako s jedním tokenem. To je užitečné pro data, jako jsou kódy PSČ, ID a některé názvy produktů. | 
| Filtrované hledání | [Výraz filtru OData](query-odata-filter-orderby-syntax.md) a buď analyzátor | Filtrovat dotazy vyhodnotit logický výraz nad všemi filtrovanými poli v indexu. Na rozdíl od hledání dotaz filtru odpovídá přesnému obsahu pole, včetně rozlišování velkých a malých písmen v polích řetězců. Dalším rozdílem je, že dotazy filtru jsou vyjádřené v syntaxi OData. <br/>[Příklad výrazu filtru](search-query-simple-examples.md#example-3-filter-queries) |
| Geografické vyhledávání | [Typ EDM. GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) pro pole, výraz filtru a buď analyzátor | Souřadnice uložené v poli s EDM. GeographyPoint se používají pro ovládací prvky hledání v blízkosti nebo mapování na základě map. <br/>[Příklad geografického hledání](search-query-simple-examples.md#example-5-geo-search)|
| Hledání rozsahu | výraz filtru a jednoduchý analyzátor | V Azure Search jsou dotazy Range sestaveny pomocí parametru Filter. <br/>[Příklad filtru rozsahu](search-query-simple-examples.md#example-4-range-filters) | 
| [Hledání v poli](query-lucene-syntax.md#bkmk_fields) | Parametr Search a úplný analyzátor | Sestavení složeného výrazu dotazu zaměřeného na jedno pole. <br/>[Příklad hledání v poli](search-query-lucene-examples.md#example-2-fielded-search) |
| [hledání přibližných výsledků](query-lucene-syntax.md#bkmk_fuzzy) | Parametr Search a úplný analyzátor | Odpovídá podmínkám, které mají podobnou konstrukci nebo pravopis. <br/>[Příklad přibližného hledání](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [hledání blízkosti](query-lucene-syntax.md#bkmk_proximity) | Parametr Search a úplný analyzátor | Vyhledá v dokumentu skoro stejné výrazy. <br/>[Příklad hledání blízkosti](search-query-lucene-examples.md#example-4-proximity-search) |
| [zvyšování termínů](query-lucene-syntax.md#bkmk_termboost) | Parametr Search a úplný analyzátor | Rozhodne dokument větší, pokud obsahuje zvýšený termín vzhledem k ostatním, které ne. <br/>[Příklad zvyšování skóre termínu](search-query-lucene-examples.md#example-5-term-boosting) |
| [hledání regulárních výrazů](query-lucene-syntax.md#bkmk_regex) | Parametr Search a úplný analyzátor | Odpovídá na základě obsahu regulárního výrazu. <br/>[Příklad regulárního výrazu](search-query-lucene-examples.md#example-6-regex) |
|  [zástupné znaky nebo hledání předpon](query-lucene-syntax.md#bkmk_wildcard) | Parametr Search a úplný analyzátor | Odpovídá v závislosti na předponě a vlnovce (`~`) nebo jednom znaku (`?`). <br/>[Příklad hledání pomocí zástupných znaků](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>Správa výsledků hledání 

Výsledky dotazu jsou v REST API streamované jako dokumenty JSON, i když použijete rozhraní API .NET, ve kterém je zabudovaná serializace. Výsledky můžete tvarovat nastavením parametrů pro dotaz, a to tak, že vyberete konkrétní pole pro odpověď.

Parametry pro dotaz lze použít ke strukturování sady výsledků následujícími způsoby:

+ Omezení nebo dávkování počtu dokumentů ve výsledcích (standardně 50)
+ Výběr polí, která se mají zahrnout do výsledků
+ Nastavení pořadí řazení
+ Přidání zvýraznění přístupů k tomu, aby bylo možné upozornit na vyhovující výrazy v těle výsledků hledání

### <a name="tips-for-unexpected-results"></a>Tipy pro neočekávané výsledky

V některých případech se jedná o neočekávanou látku a nikoli strukturu výsledků. Pokud výsledky dotazu nečekají na to, co očekáváte, můžete vyzkoušet tyto úpravy dotazů, abyste viděli, jestli výsledky zlepšují:

+ Změňte **`searchMode=any`** (výchozí) na **`searchMode=all`** tak, aby vyžadovala shodu u všech kritérií místo některého z kritérií. To platí zejména při zahrnutí logických operátorů do dotazu.

+ Změňte způsob dotazování v případě, že je nutné zadat text nebo lexikální analýzu, ale typ dotazu vylučuje lingvistické zpracování. V fulltextovém vyhledávání, textu nebo lexikální analýze je automatických oprav pro chyby pravopisu, wordových formulářů v množném čísle a dokonce i na nepravidelných operacích a podstatných případech. Pro některé dotazy, jako je například přibližné nebo zástupné vyhledávání, není analýza textu součástí kanálu analýzy dotazů. V některých případech byly regulární výrazy použity jako alternativní řešení. 

### <a name="paging-results"></a>Výsledky stránkování
Služba Azure Search umožňuje snadnou implementaci stránkování výsledků vyhledávání. Pomocí **`top`** parametrů a **`skip`** můžete plynule vydávat žádosti o vyhledávání, které vám umožní získat celkovou sadu výsledků hledání v spravovatelných, seřazených podmnožinách, které umožňují správné postupy uživatelského rozhraní pro hledání. Při získávání těchto menších podmnožin výsledků můžete také získat počet dokumentů v úplné sadě výsledků vyhledávání.

Další informace o stránkování výsledků vyhledávání naleznete v článku [Stránkování výsledků vyhledávání ve službě Azure Search](search-pagination-page-layout.md).

### <a name="ordering-results"></a>Řazení výsledků
Když přijímáte výsledky vyhledávacího dotazu, můžete požadovat, aby služba Azure Search vracela výsledky seřazené podle hodnot v určitém poli. Ve výchozím nastavení služba Azure Search řadí výsledky podle skóre vyhledávání každého dokumentu, které je odvozeno z [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Pokud chcete, aby Azure Search vracet výsledky seřazené podle jiné hodnoty než skóre hledání, můžete použít **`orderby`** parametr Search. Můžete zadat hodnotu **`orderby`** parametru pro zahrnutí názvů polí a volání [ **`geo.distance()` funkce**](query-odata-filter-orderby-syntax.md) pro geoprostorové hodnoty. Každý výraz může následovat `asc` za účelem indikace, že výsledky jsou požadovány ve vzestupném pořadí a **`desc`** označují, že výsledky jsou požadovány v sestupném pořadí. Ve výchozím nastavení se používá vzestupné pořadí.


### <a name="hit-highlighting"></a>Zvýrazňování položek
V Azure Search zdůraznění přesné části výsledků hledání, které odpovídají hledanému dotazu, je snadné pomocí **`highlight`** parametrů, **`highlightPreTag`** a **`highlightPostTag`** . Můžete určit, u jakých *prohledávatelných* polí má být odpovídající text zvýrazněný, stejně tak můžete zadat značky řetězce, které se mají připojit k začátku a ke konci odpovídajícího text vráceného službou Azure Search.

## <a name="see-also"></a>Viz také:

+ [Jak funguje úplné hledání textu v Azure Search (architektura analýzy dotazů)](search-lucene-query-architecture.md)
+ [Průzkumník vyhledávání](search-explorer.md)
+ [Dotazování v .NET](search-query-dotnet.md)
+ [Dotazování v REST](search-create-index-rest-api.md)

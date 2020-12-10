---
title: Typy dotazů
titleSuffix: Azure Cognitive Search
description: Seznamte se s typy dotazů podporovaných v Kognitivní hledání, včetně volného textu, filtru, automatického dokončování a návrhů, geografického hledání, systémových dotazů a vyhledávání v dokumentech.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: d1ea2d0ba8ed5850e5d4cd9c06a0b016c4059ca7
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007853"
---
# <a name="query-types-in-azure-cognitive-search"></a>Typy dotazů v Azure Kognitivní hledání

V Azure Kognitivní hledání je dotaz kompletní specifikací operace Round-Trip s parametry, které řídí provádění dotazů, a parametry, které tvarující odpověď vrací.

## <a name="elements-of-a-request"></a>Prvky požadavku

V následujícím příkladu je reprezentativní dotaz vytvořený pomocí [dokumentů pro hledání REST API](/rest/api/searchservice/search-documents). Tento příklad cílí na [index ukázky hotelů](search-get-started-portal.md) a obsahuje společné parametry, které vám pomohou získat představu o tom, co dotaz vypadá.

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=[api-version]
{
    "queryType": "simple" 
    "search": "`New York` +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

Dotazy jsou vždy směrovány na kolekci dokumentů jednoho indexu. Nemůžete spojit indexy nebo vytvářet vlastní nebo dočasné datové struktury jako cíl dotazu.

+ **`queryType`** Nastaví analyzátor, což je buď [výchozí jednoduchý analyzátor dotazů](search-query-simple-examples.md) (optimální pro fulltextové vyhledávání), nebo [kompletní analyzátor dotazů Lucene](search-query-lucene-examples.md) , který se používá pro pokročilé konstrukce dotazů, jako jsou regulární výrazy, vyhledávání blízkosti, přibližná a zástupné vyhledávání, pro pojmenování několika.

+ **`search`** poskytuje kritéria shody, obvykle celé podmínky nebo fráze, ale často spolu s logickými operátory. Jednou samostatnou *pojmem jsou dotazy* . Citace – uzavřené dotazy s více částmi jsou dotazy na *fráze* . Hledání může být nedefinované, jako v **`search=*`** , ale bez kritérií, která by se měla shodovat, se sada výsledků skládá z libovolných vybraných dokumentů.

+ **`searchFields`** omezuje provádění dotazů na konkrétní pole. Každé pole, které je s atributy *prohledávatelné* ve schématu indexu, je kandidátem pro tento parametr.

Odpovědi jsou také ve tvaru podle parametrů, které zahrnete do dotazu:

+ **`select`** Určuje, která pole se mají vrátit v odpovědi. V příkazu SELECT lze použít pouze pole *, která jsou označena jako* načístelné v indexu.

+ **`top`** Vrátí zadaný počet nejlépe vyhovujících dokumentů. V tomto příkladu se vrátí jenom 10 přístupů. Pro stránku výsledků můžete použít horní a přeskočení (není zobrazeno).

+ **`count`** oznamuje, kolik dokumentů v celém indexu se shoduje se souhrnem, což může být víc, než se vrátí. 

+ **`orderby`** se používá, pokud chcete výsledky seřadit podle hodnoty, jako je například hodnocení nebo umístění. V opačném případě se ve výchozím nastavení použije hodnocení relevance k zařazení výsledků.

> [!Tip]
> Před zápisem kódu můžete pomocí nástrojů dotazů zjistit syntaxi a experimentovat s různými parametry. Nejrychlejší přístup je integrovaný nástroj portál, který je v [Průzkumníkovi vyhledávání](search-explorer.md).
>
> Pokud jste postupovali podle tohoto rychlého startu a [vytvořili ukázkový index hotelů](search-get-started-portal.md), můžete vložit tento řetězec dotazu do panelu hledání v Průzkumníkovi a spustit tak první dotaz: `search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

### <a name="how-field-attributes-in-an-index-determine-query-behaviors"></a>Jak atributy pole v indexu určují chování dotazů

Návrh indexu a návrh dotazu jsou úzce spojeny v Azure Kognitivní hledání. Základní fakt, jak předem zjistit, je, že *schéma indexu* s atributy v jednotlivých polích určuje druh dotazu, který můžete sestavit. 

Atributy indexu v poli nastavily povolené operace – určuje, jestli je pole možné *Prohledávat* v indexu, *získat ve výsledcích* , *seřaditelné*, *filtrovatelné* a tak dále. V příkladu řetězce dotazu `"$orderby": "Rating"` funguje pouze to, protože pole hodnocení je ve schématu indexu označeno jako *seřaditelné* . 

![Definice indexu pro ukázku hotelu](./media/search-query-overview/hotel-sample-index-definition.png "Definice indexu pro ukázku hotelu")

Výše uvedený snímek obrazovky je částečný seznam atributů indexu pro ukázku hotelů. Celé schéma indexu můžete zobrazit na portálu. Další informace o atributech indexu naleznete v tématu [Create index REST API](/rest/api/searchservice/create-index).

> [!Note]
> Některé funkce dotazů jsou povoleny v rámci indexu, nikoli podle jednotlivých polí. Mezi tyto možnosti patří: [mapy synonym](search-synonyms.md), [vlastní analyzátory](index-add-custom-analyzers.md), [konstrukce návrhu (pro automatické dokončování a navrhované dotazy)](index-add-suggesters.md), [logika bodování pro výsledky řazení](index-add-scoring-profiles.md).

## <a name="choose-a-parser-simple--full"></a>Zvolit analyzátor: jednoduché | kompletní

Azure Kognitivní hledání nabízí možnost volby mezi dvěma analyzátory dotazů pro zpracování typických a specializovaných dotazů. Požadavky využívající jednoduchý analyzátor jsou formulovány pomocí [jednoduché syntaxe dotazu](query-simple-syntax.md), která je vybrána jako výchozí pro rychlost a efektivitu v textových dotazech bezplatného formuláře. Tato syntaxe podporuje řadu běžných operátorů hledání, včetně operátorů a, nebo, NOT, fráze, přípony a priority.

[Úplná syntaxe dotazů Lucene](query-Lucene-syntax.md#bkmk_syntax), která je povolená, když přidáváte `queryType=full` žádost, zveřejňuje široce přijatý a exprese dotazový jazyk vyvinutý jako součást [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Úplná syntaxe rozšiřuje jednoduchou syntaxi. Každý dotaz, který zapíšete pro jednoduchou syntaxi, se spustí s úplným analyzátorem Lucene. 

Následující příklady ilustrují bod: stejný dotaz, ale s jiným nastavením dotazu, poskytují různé výsledky. V prvním dotazu `^3` je potom považován za `historic` součást hledaného termínu. Výsledek nejvyšší úrovně pro tento dotaz je "Marquis Plaza & sady", jejichž popis má *oceánu* .

```http
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

Stejný dotaz používající kompletní analyzátor Lucene se interpretuje `^3` jako rozbuška termínů v terénu. Přepínáním analyzátorů se mění pořadí s výsledky, které obsahují *historické* přesuny do horní části.

```http
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Typy dotazů

Azure Kognitivní hledání podporuje širokou škálu typů dotazů. 

| Typ dotazu | Využití | Příklady a další informace |
|------------|--------|-------------------------------|
| Hledání textu bezplatného formuláře | Vyhledávací parametr a buď analyzátor| Fulltextové vyhledávání vyhledává jeden nebo více výrazů ve všech *prohledávatelných* polích v indexu a funguje tak, jak byste očekávali, že vyhledávací stroj, jako je Google nebo Bing, funguje. Příkladem v úvodu je fulltextové vyhledávání.<br/><br/>Fulltextové vyhledávání: nastala lexikální analýza pomocí standardního analyzátoru Lucene (ve výchozím nastavení), aby se snížila velikost písmen, jako je "a". Můžete přepsat výchozí pomocí [analyzátorů](index-add-language-analyzers.md#language-analyzer-list) , které nejsou v angličtině, nebo [specializované nezávislá analyzátory jazyka](index-add-custom-analyzers.md#AnalyzerTable) , které upraví lexikální analýzu. Příkladem je [klíčové slovo](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) , které zachází s celým obsahem pole jako s jedním tokenem. To je užitečné pro data, jako jsou kódy PSČ, ID a některé názvy produktů. | 
| Filtrované hledání | [Výraz filtru OData](query-odata-filter-orderby-syntax.md) a buď analyzátor | Filtrovat dotazy vyhodnotit logický výraz nad všemi *filtrovanými* poli v indexu. Na rozdíl od hledání dotaz filtru odpovídá přesnému obsahu pole, včetně rozlišování velkých a malých písmen v polích řetězců. Dalším rozdílem je, že dotazy filtru jsou vyjádřené v syntaxi OData. <br/>[Příklad výrazu filtru](search-query-simple-examples.md#example-3-filter-queries) |
| Geografické vyhledávání | [Typ EDM. GeographyPoint](/rest/api/searchservice/supported-data-types) pro pole, výraz filtru a buď analyzátor | Souřadnice uložené v poli s EDM. GeographyPoint se používají pro ovládací prvky hledání v blízkosti nebo mapování na základě map. <br/>[Příklad geografického hledání](search-query-simple-examples.md#example-5-geo-search)|
| Hledání rozsahu | výraz filtru a jednoduchý analyzátor | V Azure Kognitivní hledání jsou dotazy Range sestaveny pomocí parametru Filter. <br/>[Příklad filtru rozsahu](search-query-simple-examples.md#example-4-range-filters) | 
| [Hledání v poli](query-lucene-syntax.md#bkmk_fields) | Parametr Search a úplný analyzátor | Sestavení složeného výrazu dotazu zaměřeného na jedno pole. <br/>[Příklad hledání v poli](search-query-lucene-examples.md#example-2-fielded-search) |
| [Automatické dokončování nebo navrhované výsledky](search-autocomplete-tutorial.md) | Parametr pro automatické dokončování nebo návrh | Alternativní formulář dotazu, který se spouští na základě částečných řetězců v prostředí hledání typu vyhledávání. Můžete použít funkci automatického dokončování a návrhů společně nebo samostatně. |
| [hledání přibližných výsledků](query-lucene-syntax.md#bkmk_fuzzy) | Parametr Search a úplný analyzátor | Odpovídá podmínkám, které mají podobnou konstrukci nebo pravopis. <br/>[Příklad přibližného hledání](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [hledání blízkosti](query-lucene-syntax.md#bkmk_proximity) | Parametr Search a úplný analyzátor | Vyhledá v dokumentu skoro stejné výrazy. <br/>[Příklad hledání blízkosti](search-query-lucene-examples.md#example-4-proximity-search) |
| [zvyšování termínů](query-lucene-syntax.md#bkmk_termboost) | Parametr Search a úplný analyzátor | Rozhodne dokument větší, pokud obsahuje zvýšený termín vzhledem k ostatním, které ne. <br/>[Příklad zvyšování skóre termínu](search-query-lucene-examples.md#example-5-term-boosting) |
| [hledání regulárních výrazů](query-lucene-syntax.md#bkmk_regex) | Parametr Search a úplný analyzátor | Odpovídá na základě obsahu regulárního výrazu. <br/>[Příklad regulárního výrazu](search-query-lucene-examples.md#example-6-regex) |
|  [zástupné znaky nebo hledání předpon](query-lucene-syntax.md#bkmk_wildcard) | Parametr Search a úplný analyzátor | Odpovídá v závislosti na předponě a vlnovce () `~` nebo jednom znaku ( `?` ). <br/>[Příklad hledání pomocí zástupných znaků](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="next-steps"></a>Další kroky

Pomocí portálu nebo jiného nástroje, jako je například post nebo Visual Studio Code, nebo jedné ze sad SDK můžete prozkoumat dotazy podrobněji. Následující odkazy vám pomohou začít.

+ [Průzkumník vyhledávání](search-explorer.md)
+ [Dotazování v .NET](./search-get-started-dotnet.md)
+ [Dotazování v REST](./search-get-started-powershell.md)
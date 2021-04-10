---
title: Typy dotazů
titleSuffix: Azure Cognitive Search
description: Seznamte se s typy dotazů podporovaných v Kognitivní hledání, včetně volného textu, filtru, automatického dokončování a návrhů, geografického hledání, systémových dotazů a vyhledávání v dokumentech.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 21012848ba3624df6110eaea182beccc4646d234
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105609271"
---
# <a name="querying-in-azure-cognitive-search"></a>Dotazování v Azure Kognitivní hledání

Azure Kognitivní hledání nabízí bohatý dotazovací jazyk pro podporu široké škály scénářů, od bezplatného vyhledávání textu až po vysoce specifikované vzory dotazů. Tento článek popisuje požadavky na dotazy a typy dotazů, které můžete vytvořit.

V Kognitivní hledání je dotaz kompletní specifikací operace Round-Trip **`search`** s parametry, které informují provádění dotazů a tvarují odpověď zpět. Parametry a analyzátory určují typ žádosti o dotaz. Následující příklad dotazu je bezplatný textový dotaz s logickým operátorem, který používá [hledání dokumentů (REST API)](/rest/api/searchservice/search-documents), cílení na kolekci [hotelů – Sample-index](search-get-started-portal.md) Documents.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "queryType": "simple",
    "searchMode": "all",
    "search": "restaurant +view",
    "searchFields": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "select": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

Mezi parametry používané při provádění dotazu patří:

+ **`queryType`** Nastaví analyzátor, což je buď [výchozí jednoduchý analyzátor dotazů](search-query-simple-examples.md) (optimální pro fulltextové vyhledávání), nebo [kompletní analyzátor dotazů Lucene](search-query-lucene-examples.md) , který se používá pro pokročilé konstrukce dotazů, jako jsou regulární výrazy, vyhledávání blízkosti, přibližná a zástupné vyhledávání, pro pojmenování několika.

+ **`searchMode`** Určuje, zda jsou shody založené na kritériích "All" nebo "any" ve výrazu. Výchozí hodnota je.

+ **`search`** poskytuje kritéria shody, obvykle celé podmínky nebo fráze s operátory nebo bez. Každé pole, které je s atributy *prohledávatelné* ve schématu indexu, je kandidátem pro tento parametr.

+ **`searchFields`** omezuje provádění dotazů na konkrétní hledaná pole. Během vývoje je vhodné použít pro příkaz SELECT a Search stejný seznam polí. V opačném případě může být shoda založena na hodnotách pole, které ve výsledcích nevidíte, a vytvořit nejistotu, co byl dokument vrácen.

Parametry použité k tvarování odpovědi:

+ **`select`** Určuje, která pole se mají vrátit v odpovědi. V příkazu SELECT lze použít pouze pole *, která jsou označena jako* načístelné v indexu.

+ **`top`** Vrátí zadaný počet nejlépe vyhovujících dokumentů. V tomto příkladu se vrátí jenom 10 přístupů. Pro stránku výsledků můžete použít horní a přeskočení (není zobrazeno).

+ **`count`** oznamuje, kolik dokumentů v celém indexu se shoduje se souhrnem, což může být víc, než se vrátí. 

+ **`orderby`** se používá, pokud chcete výsledky seřadit podle hodnoty, jako je například hodnocení nebo umístění. V opačném případě se ve výchozím nastavení použije hodnocení relevance k zařazení výsledků. Pole musí být v atributu, aby bylo možné *Řadit* jako kandidát pro tento parametr.

Seznam výše je reprezentativní, ale není vyčerpávající. Úplný seznam parametrů pro požadavek na dotaz naleznete v tématu [Search Documents (REST API)](/rest/api/searchservice/search-documents).

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Typy dotazů

V několika důležitých výjimkách je požadavek na dotaz iterace u obrácených indexů, které jsou strukturované pro rychlé prověřování, kde shoda se dá najít v potenciálně jakémkoli poli, a to v libovolném počtu vyhledávacích dokumentů. V Kognitivní hledání primární metodologie hledání shody je buď fulltextové vyhledávání, nebo filtry, ale můžete také implementovat jiné dobře známé vyhledávací prostředí, jako je automatické dokončování nebo hledání geografického umístění. Zbývající část tohoto článku shrnuje dotazy v Kognitivní hledání a obsahuje odkazy na Další informace a příklady.

## <a name="full-text-search"></a>Fulltextové vyhledávání

Pokud vaše aplikace pro vyhledávání obsahuje vyhledávací pole, které shromažďuje termín, je pravděpodobné, že operace dotazování, která toto prostředí provádí, je pravděpodobně fulltextovým vyhledáváním. Fulltextové vyhledávání přijímá podmínky nebo fráze předané **`search`** parametrem ve všech *prohledávatelných* polích v indexu. Volitelné logické operátory v řetězci dotazu mohou určovat kritéria zahrnutí nebo vyloučení. Jednoduché analyzátory i úplné analyzátory podporují fulltextové vyhledávání.

V Kognitivní hledání je fulltextové vyhledávání založené na dotazovacím stroji Apache Lucene. Řetězce dotazů v fulltextovém vyhledávání procházejí lexikální analýzou, aby bylo prohledávání efektivnější. Analýza zahrnuje všechny termíny s malým celým písmenem, odebrání stop slov, jako je "a", a zkrácení podmínek pro primitivní kořenové formuláře. Výchozí analyzátor je standardní Lucene.

V případě, že jsou nalezeny odpovídající výrazy, modul dotazů znovu vytvoří vyhledávací dokument obsahující shodu pomocí klíče dokumentu nebo ID k sestavení hodnot polí, rozhodne dokumenty v pořadí podle relevance a vrátí nejvyšší 50 (ve výchozím nastavení) v odpovědi nebo jiné číslo, pokud jste určili **`top`** .

Pokud implementujete fulltextové vyhledávání, pomůže vám pochopit, jakým způsobem je váš obsah založen na tokenech, a pomůže vám ladit případné anomálie dotazů. Dotazy nad řetězci s pomlčkou nebo speciálními znaky můžou vyžadovat použití analyzátoru jiného než výchozího standardního poiterace, aby se zajistilo, že index obsahuje správné tokeny. Můžete přepsat výchozí hodnoty pomocí [analyzátorů jazyka](index-add-language-analyzers.md#language-analyzer-list) nebo [specializovaných analyzátorů](index-add-custom-analyzers.md#built-in-analyzers) , které upraví lexikální analýzu. Jedním z příkladů je [klíčové slovo](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) , které se zachází s celým obsahem pole jako s jedním tokenem. To je užitečné pro data, jako jsou kódy PSČ, ID a některé názvy produktů. Další informace naleznete v části [hledání částečného termínu a vzory se speciálními znaky](search-query-partial-matching.md).

Pokud předpokládáte těžké použití logických operátorů, což je pravděpodobnější v indexech, které obsahují velké textové bloky (pole obsahu nebo dlouhé popisy), ujistěte se, že testujete dotazy s **`searchMode=Any|All`** parametrem, abyste vyhodnotili dopad tohoto nastavení na logických hledání.

## <a name="autocomplete-and-suggested-queries"></a>Automatické dokončování a navrhované dotazy

[Automatické dokončování nebo navrhované výsledky](search-add-autocomplete-suggestions.md) jsou alternativami k **`search`** tomuto úspěšnému zpracování požadavků na dotazy, které jsou založeny na částečných vstupech řetězců (za každý znak) v prostředí hledání, které je typu vyhledávání. Můžete použít **`autocomplete`** a **`suggestions`** parametr společně nebo samostatně, jak je popsáno v [tomto kurzu](tutorial-csharp-type-ahead-and-suggestions.md), ale nemůžete je použít v **`search`** . Dokončené a navrhované dotazy jsou odvozeny z obsahu indexu. Modul nikdy nevrátí řetězec nebo návrh, který není ve vašem indexu existující. Další informace najdete v tématu [Automatické dokončování (REST API)](/rest/api/searchservice/autocomplete) a [návrhy (REST API)](/rest/api/searchservice/suggestions).

## <a name="filter-search"></a>Filtrovat hledání

Filtry se běžně používají v aplikacích, které zahrnují Kognitivní hledání. Na stránkách aplikace jsou filtry často vizuálně znázorněné jako charakteristiky v navigačních strukturách odkazů pro uživatelsky orientované filtrování. Filtry se používají také interně k vystavování řezů indexovaných obsahu. Můžete například inicializovat stránku vyhledávání pomocí filtru v kategorii produktů nebo jazyka, pokud rejstřík obsahuje pole v angličtině i ve francouzštině.

Je také možné, že budete potřebovat filtry k vyvolání specializovaného formuláře dotazu, jak je popsáno v následující tabulce. Můžete použít filtr s neurčeným hledáním ( **`search=*`** ) nebo s řetězcem dotazu, který obsahuje výrazy, fráze, operátory a vzory.

| Scénář filtru | Description |
|-----------------|-------------|
| Filtry rozsahu | V Azure Kognitivní hledání jsou dotazy Range sestaveny pomocí parametru Filter. Další informace a příklady najdete v tématu [příklad filtru rozsahu](search-query-simple-examples.md#example-5-range-filters). |
| Hledání geografického umístění | Pokud je prohledávatelné pole [typu EDM. GeographyPoint](/rest/api/searchservice/supported-data-types), můžete vytvořit výraz filtru pro "najít blízko" nebo ovládací prvky hledání na základě mapy. Pole, která jednotky geografického hledání obsahují, obsahují souřadnice. Další informace a příklad najdete v [příkladu geografického hledání](search-query-simple-examples.md#example-6-geo-search). |
| Fasetová navigace | Navigační struktura omezující vlastnosti se bude instrumentovat v navigaci orientované na uživatele při vyvolání filtru v reakci na `onclick` událost s omezující vlastností. V takovém případě jsou omezující vlastnosti a filtry k dispozici. Pokud přidáte navigaci omezující vlastnosti, budete potřebovat filtry pro dokončení prostředí. Další informace najdete v tématu [postup sestavení filtru omezující vlastnosti](search-filters-facets.md). |

> [!NOTE]
> Text, který se používá ve výrazu filtru, se během zpracování dotazu neanalyzuje. Textový vstup je považován za doslovné vzor znaků, který je buď úspěšný nebo neúspěšný na shodě. Výrazy filtru jsou vytvořené pomocí [syntaxe OData](query-odata-filter-orderby-syntax.md) a předají se v **`filter`** parametru ve všech polích, která se *filtrují* v indexu. Další informace najdete v tématu [filtry v Azure kognitivní hledání](search-filters.md).

## <a name="document-look-up"></a>Hledání dokumentů

Na rozdíl od dříve popsaných formulářů dotazů načte tato funkce jeden [vyhledávací dokument podle ID](/rest/api/searchservice/lookup-document)bez odpovídajícího hledání indexu ani skenování. Je požadován a vrácen pouze jeden dokument. Když uživatel vybere položku ve výsledcích hledání, načte dokument a naplní stránku podrobností pomocí polí je typickou odpovědí a vyhledávání dokumentů je operace, která ji podporuje.

## <a name="advanced-search-fuzzy-wildcard-proximity-regex"></a>Rozšířené vyhledávání: fuzzy, zástupné znaky, blízkost, Regex

Rozšířený formulář dotazu závisí na úplném analyzátoru a operátorech Lucene, které spouštějí konkrétní chování dotazu.

| Typ dotazu | Využití | Příklady a další informace |
|------------|--------|------------------------------|
| [Hledání v poli](query-lucene-syntax.md#bkmk_fields) | **`search`**  ukazatele **`queryType=full`**  | Sestavení složeného výrazu dotazu zaměřeného na jedno pole. <br/>[Příklad hledání v poli](search-query-lucene-examples.md#example-1-fielded-search) |
| [hledání přibližných výsledků](query-lucene-syntax.md#bkmk_fuzzy) | **`search`** ukazatele **`queryType=full`** | Odpovídá podmínkám, které mají podobnou konstrukci nebo pravopis. <br/>[Příklad přibližného hledání](search-query-lucene-examples.md#example-2-fuzzy-search) |
| [hledání blízkosti](query-lucene-syntax.md#bkmk_proximity) | **`search`** ukazatele **`queryType=full`** | Vyhledá v dokumentu skoro stejné výrazy. <br/>[Příklad hledání blízkosti](search-query-lucene-examples.md#example-3-proximity-search) |
| [zvyšování termínů](query-lucene-syntax.md#bkmk_termboost) | **`search`** ukazatele **`queryType=full`** | Rozhodne dokument větší, pokud obsahuje zvýšený termín vzhledem k ostatním, které ne. <br/>[Příklad zvyšování skóre termínu](search-query-lucene-examples.md#example-4-term-boosting) |
| [hledání regulárních výrazů](query-lucene-syntax.md#bkmk_regex) | **`search`** ukazatele **`queryType=full`** | Odpovídá na základě obsahu regulárního výrazu. <br/>[Příklad regulárního výrazu](search-query-lucene-examples.md#example-5-regex) |
|  [zástupné znaky nebo hledání předpon](query-lucene-syntax.md#bkmk_wildcard) | **`search`** parametr s * *_`~`_* nebo **`?`** , **`queryType=full`**| Odpovídá v závislosti na předponě a vlnovce () `~` nebo jednom znaku ( `?` ). <br/>[Příklad hledání pomocí zástupných znaků](search-query-lucene-examples.md#example-6-wildcard-search) |

## <a name="next-steps"></a>Další kroky

Pro bližší pohled na implementaci dotazu si Projděte příklady jednotlivých syntaxí. Pokud začínáte s fulltextovým vyhledáváním, podíváme se na to, co modul dotazů může být stejně dobrý.

+ [Příklady jednoduchých dotazů](search-query-simple-examples.md)
+ [Příklady dotazů syntaxe Lucene pro vytváření pokročilých dotazů](search-query-lucene-examples.md)
+ [Jak funguje úplné hledání textu v Azure kognitivní hledání](search-lucene-query-architecture.md)Git
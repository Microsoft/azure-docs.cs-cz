---
title: Filtrovat podle výsledků hledání
titleSuffix: Azure Cognitive Search
description: Filtr podle identity zabezpečení uživatele, jazyka, geografického umístění nebo číselných hodnot pro omezení výsledků hledání dotazů v Azure Kognitivní hledání, hostované cloudové vyhledávací služby v Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: ba538f4753c2365406bd88286b6d54cff1a9e9ea
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104800818"
---
# <a name="filters-in-azure-cognitive-search"></a>Filtry v Azure Kognitivní hledání 

*Filtr* poskytuje kritéria založená na hodnotách pro výběr dokumentů použitých v dotazu. Filtr může být jedna hodnota nebo [výraz filtru](search-query-odata-filter.md)OData. Na rozdíl od fulltextového vyhledávání vrátí hodnota filtru nebo výraz pouze striktní shodu.

Některá vyhledávací prostředí, jako je například vymezená [Navigace](search-filters-facets.md), závisí na filtrech v rámci implementace, ale filtry můžete použít kdykoli, chcete-li určit obor dotazu na konkrétní hodnoty. Pokud místo toho máte v cíli obor dotazu na konkrétní pole, existují alternativní metody popsané níže.

## <a name="when-to-use-a-filter"></a>Kdy použít filtr

Filtry jsou základem pro několik vyhledávacích funkcí, jako je "najít blízko jsem", omezující navigaci a filtry zabezpečení, které zobrazují pouze dokumenty, které uživatel může zobrazit. Pokud implementujete některé z těchto prostředí, je vyžadován filtr. Je to filtr připojený k vyhledávacímu dotazu, který poskytuje souřadnice geografického umístění, kategorii omezující vlastnosti vybranou uživatelem nebo ID zabezpečení žadatele.

Mezi běžné scénáře patří následující:

+ Výsledky hledání řezů na základě obsahu v indexu Když máte schéma s umístěním hotelu, kategoriemi a rekreačními oblastmi, můžete vytvořit filtr, který explicitně odpovídá kritériím (v Seattlu, v celé vodě se zobrazením). 

+ Implementací možnosti vyhledávání se dokončí požadavek filtru:

  + [Omezující navigace](search-faceted-navigation.md) používá filtr k předání zpět kategorie omezující vlastnosti vybrané uživatelem.
  + Geografické vyhledávání používá filtr k předání souřadnic aktuálního umístění v aplikacích "najít blízko". 
  + [Filtry zabezpečení](search-security-trimming-for-azure-search.md) přecházejí identifikátory zabezpečení jako kritéria filtru, kde shoda v indexu slouží jako proxy pro přístupová práva k dokumentu.

+ Provede "čísla hledání". Číselná pole lze načíst a mohou se zobrazit ve výsledcích hledání, ale nelze je vyhledat (podle toho, co je v souladu s fulltextovým vyhledáváním). Pokud potřebujete kritéria výběru založená na číslech dat, použijte filtr.

### <a name="alternative-methods-for-reducing-scope"></a>Alternativní metody pro omezení rozsahu

Pokud chcete ve výsledcích hledání zúžit efekt, filtry nejsou vaší volbou. V závislosti na vašem cíli můžou být tyto alternativy lépe vyhovující:

+ `searchFields` parametr dotazu omezuje hledání na konkrétní pole. Například pokud váš index poskytuje samostatná pole pro anglické a španělské popisy, můžete použít searchFields k určení, která pole se mají použít pro fulltextové vyhledávání. 

+ `$select` parametr se používá k určení, která pole se mají zahrnout do sady výsledků, aby se před odesláním do volající aplikace účinně vystřihoval odpověď. Tento parametr neupřesňuje dotaz ani neomezuje kolekci dokumentů, ale pokud je menší odezva na váš cíl, je tento parametr možnost zvážit. 

Další informace o obou parametrech naleznete v tématu [Search documents > Request > dotazu Parameters](/rest/api/searchservice/search-documents#query-parameters).

## <a name="how-filters-are-executed"></a>Jak se spouštějí filtry

Analyzátor filtru v době dotazu přijímá kritéria jako vstup, převede výraz na atomické logické výrazy reprezentované jako strom a pak vyhodnotí strom filtru přes pole, která lze filtrovat v indexu.

K filtrování dochází v kombinaci se službou Search a je kvalifikováno, které dokumenty zahrnout do zpracování pro příjem dat pro načtení dokumentů a hodnocení relevance. Při párování s hledaným řetězcem filtr efektivně snižuje sadu odvolání následné operace vyhledávání. Při samostatném použití (například při prázdném řetězci dotazu, kde `search=*` ) je kritérium filtru jediným vstupem. 

## <a name="defining-filters"></a>Definování filtrů

Filtry jsou výrazy OData kloubované ve [syntaxi filtru](search-query-odata-filter.md) podporované kognitivní hledání.

Pro každou operaci **hledání** můžete zadat jeden filtr, ale samotný filtr může zahrnovat více polí, více kritérií a pokud použijete funkci **Match** , více výrazů fulltextového vyhledávání. Ve výrazu filtru s více částmi lze predikáty zadat v libovolném pořadí (v souladu s pravidly priority operátoru). V případě, že se pokusíte změnit uspořádání predikátů v určité sekvenci, neexistuje žádný výrazný nárůst výkonu.

Jedním z omezení výrazu filtru je omezení maximální velikosti požadavku. Celý požadavek, včetně filtru, může mít maximálně 16 MB pro POST nebo 8 KB pro GET. Existuje také omezení počtu klauzulí ve výrazu filtru. Dobrým pravidlem je, že pokud máte stovky klauzulí, riskujete, že se nebudete muset provozovat. Doporučujeme navrhnout aplikaci tak, aby negenerovala filtry neohraničené velikosti.

Následující příklady znázorňují definice filtru typický v několika rozhraních API.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?api-version=2020-06-30&search=*&$filter=Rooms/any(room: room/BaseRate lt 150.0)&$select=HotelId, HotelName, Rooms/Description, Rooms/BaseRate

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate lt 150.0)",
    "select": "HotelId, HotelName, Rooms/Description, Rooms/BaseRate"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(room: room/BaseRate lt 150.0)",
            Select = new[] { "HotelId", "HotelName", "Rooms/Description" ,"Rooms/BaseRate"}
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>Filtrovat vzorce použití

Následující příklady ilustrují několik vzorů použití pro scénáře filtru. Další nápady najdete v tématu [Syntaxe výrazů OData > příklady](./search-query-odata-filter.md#examples).

+ Samostatný **$Filter** bez řetězce dotazu, který je užitečný, když výraz filtru dokáže plně kvalifikovat dokumenty, které vás zajímají. Bez řetězce dotazu neexistují žádné lexikální ani lingvistické analýzy, žádné bodování a žádné hodnocení. Všimněte si, že hledaný řetězec je jenom hvězdička, což znamená "odpovídá všem dokumentům".

  ```http
  {
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu"
  }
  ```

+ Kombinace řetězce dotazu a **$Filter**, kde filtr vytvoří podmnožinu, a řetězec dotazu poskytuje termínové vstupy pro fulltextové vyhledávání nad filtrovanou podmnožinou. Přidání podmínek (procházejí kino z vzdálenosti) zavádí výsledky hledání ve výsledcích, kde jsou dokumenty, které nejlépe odpovídají podmínkám, seřazené vyšší. Nejběžnějším vzorem použití je použití filtru s řetězcem dotazu.

  ```http
  {
    "search": "walking distance theaters",
    "filter": "Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'"
  }

+ Compound queries, separated by "or", each with its own filter criteria (for example, 'beagles' in 'dog' or 'siamese' in 'cat'). Expressions combined with `or` are evaluated individually, with the union of documents matching each expression sent back in the response. This usage pattern is achieved through the `search.ismatchscoring` function. You can also use the non-scoring version, `search.ismatch`.

   ```
   # <a name="match-on-hostels-rated-higher-than-4-or-5-star-motels"></a>Shoda s Hostels hodnocením vyšším než 4 nebo 5 hvězdiček Motels.
   $filter = Search. ismatchscoring (' Hostel ') a hodnocení GE 4 nebo Search. ismatchscoring (' Motel ') a hodnocení EQ 5

   # <a name="match-on-luxury-or-high-end-in-the-description-field-or-on-category-exactly-equal-to-luxury"></a>V poli Popis se shoduje s ' luxus ' nebo ' high-end ' v poli Description nebo v kategorii, která se přesně rovná ' luxus '.
   $filter = Search. ismatchscoring (' luxus | high-end ', ' Description ') nebo Category EQ ' luxus ' &$count = true
   ```

  It is also possible to combine full-text search via `search.ismatchscoring` with filters using `and` instead of `or`, but this is functionally equivalent to using the `search` and `$filter` parameters in a search request. For example, the following two queries produce the same result:

  ```
  $filter = Search. ismatchscoring (' Pool ') a hodnocení GE 4

  Search = Pool&$filter = hodnocení GE 4
  ```

Follow up with these articles for comprehensive guidance on specific use cases:

+ [Facet filters](search-filters-facets.md)
+ [Security trimming](search-security-trimming-for-azure-search.md) 

## Field requirements for filtering

In the REST API, filterable is *on* by default for simple fields. Filterable fields increase index size; be sure to set `"filterable": false` for fields that you don't plan to actually use in a filter. For more information about settings for field definitions, see [Create Index](/rest/api/searchservice/create-index).

In the .NET SDK, the filterable is *off* by default. You can make a field filterable by setting the [IsFilterable property](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) of the corresponding [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) object to `true`. In the example below, the attribute is set on the `BaseRate` property of a model class that maps to the index definition.

```csharp
[IsFilterable, IsSortable, IsFacetable]
public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Zpřístupnění existujícího pole s filtrem

Existující pole nemůžete upravovat, aby je bylo možné filtrovat. Místo toho je nutné přidat nové pole nebo znovu sestavit index. Další informace o opětovném sestavení indexu nebo přeplňování polí najdete v tématu [jak znovu sestavit index služby Azure kognitivní hledání](search-howto-reindex.md).

## <a name="text-filter-fundamentals"></a>Základy textových filtrů

Textové filtry odpovídají polím řetězců v řetězcích literálů, které zadáte do filtru: `$filter=Category eq 'Resort and Spa'`

Na rozdíl od fulltextového vyhledávání neexistuje žádná lexikální analýza ani zalamování slov pro textové filtry, takže porovnání jsou pouze pro přesné shody. Například Předpokládejme, že pole *f* obsahuje "Slunečné Day", neodpovídá `$filter=f eq 'Sunny'` , ale `$filter=f eq 'sunny day'` bude. 

V textových řetězcích se rozlišují velká a malá písmena. Nejsou k dispozici malá a velká písmena použitach slov: `$filter=f eq 'Sunny day'` "Slunečné Day" (den) se nenalezne.

### <a name="approaches-for-filtering-on-text"></a>Přístupy k filtrování textu

| Přístup | Description | Kdy je použít |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | Funkce, která odpovídá poli s odděleným seznamem řetězců. | Doporučuje se pro [filtry zabezpečení](search-security-trimming-for-azure-search.md) a pro všechny filtry, u kterých je potřeba spárovat mnoho nezpracovaných textových hodnot pomocí pole řetězce. Funkce **Search.in** je navržena pro rychlost a je mnohem rychlejší než explicitní porovnání pole s každým řetězcem pomocí `eq` a `or` . | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | Funkce, která umožňuje kombinovat operace fulltextového vyhledávání s přísnými logickými operacemi filtru ve stejném výrazu filtru. | Pokud chcete více kombinací vyhledávacího filtru v jednom požadavku, použijte **Search** . ismatchscoring (nebo jeho ekvivalent pro vyhodnocování, **Search.**). Můžete ji také použít pro filtr *obsahuje* pro filtrování částečného řetězce v rámci většího řetězce. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | Uživatelsky definovaný výraz tvořený poli, operátory a hodnotami. | Toto použijte, pokud chcete najít přesné shody mezi řetězcovým polem a řetězcovou hodnotou. |

## <a name="numeric-filter-fundamentals"></a>Základy číselného filtru

Číselná pole nejsou `searchable` v kontextu fulltextového vyhledávání. Pouze řetězce jsou předmětem fulltextového vyhledávání. Pokud například zadáte 99,99 jako hledaný termín, nebudou se za položky vracet ceny za $99,99. Místo toho se zobrazí položky, které mají číslo 99 v polích řetězců dokumentu. Pokud tedy máte číselná data, předpokládáme, že je budete používat pro filtry, včetně rozsahů, omezujících vlastností, skupin a tak dále. 

Dokumenty, které obsahují číselná pole (Price, Size, SKU, ID), poskytují tyto hodnoty ve výsledcích hledání, pokud je pole označeno `retrievable` . Zde je uvedeno, že fulltextové vyhledávání samotné není použitelné pro číselné typy polí.

## <a name="next-steps"></a>Další kroky

Nejprve na portálu zkuste **Vyhledat** dotazy pomocí parametrů **$Filter** . [Index reálného vzorku](search-get-started-portal.md) poskytuje zajímavé výsledky pro následující filtrované dotazy při jejich vkládání do panelu hledání:

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

Chcete-li pracovat s dalšími příklady, viz [Příklady syntaxe výrazů filtru OData > příklady](./search-query-odata-filter.md#examples).

## <a name="see-also"></a>Viz také

+ [Jak funguje fulltextové vyhledávání ve službě Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Rozhraní API pro vyhledávání v dokumentech](/rest/api/searchservice/search-documents)
+ [Jednoduchá syntaxe dotazů](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Syntaxe dotazů Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Podporované datové typy](/rest/api/searchservice/supported-data-types)
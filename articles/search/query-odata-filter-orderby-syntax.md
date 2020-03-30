---
title: Přehled jazyka OData
titleSuffix: Azure Cognitive Search
description: Přehled jazyka OData pro dotazy filtrů, výběru a pořadí podle pro Azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: f3a1be435e297ab4a9ba7f8bfbd5f3ce3451d8a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77153872"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-cognitive-search"></a>Přehled jazyka OData `$orderby`pro `$select` `$filter`, a v Azure Cognitive Search

Azure Cognitive Search podporuje podmnožinu syntaxe výrazu OData pro výrazy **$filter**, **$orderby**a **$select.** Výrazy filtrů jsou vyhodnocovány během analýzy dotazu, omezení vyhledávání na konkrétní pole nebo přidání kritérií shody používaných při prohledávání indexu. Vyjádření podle pořadí jsou použita jako krok po zpracování nad sadou výsledků k řazení vrácených dokumentů. Výběr výrazů určuje, která pole dokumentu jsou zahrnuta do sady výsledků. Syntaxe těchto výrazů se liší od [jednoduché](query-simple-syntax.md) nebo [úplné](query-lucene-syntax.md) syntaxe dotazu, která se používá v parametru **vyhledávání,** i když je v syntaxi pro odkazování na pole určité překrytí.

Tento článek obsahuje přehled jazyka OData používaného ve filtrech, pořadí a vybraných výrazech. Jazyk je prezentován "zdola nahoru", počínaje nejzákladnějšími prvky a jejich stavbou. Syntaxe nejvyšší úrovně pro každý parametr je popsána v samostatném článku:

- [$filter syntaxe](search-query-odata-filter.md)
- [$orderby syntaxe](search-query-odata-orderby.md)
- [$select syntaxe](search-query-odata-select.md)

OData výrazy se pohybují od jednoduchých až po velmi složité, ale všechny sdílejí společné prvky. Nejzákladnější části výrazu OData v Azure Cognitive Search jsou:

- **Cesty polí**, které odkazují na určitá pole indexu.
- **Konstanty**, které jsou literálové hodnoty určitého datového typu.

> [!NOTE]
> Terminologie v Azure Cognitive Search se liší od [standardu OData](https://www.odata.org/documentation/) několika způsoby. To, co nazýváme **pole** v Azure Cognitive Search se nazývá **vlastnost** v OData a podobně pro **cestu pole** versus **cestu vlastnosti**. **Index** obsahující **dokumenty** v Azure Cognitive Search se v OData označuje obecněji jako **sada entit** obsahující **entity**. Terminologie Azure Cognitive Search se používá v celém tomto odkazu.

## <a name="field-paths"></a>Cesty polí

Následující formulář EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiku cest polí.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

K dispozici je také interaktivní syntaktický diagram:

> [!div class="nextstepaction"]
> [Syntaxový diagram OData pro Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> Viz [odkaz na syntaxi výrazu OData pro Azure Cognitive Search](search-query-odata-syntax-reference.md) pro kompletní EBNF.

Cesta pole se skládá z jednoho nebo více **identifikátorů oddělených** lomítky. Každý identifikátor je posloupnost znaků, které musí začínat písmenem ASCII nebo podtržítkem a obsahují pouze písmena, číslice nebo podtržítka ASCII. Písmena mohou být velká nebo malá písmena.

Identifikátor může odkazovat buď na název pole, nebo na **proměnnou rozsahu** `any` v `all`kontextu [výrazu kolekce](search-query-odata-collection-operators.md) ( nebo ) ve filtru. Proměnná rozsahu je jako proměnná smyčky, která představuje aktuální prvek kolekce. Pro komplexní kolekce představuje tato proměnná objekt, což je důvod, proč můžete použít cesty polí odkazovat na dílčí pole proměnné. To je analogické dot notace v mnoha programovacích jazycích.

Příklady cest polí jsou uvedeny v následující tabulce:

| Cesta pole | Popis |
| --- | --- |
| `HotelName` | Odkazuje na pole nejvyšší úrovně indexu. |
| `Address/City` | Odkazuje na `City` dílčí pole komplexního pole v indexu; `Address` je typu `Edm.ComplexType` v tomto příkladu |
| `Rooms/Type` | Odkazuje na `Type` dílčí pole komplexníkolekce pole v indexu; `Rooms` je typu `Collection(Edm.ComplexType)` v tomto příkladu |
| `Stores/Address/Country` | Odkazuje na `Country` dílčí pole dílčího `Address` pole komplexního sběrného pole v indexu; `Stores` je typu `Collection(Edm.ComplexType)` `Address` a je `Edm.ComplexType` typu v tomto příkladu |
| `room/Type` | Odkazuje na `Type` dílčí pole proměnné `room` rozsahu, například ve výrazu filtru.`Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | Odkazuje na `Country` dílčí pole `Address` dílčího pole proměnné `store` rozsahu, například ve výrazu filtru.`Stores/any(store: store/Address/Country eq 'Canada')` |

Význam cesty pole se liší v závislosti na kontextu. Ve filtrech označuje cesta pole hodnotu *jedné instance* pole v aktuálním dokladu. V jiných kontextech, například **$orderby**, **$select**nebo při [hledání v poli v úplné syntaxi Lucene](query-lucene-syntax.md#bkmk_fields), označuje cesta pole samotné pole. Tento rozdíl má některé důsledky pro použití cesty polí ve filtrech.

Zvažte cestu `Address/City`pole . Ve filtru se jedná o jedno město pro aktuální dokument, například "San Francisco". V kontrastu, `Rooms/Type` odkazuje `Type` na sub-pole pro mnoho místností (jako "standard" pro první pokoj, "deluxe" pro druhou místnost, a tak dále). Vzhledem k tomu, `Rooms/Type` že neodkazuje na *jednu instanci* dílčího pole `Type`, nelze ji použít přímo ve filtru. Místo toho chcete-li filtrovat typ místnosti, použijte [výraz lambda](search-query-odata-collection-operators.md) s proměnnou rozsahu, například takto:

    Rooms/any(room: room/Type eq 'deluxe')

V tomto příkladu `room` se v `room/Type` cestě pole zobrazí proměnná rozsahu. Tímto `room/Type` způsobem odkazuje na typ aktuální místnosti v aktuálním dokumentu. Jedná se o jednu instanci dílčího `Type` pole, takže ji lze použít přímo ve filtru.

### <a name="using-field-paths"></a>Použití cest polí

Cesty polí se používají v mnoha parametrech [rozhraní REST Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/). V následující tabulce jsou uvedena všechna místa, kde je lze použít, a všechna omezení jejich použití:

| rozhraní API | Název parametru | Omezení |
| --- | --- | --- |
| [Vytvořit](https://docs.microsoft.com/rest/api/searchservice/create-index) nebo [aktualizovat](https://docs.microsoft.com/rest/api/searchservice/update-index) index | `suggesters/sourceFields` | Žádný |
| [Vytvořit](https://docs.microsoft.com/rest/api/searchservice/create-index) nebo [aktualizovat](https://docs.microsoft.com/rest/api/searchservice/update-index) index | `scoringProfiles/text/weights` | Může odkazovat pouze na **prohledávatelná** pole |
| [Vytvořit](https://docs.microsoft.com/rest/api/searchservice/create-index) nebo [aktualizovat](https://docs.microsoft.com/rest/api/searchservice/update-index) index | `scoringProfiles/functions/fieldName` | Může odkazovat pouze na **filtrovatelná** pole |
| [Hledat](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `search`kdy `queryType` je`full` | Může odkazovat pouze na **prohledávatelná** pole |
| [Hledat](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | Může odkazovat pouze na **pole s lícovou stůl.** |
| [Hledat](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | Může odkazovat pouze na **prohledávatelná** pole |
| [Hledat](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | Může odkazovat pouze na **prohledávatelná** pole |
| [Návrh](https://docs.microsoft.com/rest/api/searchservice/suggestions) a [automatické dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | Může odkazovat pouze na pole, která jsou součástí [návrhu.](index-add-suggesters.md) |
| [Hledání](https://docs.microsoft.com/rest/api/searchservice/search-documents), [návrh](https://docs.microsoft.com/rest/api/searchservice/suggestions)a [automatické dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | Může odkazovat pouze na **filtrovatelná** pole |
| [Hledat](https://docs.microsoft.com/rest/api/searchservice/search-documents) a [navrhnout](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | Může odkazovat pouze na **seřaditelná** pole. |
| [Hledání](https://docs.microsoft.com/rest/api/searchservice/search-documents), [návrh](https://docs.microsoft.com/rest/api/searchservice/suggestions)a [vyhledávání](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | Může odkazovat pouze na **navypronatelná** pole. |

## <a name="constants"></a>Konstanty

Konstanty v OData jsou literály daného typu [datového modelu entity](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) (EDM). Seznam podporovaných typů v Azure Cognitive Search najdete v [tématu Podporované datové typy.](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) Konstanty typů kolekcí nejsou podporovány.

V následující tabulce jsou uvedeny příklady konstant pro každý z datových typů podporovaných Azure Cognitive Search:

| Datový typ | Příkladkonstanty |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

### <a name="escaping-special-characters-in-string-constants"></a>Unikající speciální znaky v řetězcových konstantách

Řetězcové konstanty v OData jsou odděleny jednoduchými uvozovkami. Pokud potřebujete vytvořit dotaz s řetězcovou konstantou, která může sama obsahovat jednoduché uvozovky, můžete uniknout vložené uvozovky jejich zdvojnásobením.

Například fráze s neformátovanou apostrof jako "Alice auto" by být zastoupeny `'Alice''s car'`v OData jako řetězec konstanta .

> [!IMPORTANT]
> Při vytváření filtrů programově, je důležité mít na paměti, aby uniknout řetězec konstanty, které pocházejí ze vstupu uživatele. To má zmírnit možnost [vstřikovacích útoků](https://wikipedia.org/wiki/SQL_injection), zejména při použití filtrů k implementaci [bezpečnostního oříznutí](search-security-trimming-for-azure-search.md).

### <a name="constants-syntax"></a>Syntaxe konstant

Následující formulář EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiku pro většinu konstant uvedených ve výše uvedené tabulce. Gramatiku pro geograficky prostorové typy najdete v [geoprostorových funkcích OData v Azure Cognitive Search](search-query-odata-geo-spatial-functions.md).

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
constant ::=
    string_literal
    | date_time_offset_literal
    | integer_literal
    | float_literal
    | boolean_literal
    | 'null'

string_literal ::= "'"([^'] | "''")*"'"

date_time_offset_literal ::= date_part'T'time_part time_zone

date_part ::= year'-'month'-'day

time_part ::= hour':'minute(':'second('.'fractional_seconds)?)?

zero_to_fifty_nine ::= [0-5]digit

digit ::= [0-9]

year ::= digit digit digit digit

month ::= '0'[1-9] | '1'[0-2]

day ::= '0'[1-9] | [1-2]digit | '3'[0-1]

hour ::= [0-1]digit | '2'[0-3]

minute ::= zero_to_fifty_nine

second ::= zero_to_fifty_nine

fractional_seconds ::= integer_literal

time_zone ::= 'Z' | sign hour':'minute

sign ::= '+' | '-'

/* In practice integer literals are limited in length to the precision of
the corresponding EDM data type. */
integer_literal ::= digit+

float_literal ::=
    sign? whole_part fractional_part? exponent?
    | 'NaN'
    | '-INF'
    | 'INF'

whole_part ::= integer_literal

fractional_part ::= '.'integer_literal

exponent ::= 'e' sign? integer_literal

boolean_literal ::= 'true' | 'false'
```

K dispozici je také interaktivní syntaktický diagram:

> [!div class="nextstepaction"]
> [Syntaxový diagram OData pro Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> Viz [odkaz na syntaxi výrazu OData pro Azure Cognitive Search](search-query-odata-syntax-reference.md) pro kompletní EBNF.

## <a name="building-expressions-from-field-paths-and-constants"></a>Vytváření výrazů z cest polí a konstant

Cesty polí a konstanty jsou nejzákladnější částí výrazu OData, ale samy o sobě jsou úplnými výrazy. Ve skutečnosti **$select** parametr v Azure Cognitive Search není nic jiného než čárka oddělený seznam cest polí a **$orderby** není mnohem složitější než **$select**. Pokud máte v indexu pole `Edm.Boolean` typu, můžete dokonce napsat filtr, který není nic jiného než cesta tohoto pole. Konstanty `true` a `false` jsou také platné filtry.

Většinu času však budete potřebovat složitější výrazy, které odkazují na více než jedno pole a konstantu. Tyto výrazy jsou vytvořeny různými způsoby v závislosti na parametru.

Následující eBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiku pro **parametry $filter**, **$orderby**a **$select.** Ty jsou vytvořeny z jednodušších výrazů, které odkazují na cesty polí a konstanty:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

K dispozici je také interaktivní syntaktický diagram:

> [!div class="nextstepaction"]
> [Syntaxový diagram OData pro Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> Viz [odkaz na syntaxi výrazu OData pro Azure Cognitive Search](search-query-odata-syntax-reference.md) pro kompletní EBNF.

**Parametry $orderby** a **$select** jsou seznamy jednodušších výrazů oddělené čárkami. Parametr **$filter** je logický výraz, který se skládá z jednodušších dílčích výrazů. Tyto dílčí výrazy jsou kombinovány pomocí logických [ `and`operátorů, `or` `not` ](search-query-odata-logical-operators.md)jako jsou , a , porovnávací operátory, jako [ `eq`jsou , `lt`, `gt`a tak](search-query-odata-comparison-operators.md)dále , a inkasní operátory, jako [ `any` `all` ](search-query-odata-collection-operators.md)jsou a .

Parametry **$filter**, **$orderby**a **$select** jsou podrobněji popsány v následujících článcích:

- [Syntaxe $filter OData v Azure Cognitive Search](search-query-odata-filter.md)
- [Syntaxe $orderby OData v Azure Cognitive Search](search-query-odata-orderby.md)
- [Syntaxe $select OData v Azure Cognitive Search](search-query-odata-select.md)

## <a name="see-also"></a>Viz také  

- [Famitová navigace v Azure Cognitive Search](search-faceted-navigation.md)
- [Filtry v Azure Cognitive Search](search-filters.md)
- [Hledání dokumentů &#40;azure kognitivní vyhledávání rozhraní API rozhraní&#41;API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Syntaxe dotazů Lucene](query-lucene-syntax.md)
- [Jednoduchá syntaxe dotazu v Azure Cognitive Search](query-simple-syntax.md)

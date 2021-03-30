---
title: Přehled jazyka OData
titleSuffix: Azure Cognitive Search
description: Přehled jazyka OData pro filtry, výběr a pořadí dotazů pro Azure Kognitivní hledání.
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
ms.openlocfilehash: d04311fce81d147a0830918aee1d4a2a9c0808d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88923394"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-cognitive-search"></a>Přehled jazyka OData pro `$filter` , `$orderby` a `$select` v Azure kognitivní hledání

Azure Kognitivní hledání podporuje podmnožinu Syntaxe výrazů OData pro výrazy **$Filter**, **$OrderBy** a **$Select** . Výrazy filtru jsou vyhodnocovány během analýzy dotazů, omezení vyhledávání na konkrétní pole nebo přidání kritérií shody používaných při prohledávání indexů. Výrazy ORDER by se používají jako krok po zpracování v rámci sady výsledků dotazu pro řazení vrácených dokumentů. Výrazy pro výběr určují, která pole dokumentu jsou zahrnutá v sadě výsledků. Syntaxe těchto výrazů je odlišná od syntaxe [jednoduchého](query-simple-syntax.md) nebo [úplného](query-lucene-syntax.md) dotazu, která se používá v parametru **vyhledávání** , i když je v syntaxi pro odkazování polí nějaký překryv.

Tento článek poskytuje přehled jazyka OData Expression používaného ve filtrech, výrazech ORDER by a SELECT. Jazyk se zobrazí jako "zdola nahoru", počínaje základními prvky a sestavování na nich. Syntaxe nejvyšší úrovně pro každý parametr je popsána v samostatném článku:

- [$filter syntaxe](search-query-odata-filter.md)
- [$orderby syntaxe](search-query-odata-orderby.md)
- [$select syntaxe](search-query-odata-select.md)

Výrazy OData jsou od jednoduchých až vysoce složité, ale všechny sdílejí společné prvky. Nejzákladnější části výrazu OData v Azure Kognitivní hledání jsou:

- **Cesty k poli**, které odkazují na konkrétní pole indexu.
- **Konstanty**, které jsou literálové hodnoty určitého datového typu.

> [!NOTE]
> Terminologie v Azure Kognitivní hledání se od [standardu OData](https://www.odata.org/documentation/) liší několika způsoby. K volání **pole** v Azure kognitivní hledání se říká **vlastnost** v OData a podobně jako **cesta k poli** a cesta k **vlastnosti**. **Index** obsahující **dokumenty** v Azure kognitivní hledání se v OData označuje obecněji jako **sada entit** obsahující **entity**. V rámci tohoto odkazu se používá terminologie Azure Kognitivní hledání.

## <a name="field-paths"></a>Cesty k poli

Následující EBNF ([rozšířený Backus-Naur formulář](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiku cest polí.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

K dispozici je také diagram interaktivní syntaxe:

> [!div class="nextstepaction"]
> [Diagram syntaxe OData pro Azure Kognitivní hledání](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> Kompletní EBNF najdete v článku [referenční informace k syntaxi výrazu OData pro Azure kognitivní hledání](search-query-odata-syntax-reference.md) .

Cesta pole se skládá z jednoho nebo více **identifikátorů** oddělených lomítky. Každý identifikátor je posloupnost znaků, které musí začínat písmenem nebo podtržítkem, a obsahovat pouze písmena ASCII, číslice nebo podtržítka. Písmena můžou být velká nebo malá.

Identifikátor může odkazovat buď na název pole, nebo na **proměnnou rozsahu** v kontextu [výrazu kolekce](search-query-odata-collection-operators.md) ( `any` nebo `all` ) ve filtru. Proměnná rozsahu je jako proměnná smyčky, která představuje aktuální prvek kolekce. U komplexních kolekcí Tato proměnná představuje objekt, což je důvod, proč můžete použít cesty polí pro odkazování na dílčí pole proměnné. To je obdobou zápisu teček v mnoha programovacích jazycích.

Příklady cest polí jsou uvedené v následující tabulce:

| Cesta k poli | Popis |
| --- | --- |
| `HotelName` | Odkazuje na pole nejvyšší úrovně indexu. |
| `Address/City` | Odkazuje na `City` dílčí pole komplexního pole v indexu; `Address` je typu `Edm.ComplexType` v tomto příkladu. |
| `Rooms/Type` | Odkazuje na `Type` dílčí pole komplexní kolekce pole v indexu; `Rooms` je typu `Collection(Edm.ComplexType)` v tomto příkladu. |
| `Stores/Address/Country` | Odkazuje na `Country` dílčí pole `Address` v dílčím poli komplexní kolekce v indexu, `Stores` je typu `Collection(Edm.ComplexType)` a `Address` je typu `Edm.ComplexType` v tomto příkladu. |
| `room/Type` | Odkazuje na `Type` dílčí pole `room` proměnné rozsahu, například ve výrazu filtru. `Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | Odkazuje na `Country` dílčí pole `Address` dílčího pole `store` proměnné rozsahu, například ve výrazu filtru. `Stores/any(store: store/Address/Country eq 'Canada')` |

Význam cesty k poli se liší v závislosti na kontextu. Ve filtrech odkazuje cesta pole na hodnotu *jedné instance* pole v aktuálním dokumentu. V jiných kontextech, jako je například **$OrderBy**, **$Select** nebo v [poli hledání v úplné syntaxi Lucene](query-lucene-syntax.md#bkmk_fields), odkazuje cesta pole na pole samotné. Tento rozdíl má některé důsledky pro použití cest polí ve filtrech.

Vezměte v úvahu cestu k poli `Address/City` . Ve filtru to odkazuje na jedno město pro aktuální dokument, jako je například "San Francisco". Naopak `Rooms/Type` odkazuje na `Type` dílčí pole pro mnoho místností (například "Standard" pro první místnost, "Deluxe" pro druhou místnost atd.). Vzhledem `Rooms/Type` k tomu, že neodkazuje na *jednu instanci* dílčího pole `Type` , nelze ji použít přímo ve filtru. Místo toho byste měli použít [výraz lambda](search-query-odata-collection-operators.md) s proměnnou rozsahu, jako je například:

```odata
Rooms/any(room: room/Type eq 'deluxe')
```

V tomto příkladu se proměnná rozsahu `room` zobrazuje v cestě k `room/Type` poli. Tímto způsobem `room/Type` odkazuje na typ aktuální místnosti v aktuálním dokumentu. Toto je jediná instance `Type` dílčího pole, takže ji můžete použít přímo ve filtru.

### <a name="using-field-paths"></a>Použití cest polí

Cesty polí se používají v mnoha parametrech [rozhraní REST API služby Azure kognitivní hledání](/rest/api/searchservice/). V následující tabulce jsou uvedena všechna místa, kde je lze použít, a veškerá omezení jejich používání:

| Rozhraní API | Název parametru | Omezení |
| --- | --- | --- |
| [Vytvořit](/rest/api/searchservice/create-index) nebo [aktualizovat](/rest/api/searchservice/update-index) index | `suggesters/sourceFields` | Žádné |
| [Vytvořit](/rest/api/searchservice/create-index) nebo [aktualizovat](/rest/api/searchservice/update-index) index | `scoringProfiles/text/weights` | Lze odkazovat pouze na pole s **možností prohledávání** . |
| [Vytvořit](/rest/api/searchservice/create-index) nebo [aktualizovat](/rest/api/searchservice/update-index) index | `scoringProfiles/functions/fieldName` | Lze odkazovat pouze na pole, která lze **filtrovat** . |
| [Hledání](/rest/api/searchservice/search-documents) | `search` Kdy `queryType` je `full` | Lze odkazovat pouze na pole s **možností prohledávání** . |
| [Hledání](/rest/api/searchservice/search-documents) | `facet` | Lze odkazovat pouze na pole **obličeje** . |
| [Hledání](/rest/api/searchservice/search-documents) | `highlight` | Lze odkazovat pouze na pole s **možností prohledávání** . |
| [Hledání](/rest/api/searchservice/search-documents) | `searchFields` | Lze odkazovat pouze na pole s **možností prohledávání** . |
| [Navrhnout](/rest/api/searchservice/suggestions) a automaticky [dokončovat](/rest/api/searchservice/autocomplete) | `searchFields` | Může odkazovat jenom na pole, která jsou součástí modulu pro [návrhy](index-add-suggesters.md) . |
| [Hledání](/rest/api/searchservice/search-documents), [Návrh](/rest/api/searchservice/suggestions)a [Automatické dokončování](/rest/api/searchservice/autocomplete) | `$filter` | Lze odkazovat pouze na pole, která lze **filtrovat** . |
| [Hledat](/rest/api/searchservice/search-documents) a [navrhnout](/rest/api/searchservice/suggestions) | `$orderby` | Lze odkazovat pouze na pole, která lze **Seřadit** . |
| [Hledání](/rest/api/searchservice/search-documents), [Návrh](/rest/api/searchservice/suggestions)a [vyhledávání](/rest/api/searchservice/lookup-document) | `$select` | Může odkazovat **jenom na pole, která lze načíst** . |

## <a name="constants"></a>Konstanty

Konstanty v OData jsou literálové hodnoty daného typu [model EDM (Entity Data Model)](/dotnet/framework/data/adonet/entity-data-model) (EDM). Seznam podporovaných typů v Azure Kognitivní hledání najdete v tématu [podporované datové typy](/rest/api/searchservice/supported-data-types) . Konstanty typů kolekcí nejsou podporovány.

V následující tabulce jsou uvedeny příklady konstant pro každý datový typ podporovaný službou Azure Kognitivní hledání:

| Datový typ | Příklady konstant |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

### <a name="escaping-special-characters-in-string-constants"></a>Uvozovací znaky speciálních znaků v řetězcových konstantách

Řetězcové konstanty v OData jsou oddělené jednoduchými uvozovkami. Pokud potřebujete sestavit dotaz s řetězcovou konstantou, která by mohla obsahovat jednoduché uvozovky, můžete vložit vložené uvozovky pomocí zdvojnásobení.

Například fráze s neformátovaným apostrofem, jako je Alice auto, by byla vyjádřena v OData jako řetězcová konstanta `'Alice''s car'` .

> [!IMPORTANT]
> Při sestavování filtrů programově je důležité pamatovat na řídicí konstanty řetězce, které pocházejí ze vstupu uživatele. Účelem je zmírnit možnost [útoků prostřednictvím injektáže](https://wikipedia.org/wiki/SQL_injection), zejména při použití filtrů k implementaci [oříznutí zabezpečení](search-security-trimming-for-azure-search.md).

### <a name="constants-syntax"></a>Syntaxe konstant

Následující EBNF ([rozšířený Backus-Naur formulář](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiku pro většinu konstant zobrazených ve výše uvedené tabulce. Gramatika pro geografické typy se dá najít v [geograficky funkčních funkcích OData v Azure kognitivní hledání](search-query-odata-geo-spatial-functions.md).

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

K dispozici je také diagram interaktivní syntaxe:

> [!div class="nextstepaction"]
> [Diagram syntaxe OData pro Azure Kognitivní hledání](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> Kompletní EBNF najdete v článku [referenční informace k syntaxi výrazu OData pro Azure kognitivní hledání](search-query-odata-syntax-reference.md) .

## <a name="building-expressions-from-field-paths-and-constants"></a>Vytváření výrazů z cest polí a konstant

Cesty polí a konstanty jsou nejzákladnější částí výrazu OData, ale jsou již plnými výrazy samotné. Ve skutečnosti je parametr **$Select** v Azure kognitivní hledání Nothing, ale čárkami oddělený seznam cest polí a **$OrderBy** není mnohem složitější než **$Select**. Pokud budete mít pole typu `Edm.Boolean` v indexu, můžete dokonce napsat filtr, který není Nothing, ale cesta k tomuto poli. Konstanty `true` a `false` jsou stejně platnými filtry.

Ve většině případů ale budete potřebovat složitější výrazy, které odkazují na více než jedno pole a konstantu. Tyto výrazy jsou sestaveny různými způsoby v závislosti na parametru.

Následující EBNF ([rozšířený Backus-Naur formulář](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiku pro parametry **$Filter**, **$OrderBy** a **$Select** . Ty jsou sestavené z jednodušších výrazů, které odkazují na cesty polí a konstanty:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

K dispozici je také diagram interaktivní syntaxe:

> [!div class="nextstepaction"]
> [Diagram syntaxe OData pro Azure Kognitivní hledání](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> Kompletní EBNF najdete v článku [referenční informace k syntaxi výrazu OData pro Azure kognitivní hledání](search-query-odata-syntax-reference.md) .

Parametry **$OrderBy** a **$Select** jsou čárkami oddělený seznam jednodušších výrazů. Parametr **$Filter** je logický výraz, který se skládá z jednodušších dílčích výrazů. Tyto dílčí výrazy jsou kombinovány pomocí logických operátorů, jako jsou [ `and` , `or` a `not` ](search-query-odata-logical-operators.md), operátory porovnání, jako [ `eq` jsou,, `lt` `gt` a tak](search-query-odata-comparison-operators.md)dále, a operátory kolekce, jako jsou [ `any` a `all` ](search-query-odata-collection-operators.md).

Parametry **$Filter**, **$OrderBy** a **$Select** jsou podrobněji prozkoumání v následujících článcích:

- [Syntaxe $filter OData v Azure Kognitivní hledání](search-query-odata-filter.md)
- [Syntaxe $orderby OData v Azure Kognitivní hledání](search-query-odata-orderby.md)
- [Syntaxe $select OData v Azure Kognitivní hledání](search-query-odata-select.md)

## <a name="see-also"></a>Viz také  

- [Omezující navigace v Azure Kognitivní hledání](search-faceted-navigation.md)
- [Filtry v Azure Kognitivní hledání](search-filters.md)
- [Hledání dokumentů &#40;Azure Kognitivní hledání REST API&#41;](/rest/api/searchservice/Search-Documents)
- [Syntaxe dotazů Lucene](query-lucene-syntax.md)
- [Jednoduchá syntaxe dotazů v Azure Kognitivní hledání](query-simple-syntax.md)
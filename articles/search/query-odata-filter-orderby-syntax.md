---
title: Přehled jazyka OData – Azure Search
description: Přehled jazyka OData pro filtry, vyberte a ORDER pro Azure Search dotazy.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: 166c23088fe0388199ca51efde05153bb5697e38
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063706"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-search"></a>Přehled jazyka OData pro `$filter`, `$orderby`, a `$select` ve službě Azure Search

Služba Azure Search podporuje podmnožinou syntaxe výrazů OData pro **$filter**, **$orderby**, a **$select** výrazy. Filtrovací výrazy jsou vyhodnocovány během zpracování dotazu parsování, omezení hledání konkrétních polí nebo přidání kritéria shody využít při kontrolách indexu. Výrazy Order by se použijí jako krok následného zpracování přes sadu výsledků pro uspořádání dokumentů, které jsou vráceny. Vyberte výrazy určit pole dokumentu, která jsou součástí sady výsledků. Syntaxe tyto výrazy se liší od [jednoduché](query-simple-syntax.md) nebo [úplné](query-lucene-syntax.md) syntaxe, která se používá v dotazu **hledání** parametr sice nějakou překrývají v syntaxi pro odkazování na pole.

Tento článek poskytuje přehled o výraz jazyka OData použít filtry, klauzule order by a select výrazy. Jazyk se zobrazí "zdola nahoru", spuštění s naprosto základní prvky a na jejich vytváření. Nejvyšší úrovně syntaxe pro každý parametr je věnovaný samostatný článek podle:

- [Syntaxe $filter](search-query-odata-filter.md)
- [Syntaxe $orderby](search-query-odata-orderby.md)
- [Syntaxe $select](search-query-odata-select.md)

OData výrazy v rozsahu od jednoduchých velmi složité, ale všechny sdílejí společné prvky. Většina základních částí výrazu OData ve službě Azure Search jsou:

- **Pole cest**, který odkazuje na konkrétní pole vašeho indexu.
- **Konstanty**, které jsou literálové hodnoty určitého data typu.

> [!NOTE]
> Terminologie ve službě Azure Search se liší od [OData standard](https://www.odata.org/documentation/) několika způsoby. Čemu říkáme **pole** ve službě Azure Search je volána **vlastnost** v OData a podobně pro **cesta pole** oproti **cesta k vlastnosti**. **Index** obsahující **dokumenty** ve službě Azure Search je podle obecněji OData jako **sadu entit** obsahující **entity**. Terminologie pro Azure Search se používá v celém tento odkaz.

## <a name="field-paths"></a>Pole cesty

Následující EBNF ([Extended Backus-Naur – formulář](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiky pole cesty.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

Diagramu interaktivní syntaxe je také k dispozici:

> [!div class="nextstepaction"]
> [Diagram syntaxe OData pro službu Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> Zobrazit [referenční příručka syntaxe výrazů OData pro službu Azure Search](search-query-odata-syntax-reference.md) pro dokončení EBNF.

Cesta pole se skládá z jedné nebo více **identifikátory** odděleny lomítky. Každý identifikátor je sekvence znaků, které musí začínat ASCII písmenem nebo podtržítkem a obsahovat jen ASCII písmena, číslice a podtržítka. Může být písmena, velká nebo malá.

Identifikátor mohou odkazovat na název pole, nebo **proměnnou rozsahu** v kontextu [výrazu kolekce](search-query-odata-collection-operators.md) (`any` nebo `all`) ve filtru. Proměnná rozsahu je jako proměnnou smyčky, který reprezentuje aktuálního prvku kolekce. Pro komplexní kolekce Tato proměnná představuje objekt, což je důvod, proč můžete použít pole cesty k odkazování na dílčí pole proměnné. To je obdobou zápisu s tečkou v řadě programovacích jazyků.

V následující tabulce jsou uvedeny příklady pole cest:

| Cesta pole | Popis |
| --- | --- |
| `HotelName` | Odkazuje na nejvyšší úrovni pole indexu |
| `Address/City` | Odkazuje `City` dílčí pole komplexní pole v indexu; `Address` je typu `Edm.ComplexType` v tomto příkladu |
| `Rooms/Type` | Odkazuje `Type` dílčí pole komplexní kolekci pole v indexu; `Rooms` je typu `Collection(Edm.ComplexType)` v tomto příkladu |
| `Stores/Address/Country` | Odkazuje `Country` dílčí pole `Address` dílčí pole komplexní kolekci pole v indexu; `Stores` je typu `Collection(Edm.ComplexType)` a `Address` je typu `Edm.ComplexType` v tomto příkladu |
| `room/Type` | Odkazuje `Type` dílčí pole `room` proměnnou rozsahu, například ve výrazu filtru `Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | Odkazuje `Country` dílčí pole `Address` dílčí pole `store` proměnnou rozsahu, například ve výrazu filtru `Stores/any(store: store/Address/Country eq 'Canada')` |

Význam cesta pole se liší v závislosti na kontextu. Filtry, odkazuje cesta pole na hodnotu *jednu instanci* pole v aktuálním dokumentu. V jiných kontextech například **$orderby**, **$select**, nebo v [fielded vyhledávání v celé syntaxe Lucene](query-lucene-syntax.md#bkmk_fields), pole Cesta odkazuje na pole samotného. Tento rozdíl má některé důsledky pro použití pole cesty ve filtrech.

Vezměte v úvahu cesta pole `Address/City`. Ve filtru to se vztahuje na jeden Město pro aktuální dokument, jako je "San Francisco". Naproti tomu `Rooms/Type` odkazuje `Type` dílčí pole pro mnoho místnosti (např. "standard" pro první místnost "deluxe" pro druhý místnosti a tak dále). Protože `Rooms/Type` neodkazuje *jednu instanci* dílčí pole `Type`, nelze použít přímo ve filtru. Místo toho pokud chcete filtrovat podle typu místnosti, použijte [výraz lambda](search-query-odata-collection-operators.md) s proměnnou rozsahu, například takto:

    Rooms/any(room: room/Type eq 'deluxe')

V tomto příkladu proměnná rozsahu `room` se zobrazí v `room/Type` cesta pole. Tímto způsobem `room/Type` odkazuje na typ aktuální místnosti v aktuálním dokumentu. Toto je jedna instance `Type` dílčí pole, takže ho můžete použít přímo ve filtru.

### <a name="using-field-paths"></a>Pomocí pole cesty

Pole cesty se používají v mnoha parametry [API služby Azure Search](https://docs.microsoft.com/rest/api/searchservice/). Následující tabulka uvádí všechna místa, kde lze použít, a navíc nějaká omezení jejich využití:

| Rozhraní API | Název parametru | Omezení |
| --- | --- | --- |
| [Vytvoření](https://docs.microsoft.com/rest/api/searchservice/create-index) nebo [aktualizace](https://docs.microsoft.com/rest/api/searchservice/update-index) indexu | `suggesters/sourceFields` | Žádný |
| [Vytvoření](https://docs.microsoft.com/rest/api/searchservice/create-index) nebo [aktualizace](https://docs.microsoft.com/rest/api/searchservice/update-index) indexu | `scoringProfiles/text/weights` | Mohou odkazovat pouze na **prohledávatelné** pole |
| [Vytvoření](https://docs.microsoft.com/rest/api/searchservice/create-index) nebo [aktualizace](https://docs.microsoft.com/rest/api/searchservice/update-index) indexu | `scoringProfiles/functions/fieldName` | Mohou odkazovat pouze na **filterable** pole |
| [Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `search` Když `queryType` je `full` | Mohou odkazovat pouze na **prohledávatelné** pole |
| [Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | Mohou odkazovat pouze na **facetable** pole |
| [Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | Mohou odkazovat pouze na **prohledávatelné** pole |
| [Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | Mohou odkazovat pouze na **prohledávatelné** pole |
| [Navrhnout](https://docs.microsoft.com/rest/api/searchservice/suggestions) a [automatického dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | Mohou odkazovat pouze na pole, které jsou součástí [modulu pro návrhy](index-add-suggesters.md) |
| [Hledání](https://docs.microsoft.com/rest/api/searchservice/search-documents), [navrhnout](https://docs.microsoft.com/rest/api/searchservice/suggestions), a [automatického dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | Mohou odkazovat pouze na **filterable** pole |
| [Hledání](https://docs.microsoft.com/rest/api/searchservice/search-documents) a [navrhnout](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | Mohou odkazovat pouze na **seřaditelné** pole |
| [Hledání](https://docs.microsoft.com/rest/api/searchservice/search-documents), [navrhnout](https://docs.microsoft.com/rest/api/searchservice/suggestions), a [vyhledávání](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | Mohou odkazovat pouze na **retrievable** pole |

## <a name="constants"></a>Konstanty

Konstanty v OData se jednu hodnotu literálu danou [modelu Entity Data Model](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) typ (EDM). Zobrazit [podporované datové typy](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) seznam podporovaných typů ve službě Azure Search. Konstanty kolekci typů nejsou podporovány.

V následující tabulce jsou uvedeny příklady konstanty pro jednotlivé typy dat podporované službou Azure Search:

| Typ dat | Příklad konstanty |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

Následující EBNF ([Extended Backus-Naur – formulář](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiku pro většinu konstanty je znázorněno výše uvedené tabulky. Gramatika pro geoprostorové typy lze nalézt v [OData geoprostorové funkce ve službě Azure Search](search-query-odata-geo-spatial-functions.md).

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

Diagramu interaktivní syntaxe je také k dispozici:

> [!div class="nextstepaction"]
> [Diagram syntaxe OData pro službu Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> Zobrazit [referenční příručka syntaxe výrazů OData pro službu Azure Search](search-query-odata-syntax-reference.md) pro dokončení EBNF.

## <a name="building-expressions-from-field-paths-and-constants"></a>Vytváření výrazů od a konstanty pole cesty

Pole cest a konstanty jsou základní součástí výrazu OData, ale tam už úplné výrazy sami. Ve skutečnosti **$select** parametr ve službě Azure Search není nic ale čárkou oddělený seznam cest pole a **$orderby** není mnohem složitější než **$select**. Pokud máte náhodou pole typu `Edm.Boolean` v indexu, lze dokonce psát filtr, který má hodnotu nothing, ale cesta tohoto pole. Konstanty `true` a `false` jsou rovněž platné filtry.

Ve většině případů budete však mnohem složitější výrazy, které odkazují na více než jedno pole a konstanty. Tyto výrazy jsou sestaveny různými způsoby v závislosti na parametr.

Následující EBNF ([Extended Backus-Naur – formulář](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiky **$filter**, **$orderby**, a **$select** parametry. Tyto jsou vytvářeny z jednodušší výrazy, které odkazují na pole cesty a konstanty:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

Diagramu interaktivní syntaxe je také k dispozici:

> [!div class="nextstepaction"]
> [Diagram syntaxe OData pro službu Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> Zobrazit [referenční příručka syntaxe výrazů OData pro službu Azure Search](search-query-odata-syntax-reference.md) pro dokončení EBNF.

**$Orderby** a **$select** parametry jsou obě čárkami oddělený seznam výrazů jednodušší. **$Filter** parametr je logický výraz, který se skládá z jednodušší dílčích výrazů. Tato dílčí výrazy jsou kombinované pomocí logických operátorů, například [ `and`, `or`, a `not` ](search-query-odata-logical-operators.md), operátory porovnání, jako například [ `eq`, `lt`, `gt`, a tak dále](search-query-odata-comparison-operators.md)a kolekce operátory, jako například [ `any` a `all` ](search-query-odata-collection-operators.md).

**$Filter**, **$orderby**, a **$select** parametry jsou prozkoumat podrobněji v následujících článcích:

- [Syntaxe OData $filter ve službě Azure Search](search-query-odata-filter.md)
- [Syntaxe OData $orderby ve službě Azure Search](search-query-odata-orderby.md)
- [Syntaxe OData $select ve službě Azure Search](search-query-odata-select.md)

## <a name="see-also"></a>Další informace najdete v tématech  

- [Fasetová navigace ve službě Azure Search](search-faceted-navigation.md)
- [Filtry ve službě Azure Search](search-filters.md)
- [Hledání dokumentů &#40;rozhraní REST API služby Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Syntaxe dotazů Lucene](query-lucene-syntax.md)
- [Jednoduchá syntaxe dotazů ve službě Azure Search](query-simple-syntax.md)

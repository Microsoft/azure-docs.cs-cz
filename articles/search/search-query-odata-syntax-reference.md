---
title: Odkaz syntaxe výrazu OData
titleSuffix: Azure Cognitive Search
description: Formální gramatika a syntaxe specifikace pro výrazy OData v azure kognitivní vyhledávání dotazů.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: f3422fd10e062ae87bc165491e0d01ac2b4943d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793231"
---
# <a name="odata-expression-syntax-reference-for-azure-cognitive-search"></a>Odkaz na syntaxi výrazu OData pro Azure Cognitive Search

Azure Cognitive Search používá [výrazy OData](https://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part2-url-conventions.html) jako parametry v celém rozhraní API. Nejčastěji se pro parametry `$orderby` a `$filter` používají výrazy OData. Tyto výrazy mohou být složité, obsahující více klauzulí, funkcí a operátorů. Však i jednoduché OData výrazy, jako jsou cesty vlastností se používají v mnoha částech rozhraní API Azure Cognitive Search REST. Výrazy cesty se například používají k odkazování na dílčí pole složitých polí všude v rozhraní API, například při výpisu dílčích polí v [návrhu](index-add-suggesters.md), [funkci hodnocení](index-add-scoring-profiles.md), `$select` parametr nebo dokonce [fielded vyhledávání v Lucene dotazech](query-lucene-syntax.md).

Tento článek popisuje všechny tyto formy výrazů OData pomocí formální gramatiky. K dispozici je také [interaktivní diagram,](#syntax-diagram) který vám pomůže vizuálně prozkoumat gramatiku.

## <a name="formal-grammar"></a>Formální gramatika

Můžeme popsat podmnožinu jazyka OData podporované azure kognitivní vyhledávání pomocí EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) gramatiky. Pravidla jsou uvedena "shora dolů", počínaje nejsložitějšími výrazy a jejich rozdělením na více primitivní výrazy. V horní části jsou gramatická pravidla, která odpovídají konkrétním parametrům rozhraní REST API Azure Cognitive Search:

- [`$filter`](search-query-odata-filter.md), definované `filter_expression` pravidlem.
- [`$orderby`](search-query-odata-orderby.md), definované `order_by_expression` pravidlem.
- [`$select`](search-query-odata-select.md), definované `select_expression` pravidlem.
- Cesty polí definované pravidlem. `field_path` Cesty polí se používají v celém rozhraní API. Mohou odkazovat buď na pole nejvyšší úrovně indexu, nebo na dílčí pole s jedním nebo více složitými předky [polí.](search-howto-complex-data-types.md)

Po EBNF je browsable [syntaktický diagram,](https://en.wikipedia.org/wiki/Syntax_diagram) který vám umožní interaktivně prozkoumat gramatiku a vztahy mezi jeho pravidly.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
/* Top-level rules */

filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*


/* Shared base rules */

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*


/* Rules for $orderby */

order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'


/* Rules for $filter */

boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path

collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression

logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression

comparison_expression ::= 
    variable_or_function comparison_operator constant | 
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'


/* Rules for constants and literals */

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


/* Rules for functions */

function_call ::=
    geo_distance_call |
    boolean_function_call

geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

boolean_function_call ::=
    geo_intersects_call |
    search_in_call |
    search_is_match_call

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*

search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'

/* Note that it is illegal to call search.ismatch or search.ismatchscoring
from inside a lambda expression. */
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

## <a name="syntax-diagram"></a>Syntaktický diagram

Chcete-li vizuálně prozkoumat gramatiku jazyka OData podporovanou službou Azure Cognitive Search, vyzkoušejte interaktivní syntaktický diagram:

> [!div class="nextstepaction"]
> [Syntaxový diagram OData pro Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/)

## <a name="see-also"></a>Viz také  

- [Filtry v Azure Cognitive Search](search-filters.md)
- [Hledání dokumentů &#40;azure kognitivní vyhledávání rozhraní API rozhraní&#41;API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Syntaxe dotazů Lucene](query-lucene-syntax.md)
- [Jednoduchá syntaxe dotazu v Azure Cognitive Search](query-simple-syntax.md)

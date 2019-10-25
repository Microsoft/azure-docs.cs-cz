---
title: Referenční dokumentace syntaxe výrazu OData
titleSuffix: Azure Cognitive Search
description: Formální gramatika a specifikace syntaxe pro výrazy OData v Azure Kognitivní hledání dotazy.
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
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793231"
---
# <a name="odata-expression-syntax-reference-for-azure-cognitive-search"></a>Referenční dokumentace syntaxe výrazu OData pro Azure Kognitivní hledání

Azure Kognitivní hledání jako parametry v rozhraní API používá [výrazy OData](https://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part2-url-conventions.html) . K parametrům `$orderby` a `$filter` se nejčastěji používají výrazy OData. Tyto výrazy mohou být složité, obsahující více klauzulí, funkcí a operátorů. Nicméně i jednoduché výrazy OData, jako jsou cesty k vlastnostem, se používají v mnoha částech REST API Azure Kognitivní hledání. Například výrazy cest se používají k odkazování na podpole komplexních polí všude v rozhraní API, například při výpisu dílčích polí v modulu pro [návrhy](index-add-suggesters.md), [funkci hodnocení](index-add-scoring-profiles.md), parametru `$select` nebo dokonce [hledání v dotazech Lucene. ](query-lucene-syntax.md).

Tento článek popisuje všechny tyto formy výrazů OData pomocí formální gramatiky. K dispozici je také [interaktivní diagram](#syntax-diagram) , který usnadňuje vizuální zkoumání gramatiky.

## <a name="formal-grammar"></a>Formální gramatika

Podmnožinu jazyka OData podporovaného službou Azure Kognitivní hledání můžete popsat pomocí gramatiky na EBNF ([Rozšířená Backus – Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)). Pravidla jsou uvedena v seznamu "shora dolů", počínaje nejsložitějšími výrazy a jejich rozdělení do více primitivních výrazů. V horní části jsou gramatická pravidla, která odpovídají konkrétním parametrům Azure Kognitivní hledání REST API:

- [`$filter`](search-query-odata-filter.md)definuje pravidlo `filter_expression`.
- [`$orderby`](search-query-odata-orderby.md)definuje pravidlo `order_by_expression`.
- [`$select`](search-query-odata-select.md)definuje pravidlo `select_expression`.
- Cesty polí definované pravidlem `field_path`. Cesty polí se používají v celém rozhraní API. Můžou odkazovat buď na pole nejvyšší úrovně indexu, nebo na dílčí pole s jedním nebo více [komplexními poli](search-howto-complex-data-types.md) .

Poté, co je EBNF [diagram syntaxe](https://en.wikipedia.org/wiki/Syntax_diagram) , který umožňuje interaktivně prozkoumat gramatickou a vztah mezi pravidly.

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

## <a name="syntax-diagram"></a>Diagram syntaxe

Pokud chcete vizuálně prozkoumat gramatickou gramatiku, kterou podporuje Azure Kognitivní hledání, vyzkoušejte interaktivní diagram syntaxe:

> [!div class="nextstepaction"]
> [Diagram syntaxe OData pro Azure Kognitivní hledání](https://azuresearch.github.io/odata-syntax-diagram/)

## <a name="see-also"></a>Další informace najdete v tématech  

- [Filtry v Azure Kognitivní hledání](search-filters.md)
- [Hledání dokumentů &#40;Azure kognitivní hledání REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Syntaxe dotazů Lucene](query-lucene-syntax.md)
- [Jednoduchá syntaxe dotazů v Azure Kognitivní hledání](query-simple-syntax.md)

---
title: Odkaz na objednání OData
titleSuffix: Azure Cognitive Search
description: Syntaxe a referenční jazyk dokumentace pro použití pořadí v azure kognitivní vyhledávání dotazů.
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
ms.openlocfilehash: 99ec639b88f3334530243242aadfa0ab52a40df0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113145"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>Syntaxe $orderby OData v Azure Cognitive Search

 [Pomocí parametru OData **$orderby** ](query-odata-filter-orderby-syntax.md) můžete použít vlastní pořadí řazení pro výsledky hledání ve službě Azure Cognitive Search. Tento článek podrobně popisuje syntaxi **$orderby.** Obecnější informace o tom, jak používat **$orderby** při prezentaci výsledků hledání, najdete [v tématu Jak pracovat s výsledky hledání v Azure Cognitive Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Syntaxe

Parametr **$orderby** přijímá seznam oddělených čárkami až 32 **klauzulí podle pořadí**. Syntaxe klauzule order-by je popsána následující formou EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

K dispozici je také interaktivní syntaktický diagram:

> [!div class="nextstepaction"]
> [Syntaxový diagram OData pro Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Viz [odkaz na syntaxi výrazu OData pro Azure Cognitive Search](search-query-odata-syntax-reference.md) pro kompletní EBNF.

Každá klauzule má kritéria řazení, případně`asc` následovaná směrem `desc` řazení ( pro vzestupně nebo pro sestupně). Pokud nezadáte směr, výchozí hodnota je vzestupně. Kritéria řazení mohou být buď `sortable` cesta pole nebo volání [`geo.distance`](search-query-odata-geo-spatial-functions.md) buď [`search.score`](search-query-odata-search-score-function.md) nebo funkce.

Pokud má více dokumentů stejná `search.score` kritéria řazení a funkce se nepoužívá (například pokud řadíte podle číselného `Rating` pole a tři dokumenty mají hodnocení 4), budou vazby přerušeny skóre dokumentu v sestupném pořadí. Pokud jsou skóre dokumentu stejné (například pokud v požadavku není zadán žádný fulltextový vyhledávací dotaz), je relativní pořadí vázaných dokumentů neurčité.

Můžete zadat více kritérií řazení. Pořadí výrazů určuje konečné pořadí řazení. Chcete-li například seřadit sestupně podle skóre `$orderby=search.score() desc,Rating desc`následovaném hodnocením, bude syntaxe .

Syntaxe `geo.distance` pro in **$orderby** je stejná jako v **$filter**. Při `geo.distance` použití v **$orderby**musí být pole, na `Edm.GeographyPoint` které se `sortable`vztahuje, typu a musí být také .

Syntaxe `search.score` pro v `search.score()` **$orderby** je . Funkce `search.score` nebere žádné parametry.

## <a name="examples"></a>Příklady

Třídit hotely vzestupně podle základní sazby:

    $orderby=BaseRate asc

Seřaďte hotely sestupující podle hodnocení a pak vzestupně podle základní sazby (nezapomeňte, že výstup je výchozí):

    $orderby=Rating desc,BaseRate

Třídit hotely sestupující podle hodnocení, pak vzestupně podle vzdálenosti od daných souřadnic:

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

Třídit hotely v sestupném pořadí podle search.score a hodnocení, a pak ve vzestupném pořadí podle vzdálenosti od daných souřadnic. Mezi dvěma hotely se stejným skóre relevance a hodnocení, nejbližší z nich je uveden jako první:

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Další kroky  

- [Jak pracovat s výsledky hledání v Azure Cognitive Search](search-pagination-page-layout.md)
- [Přehled jazyka OData pro Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Odkaz na syntaxi výrazu OData pro Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;azure kognitivní vyhledávání rozhraní API rozhraní&#41;API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)

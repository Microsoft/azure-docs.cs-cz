---
title: OData klauzule order by reference – Azure Search
description: Referenční dokumentace jazyka OData pro syntaxe klauzule order by v dotazech Azure Search.
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
ms.openlocfilehash: 1ced35dc73e6d596fbeda32590ab0b69df396c5c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079753"
---
# <a name="odata-orderby-syntax-in-azure-search"></a>Syntaxe OData $orderby ve službě Azure Search

 Můžete použít [OData **$orderby** parametr](query-odata-filter-orderby-syntax.md) použít vlastní pořadí řazení pro výsledky hledání ve službě Azure Search. Tento článek popisuje syntaxi **$orderby** podrobně. Další obecné informace o tom, jak používat **$orderby** při zobrazení výsledků hledání, najdete v článku [výsledků jak pracovat s hledáním ve službě Azure Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Syntaxe

**$Orderby** parametr přijímá čárkami oddělený seznam až 32 **klauzule order by – klauzule**. Syntaxe klauzuli order by je popsán následující EBNF ([Extended Backus-Naur – formulář](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

Diagramu interaktivní syntaxe je také k dispozici:

> [!div class="nextstepaction"]
> [Diagram syntaxe OData pro službu Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Zobrazit [referenční příručka syntaxe výrazů OData pro službu Azure Search](search-query-odata-syntax-reference.md) pro dokončení EBNF.

Každá klauzule má kritéria řazení, může volitelně následovat směr řazení (`asc` pro vzestupné nebo `desc` pro sestupné řazení). Pokud nezadáte směru, je výchozí nastavení vzestupné. Kritéria řazení může být cesta `sortable` pole nebo volání na buď [ `geo.distance` ](search-query-odata-geo-spatial-functions.md) nebo [ `search.score` ](search-query-odata-search-score-function.md) funkce.

Pokud máte více dokumentů stejná kritéria řazení a `search.score` nepoužívá – funkce (např. Pokud můžete seřadit podle číselná `Rating` pole a dokumenty tři všechny mají hodnocení 4), vazby nebudou fungovat podle dokumentu skóre v sestupném pořadí. Pokud dokument skóre, které se shodují (například, když není žádný dotaz fulltextové vyhledávání v požadavku je zadaná), pak relativní řazení vázané dokumentů je neurčitý.

Můžete zadat více kritéria řazení. Pořadí výrazy Určuje konečné řazení. Například, chcete-li seřadit sestupně podle skóre, za nímž následuje hodnocení, syntaxe by `$orderby=search.score() desc,Rating desc`.

Syntaxe pro `geo.distance` v **$orderby** je stejný jako v **$filter**. Při použití `geo.distance` v **$orderby**, ke kterému se vztahuje pole musí být typu `Edm.GeographyPoint` a musí být také `sortable`.

Syntaxe pro `search.score` v **$orderby** je `search.score()`. Funkce `search.score` nepřijímá žádné parametry.

## <a name="examples"></a>Příklady

Seřadit vzestupně podle základní sazba hotels:

    $orderby=BaseRate asc

Seřadit sestupně podle hodnocení, pak vzestupně podle základní sazba hotels (mějte na paměti, že ascending je výchozí hodnota):

    $orderby=Rating desc,BaseRate

Seřadit sestupně podle hodnocení, pak vzestupně podle vzdálenosti od dané souřadnice hotels:

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

Seřadí v sestupném pořadí tak, že search.score a hodnocení a potom ve vzestupném pořadí podle vzdálenosti od dané souřadnice hotels. Mezi dvěma hotely se skóre relevance identické a hodnocení na ten nejbližší je uvedená jako první:

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Další postup  

- [Jak pracovat s hledání výsledků ve službě Azure Search](search-pagination-page-layout.md)
- [Přehled o výraz jazyka OData pro službu Azure Search](query-odata-filter-orderby-syntax.md)
- [Reference k syntaxi výrazů OData pro službu Azure Search](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;rozhraní REST API služby Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)

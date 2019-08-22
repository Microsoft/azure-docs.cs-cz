---
title: Objednávka OData – podle odkazu Azure Search
description: Referenční dokumentace jazyka OData pro syntaxi ORDER-by v Azure Searchch dotazech.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: 8ee44549931100a1affa5e2bb9e5cda904c05ed1
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647543"
---
# <a name="odata-orderby-syntax-in-azure-search"></a>Syntaxe $orderby OData v Azure Search

 Pomocí [parametru **$OrderBy** OData](query-odata-filter-orderby-syntax.md) můžete použít vlastní pořadí řazení pro výsledky hledání v Azure Search. V tomto článku se podrobně popisuje syntaxe **$OrderBy** . Obecnější informace o tom, jak používat **$OrderBy** při prezentaci výsledků hledání, najdete [v tématu Jak pracovat s výsledky hledání v Azure Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Syntaxe

Parametr **$OrderBy** přijímá čárkami oddělený seznam až 32 **klauzulí ORDER by**. Syntaxe klauzule ORDER by je popsána následující EBNF ([rozšířený formulář Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

K dispozici je také diagram interaktivní syntaxe:

> [!div class="nextstepaction"]
> [Diagram syntaxe OData pro Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Kompletní EBNF najdete v referenčních informacích k [syntaxi výrazu OData pro Azure Search](search-query-odata-syntax-reference.md) .

Každá klauzule má kritéria řazení, volitelně následované směrem řazení (`asc` pro vzestupné nebo `desc` sestupné). Pokud nezadáte směr, výchozí hodnota je vzestupné. Kritéria řazení mohou být buď cesta `sortable` k poli, nebo volání [`geo.distance`](search-query-odata-geo-spatial-functions.md) [`search.score`](search-query-odata-search-score-function.md) funkcí nebo.

Pokud má více dokumentů stejné kritérium řazení a funkce se `search.score` nepoužívá (například pokud řadíte podle číselného `Rating` pole a tři dokumenty mají hodnocení 4), bude počet vazeb v sestupném pořadí rozdělen podle skóre dokumentu. Pokud jsou skóre dokumentu stejné (například když v žádosti není zadán dotaz fulltextového vyhledávání), relativní řazení vázaných dokumentů je neurčité.

Můžete zadat více kritérií řazení. Pořadí výrazů Určuje konečné pořadí řazení. Například pro řazení sestupně podle skóre, které následuje hodnocení, by měla být `$orderby=search.score() desc,Rating desc`syntaxe.

Syntaxe pro `geo.distance` v **$OrderBy** je stejná jako v **$Filter**. Při použití `geo.distance` v **$OrderBy**musí pole, na které se vztahuje, být typu `Edm.GeographyPoint` a musí být `sortable`také.

Syntaxe pro `search.score` v **$OrderBy** je `search.score()`. Funkce `search.score` nepřijímá žádné parametry.

## <a name="examples"></a>Příklady

Seřadit hotely vzestupně podle základní sazby:

    $orderby=BaseRate asc

Seřadit hotely sestupně podle hodnocení a potom vzestupně podle základní sazby (Pamatujte, že výchozí hodnota je vzestupné):

    $orderby=Rating desc,BaseRate

Seřadit hotely sestupně podle hodnocení a vzestupné vzdálenosti od daných souřadnic:

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

Seřaďte hotely v sestupném pořadí podle hledání. skóre a hodnocení a potom ve vzestupném pořadí podle vzdálenosti od daných souřadnic. Mezi dvěma hotely se stejnou relevancí a hodnocením, nejbližší je uvedeno jako první:

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Další postup  

- [Jak pracovat s výsledky hledání v Azure Search](search-pagination-page-layout.md)
- [Přehled jazyka výrazů OData pro Azure Search](query-odata-filter-orderby-syntax.md)
- [Referenční dokumentace syntaxe výrazu OData pro Azure Search](search-query-odata-syntax-reference.md)
- [Hledat dokumenty &#40;Azure Search REST API služby&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)

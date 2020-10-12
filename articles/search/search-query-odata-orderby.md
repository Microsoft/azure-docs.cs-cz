---
title: Řazení OData podle odkazu
titleSuffix: Azure Cognitive Search
description: Referenční dokumentace ke syntaxem a jazykům pro použití pořadí na základě dotazů v Azure Kognitivní hledání.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/05/2020
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
ms.openlocfilehash: 83ab2c6b97435ace0d2bc508cbf522600391b60b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88926826"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>Syntaxe $orderby OData v Azure Kognitivní hledání

 Pomocí [parametru **$OrderBy** OData](query-odata-filter-orderby-syntax.md) můžete pro výsledky hledání ve službě Azure kognitivní hledání použít vlastní pořadí řazení. V tomto článku se podrobně popisuje syntaxe **$OrderBy** . Obecnější informace o tom, jak používat **$OrderBy** při prezentaci výsledků hledání, najdete v tématu [jak pracovat s výsledky hledání v Azure kognitivní hledání](search-pagination-page-layout.md).

## <a name="syntax"></a>Syntax

Parametr **$OrderBy** přijímá čárkami oddělený seznam až 32 **klauzulí ORDER by**. Syntaxe klauzule ORDER by je popsána následující EBNF ([rozšířený formulář Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

K dispozici je také diagram interaktivní syntaxe:

> [!div class="nextstepaction"]
> [Diagram syntaxe OData pro Azure Kognitivní hledání](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Kompletní EBNF najdete v článku [referenční informace k syntaxi výrazu OData pro Azure kognitivní hledání](search-query-odata-syntax-reference.md) .

Každá klauzule má kritéria řazení, volitelně následované směrem řazení ( `asc` pro vzestupné nebo `desc` sestupné). Pokud nezadáte směr, výchozí hodnota je vzestupné. Pokud jsou v poli hodnoty null, zobrazí se nejprve hodnoty null, pokud je řazení `asc` a poslední, pokud je řazení `desc` .

Kritéria řazení mohou být buď cesta k `sortable` poli, nebo volání [`geo.distance`](search-query-odata-geo-spatial-functions.md) [`search.score`](search-query-odata-search-score-function.md) funkcí nebo.

Pokud má více dokumentů stejné kritérium řazení a funkce se `search.score` nepoužívá (například pokud řadíte podle číselného `Rating` pole a tři dokumenty mají hodnocení 4), bude počet vazeb v sestupném pořadí rozdělen podle skóre dokumentu. Pokud jsou skóre dokumentu stejné (například když v žádosti není zadán dotaz fulltextového vyhledávání), relativní řazení vázaných dokumentů je neurčité.

Můžete zadat více kritérií řazení. Pořadí výrazů Určuje konečné pořadí řazení. Například pro řazení sestupně podle skóre, které následuje hodnocení, by měla být syntaxe `$orderby=search.score() desc,Rating desc` .

Syntaxe pro `geo.distance` v **$OrderBy** je stejná jako v **$Filter**. Při použití `geo.distance` v **$OrderBy**musí pole, na které se vztahuje, být typu `Edm.GeographyPoint` a musí být také `sortable` .

Syntaxe pro `search.score` v **$OrderBy** je `search.score()` . Funkce `search.score` nepřijímá žádné parametry.

## <a name="examples"></a>Příklady

Seřadit hotely vzestupně podle základní sazby:

```odata-filter-expr
    $orderby=BaseRate asc
```

Seřadit hotely sestupně podle hodnocení a potom vzestupně podle základní sazby (Pamatujte, že výchozí hodnota je vzestupné):

```odata-filter-expr
    $orderby=Rating desc,BaseRate
```

Seřadit hotely sestupně podle hodnocení a vzestupné vzdálenosti od daných souřadnic:

```odata-filter-expr
    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc
```

Seřaďte hotely v sestupném pořadí podle hledání. skóre a hodnocení a potom ve vzestupném pořadí podle vzdálenosti od daných souřadnic. Mezi dvěma hotely se stejnou relevancí a hodnocením, nejbližší je uvedeno jako první:

```odata-filter-expr
    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc
```

## <a name="next-steps"></a>Další kroky  

- [Jak pracovat s výsledky hledání v Azure Kognitivní hledání](search-pagination-page-layout.md)
- [Přehled jazyka výrazů OData pro Azure Kognitivní hledání](query-odata-filter-orderby-syntax.md)
- [Referenční dokumentace syntaxe výrazu OData pro Azure Kognitivní hledání](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;Azure Kognitivní hledání REST API&#41;](/rest/api/searchservice/Search-Documents)
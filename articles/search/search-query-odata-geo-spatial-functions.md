---
title: OData – reference geoprostorové funkce – Azure Search
description: OData geoprostorové funkce, geo.distance a geo.intersects v dotazech Azure Search.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
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
ms.openlocfilehash: 0ce63ab1143c784eb3e10f47c20ef2b5034d63a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079792"
---
# <a name="odata-geo-spatial-functions-in-azure-search---geodistance-and-geointersects"></a>Geoprostorové funkce OData ve službě Azure Search - `geo.distance` a `geo.intersects`

Služba Azure Search podporuje geoprostorové dotazy v [výrazů filtru OData](query-odata-filter-orderby-syntax.md) prostřednictvím `geo.distance` a `geo.intersects` funkce. `geo.distance` Funkce vrací vzdálenost v kilometrech mezi dvěma body, jeden je pole nebo proměnné rozsahu a z nich bude konstantu předanou v rámci filtru. `geo.intersects` Vrací funkce `true` Pokud časovém okamžiku v daném mnohoúhelníku, kde je bod je proměnná pole nebo rozsahu a mnohoúhelník je zadána jako konstanta předanou v rámci filtru.

`geo.distance` Funkci můžete použít také v [ **$orderby** parametr](search-query-odata-orderby.md) seřadit výsledky hledání podle vzdálenost od daného bodu. Syntaxe pro `geo.distance` v **$orderby** je stejný jako v **$filter**. Při použití `geo.distance` v **$orderby**, ke kterému se vztahuje pole musí být typu `Edm.GeographyPoint` a musí být také **seřaditelné**.

## <a name="syntax"></a>Syntaxe

Následující EBNF ([Extended Backus-Naur – formulář](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiku `geo.distance` a `geo.intersects` funkce, jakož i geoprostorové hodnoty, na kterých pracují:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*
```

Diagramu interaktivní syntaxe je také k dispozici:

> [!div class="nextstepaction"]
> [Diagram syntaxe OData pro službu Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> Zobrazit [referenční příručka syntaxe výrazů OData pro službu Azure Search](search-query-odata-syntax-reference.md) pro dokončení EBNF.

### <a name="geodistance"></a>geo.distance

`geo.distance` Funkce přebírá dva parametry typu `Edm.GeographyPoint` a vrátí `Edm.Double` hodnotu, která je vzdálenost v kilometrech. Tím se liší od jiných služeb, které podporují geoprostorového operace OData, které obvykle vrací vzdálenost v metrech.

Jeden z parametrů k `geo.distance` musí být konstanta bodu zeměpisné oblasti a další musí být pole Cesta (nebo proměnnou rozsahu v případě filtr iterování přes pole typu `Collection(Edm.GeographyPoint)`). Pořadí těchto parametrů nezáleží.

Konstanta bodu zeměpisné oblasti je ve formátu `geography'POINT(<longitude> <latitude>)'`, kde zeměpisná délka a šířka jsou číselné konstanty.

> [!NOTE]
> Při použití `geo.distance` ve filtru musí porovnat vzdálenost vrácené funkcí s použitím konstantní `lt`, `le`, `gt`, nebo `ge`. Operátory `eq` a `ne` nejsou podporovány při porovnávání vzdálenosti. To je třeba správné použití `geo.distance`: `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`.

### <a name="geointersects"></a>geo.intersects

`geo.intersects` Funkce přebírá proměnnou typu `Edm.GeographyPoint` a konstantu `Edm.GeographyPolygon` a vrátí `Edm.Boolean`  --  `true` v mezích mnohoúhelníku, pokud je bod `false` jinak.

Mnohoúhelník je dvojrozměrné povrch ukládá jako posloupnost body definující ohraničující prstenec (najdete v článku [příklady](#examples) níže). Mnohoúhelník musí zavřou, což znamená, první a poslední bod sad musí být stejné. [Bodů mnohoúhelníku musí být v pořadí proti směru hodinových ručiček](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Geoprostorové dotazy a pokrývání uzlů 180th poledníků mnohoúhelníky

Pro mnoho geoprostorového dotaz knihovny formulování dotazu, který zahrnuje 180th poledníků (téměř datová hranice) je buď off-limits nebo vyžaduje alternativní řešení, jako je například rozdělení na dva na obou stranách poledníky mnohoúhelníku.

Ve službě Azure Search, geoprostorové dotazy, které obsahují zeměpisnou délku 180 stupňů bude fungovat podle očekávání, pokud je obdélníkový tvar dotazu a vaše souřadnice zarovnají k rozložení mřížky podél zeměpisnou šířku a délku (například `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`). V opačném případě pro neobdélníkových nebo nezarovnaných tvary, zvažte rozdělení mnohoúhelníku přístup.  

### <a name="geo-spatial-functions-and-null"></a>Geoprostorové funkce a `null`

Všechna ostatní pole bez kolekce ve službě Azure Search, pole typu, jako jsou `Edm.GeographyPoint` může obsahovat `null` hodnoty. Když Azure Search zpracuje `geo.intersects` pro pole, která je `null`, výsledek bude vždy `false`. Chování `geo.distance` v tomto případě závisí na kontextu:

- Filtry `geo.distance` z `null` výsledky v pole `null`. To znamená, že dokument nebude odpovídat, protože `null` ve srovnání s všechny nenulovou hodnotu, vyhodnotí `false`.
- Při řazení výsledků za použití **$orderby**, `geo.distance` z `null` výsledky v maximální možné vzdálenost pole. Dokumenty se toto pole bude zatříděna nižší, než všechny ostatní při řazení `asc` je používat (výchozí) a větší než ostatní, pokud je směr `desc`.

## <a name="examples"></a>Příklady

### <a name="filter-examples"></a>Příklady filtrů

Najít všechny hotely v rámci 10 kilometrů bodu daného odkazu (kde je pole s typem umístění `Edm.GeographyPoint`):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

Najít všechny hotely v rámci daného zobrazení popsány mnohoúhelníku (kde je pole s typem umístění `Edm.GeographyPoint`). Všimněte si, že je uzavřena mnohoúhelníku (první a poslední bod sad musí být stejná) a [body musí být uvedeny v pořadí proti směru hodinových ručiček](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Příklady klauzule ORDER by

Seřadit sestupně podle hotels `rating`, pak vzestupně podle vzdálenosti od dané souřadnice:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

Seřadit v sestupném pořadí podle hotels `search.score` a `rating`a pak ve vzestupném pořadí podle vzdálenosti od dané souřadnice tak, aby mezi dva hotels s identické hodnocení na ten nejbližší je uvedená jako první:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Další postup  

- [Filtry ve službě Azure Search](search-filters.md)
- [Přehled o výraz jazyka OData pro službu Azure Search](query-odata-filter-orderby-syntax.md)
- [Reference k syntaxi výrazů OData pro službu Azure Search](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;rozhraní REST API služby Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)

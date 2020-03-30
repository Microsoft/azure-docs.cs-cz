---
title: Odkaz na geoprostorovou funkci OData
titleSuffix: Azure Cognitive Search
description: Syntaxe a referenční dokumentace pro použití Geo-spatial funkce OData, geo.distance a geo.intersects, v Azure Cognitive Search dotazy.
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
ms.openlocfilehash: 902996c1813931638012c78f81bd65c400bee7a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113177"
---
# <a name="odata-geo-spatial-functions-in-azure-cognitive-search---geodistance-and-geointersects"></a>Geoprostorové funkce OData v `geo.distance` Azure Cognitive Search – a`geo.intersects`

Azure Cognitive Search podporuje geoprostorové dotazy ve [výrazech filtru OData](query-odata-filter-orderby-syntax.md) prostřednictvím funkcí `geo.distance` a. `geo.intersects` Funkce `geo.distance` vrátí vzdálenost v kilometrech mezi dvěma body, z toho jedna je proměnná pole nebo rozsahu a jedna konstanta předávaná jako součást filtru. Funkce `geo.intersects` vrátí, `true` pokud je daný bod v daném polygu, kde bod je proměnná pole nebo rozsah a polygon je určen jako konstanta předaná jako součást filtru.

Funkci `geo.distance` lze také použít v [ **parametru $orderby** ](search-query-odata-orderby.md) k seřazení výsledků hledání podle vzdálenosti od daného bodu. Syntaxe `geo.distance` pro in **$orderby** je stejná jako v **$filter**. Při `geo.distance` použití v **$orderby**musí být pole , `Edm.GeographyPoint` na které se vztahuje, typu a musí být také **seřaditelné**.

> [!NOTE]
> Při `geo.distance` použití v **$orderby** parametru musí pole, které předáte funkci, obsahovat pouze jeden geobod. Jinými slovy, musí být `Edm.GeographyPoint` typu `Collection(Edm.GeographyPoint)`a ne . Není možné řadit na sběrných polích v Azure Cognitive Search.

## <a name="syntax"></a>Syntaxe

Následující EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) `geo.distance` definuje `geo.intersects` gramatiku a funkcí, stejně jako geoprostorové hodnoty, na kterých pracují:

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

K dispozici je také interaktivní syntaktický diagram:

> [!div class="nextstepaction"]
> [Syntaxový diagram OData pro Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> Viz [odkaz na syntaxi výrazu OData pro Azure Cognitive Search](search-query-odata-syntax-reference.md) pro kompletní EBNF.

### <a name="geodistance"></a>geo.vzdálenost

Funkce `geo.distance` přebírá dva parametry `Edm.GeographyPoint` typu `Edm.Double` a vrátí hodnotu, která je vzdálenost mezi nimi v kilometrech. To se liší od jiných služeb, které podporují OData geoprostorové operace, které obvykle vrátit vzdálenosti v metrech.

Jedním z parametrů `geo.distance` musí být konstanta bodu zeměpisné oblasti a druhá musí být cesta pole (nebo proměnná rozsahu v `Collection(Edm.GeographyPoint)`případě iterace filtru nad polem typu ). Na pořadí těchto parametrů nezáleží.

Bodová konstanta zeměpisu je ve tvaru `geography'POINT(<longitude> <latitude>)'`, kde zeměpisná délka a zeměpisná šířka jsou číselné konstanty.

> [!NOTE]
> Při `geo.distance` použití ve filtru je nutné porovnat vzdálenost vrácenou `lt`funkcí `le` `gt`s `ge`konstantou používající , , , nebo . Operátory `eq` `ne` a nejsou podporovány při porovnávání vzdáleností. Například se jedná o `geo.distance`správné `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`použití : . .

### <a name="geointersects"></a>geo.intersects

Funkce `geo.intersects` přebírá proměnnou `Edm.GeographyPoint` typu a `Edm.GeographyPolygon` konstantu `Edm.Boolean`  --  `true` a vrátí, pokud je bod v mezích mnohonožku, `false` jinak.

Mnohočinek je dvojrozměrný povrch uložený jako posloupnost bodů definujících ohraničovací kroužek (viz [příklady](#examples) níže). Polygon musí být uzavřen, což znamená, že první a poslední sady bodů musí být stejné. [Body v polygonu musí být v pořadí proti směru hodinových ručiček](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Geoprostorové dotazy a polygony zahrnující 180.

Pro mnoho knihoven geografických prostorových dotazů, které formulují dotaz, který obsahuje 180. poledník (poblíž poledníku), je buď zakázané, nebo vyžaduje řešení, například rozdělení mnohotvaru na dvě, jednu na obou stranách poledníku.

V Azure Cognitive Search geoprostorové dotazy, které zahrnují 180 stupňů zeměpisné délky bude fungovat podle očekávání, `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`pokud je tvar dotazu obdélníkový a vaše souřadnice zarovnat rozložení mřížky podél zeměpisné délky a šířky (například ). V opačném případě u nepravoúhlých nebo nezarovnaných obrazců zvažte přístup k rozdělení polygonu.  

### <a name="geo-spatial-functions-and-null"></a>Geoprostorové funkce a`null`

Stejně jako všechna ostatní pole bez sběru `Edm.GeographyPoint` v `null` Azure Cognitive Search, pole typu může obsahovat hodnoty. Když Azure Cognitive `geo.intersects` Search vyhodnotí `null`pole, které `false`je , výsledek bude vždy . Chování `geo.distance` v tomto případě závisí na kontextu:

- Ve filtrech `geo.distance` `null` výsledkem `null`pole je . To znamená, že dokument `null` se neshoduje, protože ve `false`srovnání s libovolnou hodnotou bez hodnoty null se vyhodnotí .
- Při řazení výsledků **$orderby**pomocí `geo.distance` $orderby `null` má pole maximální možnou vzdálenost. Dokumenty s takovým polem budou při použití `asc` směru řazení (výchozí) seřazeny níže `desc`než všechny ostatní a při použití směru vyšší než všechny ostatní.

## <a name="examples"></a>Příklady

### <a name="filter-examples"></a>Příklady filtrů

Najděte všechny hotely do vzdálenosti 10 km od daného `Edm.GeographyPoint`referenčního bodu (kde se nachází pole typu ):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

Najděte všechny hotely v daném výřezu popsaném jako polygon (kde je umístění typu `Edm.GeographyPoint`pole). Všimněte si, že polygon je uzavřen (první a poslední sady bodů musí být stejné) a [body musí být uvedeny v pořadí proti směru hodinových ručiček](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Příklady objednávek

Seřaďte `rating`hotely sestupující podle , pak stoupání podle vzdálenosti od daných souřadnic:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

Seřaďte hotely `search.score` `rating`v sestupném pořadí podle a nastupujícípořadí podle vzdálenosti od daných souřadnic tak, aby mezi dvěma hotely se stejným hodnocením byl nejbližší uveden jako první:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Další kroky  

- [Filtry v Azure Cognitive Search](search-filters.md)
- [Přehled jazyka OData pro Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Odkaz na syntaxi výrazu OData pro Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;azure kognitivní vyhledávání rozhraní API rozhraní&#41;API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)

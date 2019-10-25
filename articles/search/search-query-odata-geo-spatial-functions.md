---
title: Referenční dokumentace funkce OData geografického prostoru
titleSuffix: Azure Cognitive Search
description: V Azure Kognitivní hledání dotazy jsou geografické funkce OData geograficky. Distance a geo. intersects.
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
ms.openlocfilehash: 09034423e16c652cf6994b38f8d92574abc0ce55
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793341"
---
# <a name="odata-geo-spatial-functions-in-azure-cognitive-search---geodistance-and-geointersects"></a>Geografické prostorové funkce OData v Azure Kognitivní hledání-`geo.distance` a `geo.intersects`

Azure Kognitivní hledání podporuje geografické prostorové dotazy ve [výrazech filtru OData](query-odata-filter-orderby-syntax.md) prostřednictvím funkcí `geo.distance` a `geo.intersects`. Funkce `geo.distance` vrací vzdálenost v kilometrech mezi dvěma body, jedno je pole nebo proměnná rozsahu a jedna je konstanta předaná jako součást filtru. Funkce `geo.intersects` vrátí `true`, pokud je daný bod v rámci daného mnohoúhelníku, kde je bod pole nebo proměnná rozsahu a mnohoúhelník je zadán jako součást filtru jako konstanta.

Funkci `geo.distance` lze také použít v [parametru **$OrderBy** ](search-query-odata-orderby.md) k řazení výsledků hledání podle vzdálenosti od daného bodu. Syntaxe pro `geo.distance` v **$OrderBy** je stejná jako v **$Filter**. Při použití `geo.distance` v **$OrderBy**musí být pole, na které se vztahuje, typu `Edm.GeographyPoint` a musí být také možné je **Seřadit**.

> [!NOTE]
> Při použití `geo.distance` v parametru **$OrderBy** musí pole, které předáte do funkce, obsahovat pouze jeden geografický bod. Jinými slovy, musí být typu `Edm.GeographyPoint` a nesmí `Collection(Edm.GeographyPoint)`. Pro pole kolekce v Azure Kognitivní hledání není možné řadit.

## <a name="syntax"></a>Syntaxe

Následující EBNF ([rozšířený formulář Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definuje gramatiku funkcí `geo.distance` a `geo.intersects` a také geografické hodnoty, na kterých pracují:

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

K dispozici je také diagram interaktivní syntaxe:

> [!div class="nextstepaction"]
> [Diagram syntaxe OData pro Azure Kognitivní hledání](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> Kompletní EBNF najdete v článku [referenční informace k syntaxi výrazu OData pro Azure kognitivní hledání](search-query-odata-syntax-reference.md) .

### <a name="geodistance"></a>Geografická vzdálenost

Funkce `geo.distance` přebírá dva parametry typu `Edm.GeographyPoint` a vrací `Edm.Double`ou hodnotu, která představuje vzdálenost mezi nimi v kilometrech. To se liší od jiných služeb podporujících geografické prostorové operace OData, které obvykle vracejí vzdálenosti v metrech.

Jedním z parametrů `geo.distance` musí být konstanta geografického bodu a druhá musí být cesta k poli (nebo proměnná rozsahu v případě, že je v případě filtru iterace nad polem typu `Collection(Edm.GeographyPoint)`). Pořadí těchto parametrů nezáleží.

Konstanta geografického bodu má `geography'POINT(<longitude> <latitude>)'`formuláře, kde Zeměpisná délka a zeměpis je číselné konstanty.

> [!NOTE]
> Při použití `geo.distance` ve filtru je nutné porovnat vzdálenost vrácenou funkcí s konstantou pomocí `lt`, `le`, `gt`nebo `ge`. Operátory `eq` a `ne` nejsou podporovány při porovnávání vzdálenosti. Jedná se například o správné použití `geo.distance`: `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`.

### <a name="geointersects"></a>geografické. průniky

Funkce `geo.intersects` přebírá proměnnou typu `Edm.GeographyPoint` a konstantní `Edm.GeographyPolygon` a vrací `Edm.Boolean` -- `true`, pokud je bod uvnitř hranice mnohoúhelníku, `false` jinak.

Mnohoúhelník je dvourozměrná plocha uložená jako sekvence bodů definujících ohraničující prstenec (viz [Příklady](#examples) níže). Mnohoúhelník musí být uzavřen, což znamená, že první a poslední bod musí být stejné. [Body v mnohoúhelníku musí být v pořadí zprava doleva](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Geografické prostorové dotazy a mnohoúhelníky, které pokrývá 180th poledníku

V mnoha geograficky integrovaných knihovnách dotazů je vyjednaný dotaz, který obsahuje 180th poledníku (poblíž datová hranice (), je buď vypnutý, nebo vyžaduje alternativní řešení, například rozdělení mnohoúhelníku na dvě, jednu na obě strany poledníku.

V Azure Kognitivní hledání geografické prostorové dotazy, které zahrnují 180, budou fungovat podle očekávání, pokud je obrazec dotazu obdélníkový a vaše souřadnice jsou zarovnané na rozložení mřížky podél zeměpisné délky a šířky (například `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`). V opačném případě u nepravoúhlých nebo nezarovnanéch tvarů zvažte přístup k rozdělenému mnohoúhelníku.  

### <a name="geo-spatial-functions-and-null"></a>Geografické funkce a `null`

Stejně jako všechna ostatní pole bez kolekce v Azure Kognitivní hledání můžou pole typu `Edm.GeographyPoint` obsahovat `null` hodnoty. Když Azure Kognitivní hledání vyhodnotí `geo.intersects` pro pole, které je `null`, bude výsledek vždycky `false`. Chování `geo.distance` v tomto případě závisí na kontextu:

- Ve filtrech `geo.distance` `null` pole výsledkem `null`. To znamená, že se dokument neshoduje, protože `null` v porovnání s hodnotou, která není null, je vyhodnocena jako `false`.
- Při řazení výsledků pomocí **$orderby**`geo.distance` pole `null` má za následek maximální možnou vzdálenost. Dokumenty s takovým polem budou seřazeny menší než všechny ostatní, pokud je použit směr řazení `asc` (výchozí nastavení) a větší než všechny ostatní, pokud je směr `desc`.

## <a name="examples"></a>Příklady

### <a name="filter-examples"></a>Příklady filtru

Najde všechny hotely v rámci 10 kilometrů daného referenčního bodu (kde umístění je pole typu `Edm.GeographyPoint`):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

Najde všechny hotely v rámci daného zobrazení popsané jako mnohoúhelník (umístění je pole typu `Edm.GeographyPoint`). Všimněte si, že mnohoúhelník je uzavřený (nastavení prvního a posledního bodu musí být stejné) a [body musí být uvedené v pořadí zprava doleva](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Příklady pořadí

Seřadit hotely sestupně podle `rating`a vzestupné vzdálenosti od daných souřadnic:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

Seřaďte hotely v sestupném pořadí podle `search.score` a `rating`a pak ve vzestupném pořadí podle vzdálenosti od daných souřadnic, takže mezi dvěma hotely a identickým hodnocením je nejbližší uveden jako první:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Další kroky  

- [Filtry v Azure Kognitivní hledání](search-filters.md)
- [Přehled jazyka výrazů OData pro Azure Kognitivní hledání](query-odata-filter-orderby-syntax.md)
- [Referenční dokumentace syntaxe výrazu OData pro Azure Kognitivní hledání](search-query-odata-syntax-reference.md)
- [Hledání dokumentů &#40;Azure kognitivní hledání REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)

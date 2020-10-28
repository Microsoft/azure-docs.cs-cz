---
title: Rozšířená geometriía pro injson | Mapy Microsoft Azure
description: Přečtěte si, jak Azure Maps rozšiřuje specifikace pro injson tak, aby zahrnovala další geometrické obrazce. Podívejte se na příklady, které nastaví kružnice a obdélníky pro použití v mapách.
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 87a5dec2582796c6703bfb37b76e26e53a1192c0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895779"
---
# <a name="extended-geojson-geometries"></a>Rozšířená geometriía pro injson

Azure Maps poskytuje seznam výkonných rozhraní API pro vyhledávání uvnitř a podél geografických funkcí. Tato rozhraní API jsou v souladu se standardními [specifikacemi][1] geografického formátu JSON, které představují geografické funkce.  

[Specifikace INjson][1] pro geometrií podporuje pouze následující:

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Vyberte
* Mnohoúhelník

Některá Azure Maps rozhraní API akceptují geometrií, která nejsou součástí [specifikace pro injson][1]. Například [vyhledávání uvnitř geometrie](/rest/api/maps/search/postsearchinsidegeometry) API přijímá kružnice a mnohoúhelníky.

Tento článek poskytuje podrobné vysvětlení způsobu, jakým Azure Maps rozšiřuje [specifikace][1] pro geometrií, aby představoval konkrétní.

## <a name="circle"></a>Kruh

`Circle`Geometrie není podporována [specifikací biojson][1]. `GeoJSON Point Feature`K reprezentaci kruhu slouží objekt.

`Circle`Geometrie reprezentovaná pomocí `GeoJSON Feature` objektu __musí__ obsahovat následující souřadnice a vlastnosti:

- Střed

    Střed kružnice je reprezentován pomocí `GeoJSON Point` objektu.

- RADIUS

    Kruh `radius` je reprezentován pomocí `GeoJSON Feature` vlastností. Hodnota poloměru je v _metrech_ a musí být typu `double` .

- Podtyp

    Geometrie kruhu musí také obsahovat `subType` vlastnost. Tato vlastnost musí být součástí `GeoJSON Feature` vlastností a její hodnota by měla být _Circle_ .

#### <a name="example"></a>Příklad

Tady je postup, jak znázornit kruh pomocí `GeoJSON Feature` objektu. Prostředte si kruh v šířce: 47,639754 a zeměpisná délka:-122,126986 a přiřaďte mu poloměr, který se rovná 100 měřičů:

```json            
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}          
```

## <a name="rectangle"></a>Obdélník

`Rectangle`Geometrie není podporována [specifikací biojson][1]. `GeoJSON Polygon Feature`K reprezentaci obdélníku používáme objekt. Rozšíření Rectangle je primárně používáno modulem nástrojů pro kreslení sady web SDK.

`Rectangle`Geometrie reprezentovaná pomocí `GeoJSON Polygon Feature` objektu __musí__ obsahovat následující souřadnice a vlastnosti:

- Roh

    Rohy obdélníku jsou reprezentovány pomocí souřadnic `GeoJSON Polygon` objektu. Mělo by existovat pět souřadnic, jeden pro každý roh. A, pátou souřadnici, která je shodná s první souřadnicí, pro zavření kroužku mnohoúhelníku. Předpokládá se, že tyto souřadnice budou zarovnané a že je vývojář může otočit tak, jak je chtěli.

- Podtyp

    Geometrie obdélníku musí také obsahovat `subType` vlastnost. Tato vlastnost musí být součástí `GeoJSON Feature` vlastností a její hodnota by měla být _Rectangle_ .

### <a name="example"></a>Příklad

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Polygon",
        "coordinates": [[[5,25],[14,25],[14,29],[5,29],[5,25]]]
    },
    "properties": {
        "subType": "Rectangle"
    }
}

```
## <a name="next-steps"></a>Další kroky

Další informace o datech o injson v Azure Maps:

> [!div class="nextstepaction"]
> [Formát geografického geografického formátu](geofence-geojson.md)

Projděte si Glosář běžných technických podmínek spojených s Azure Maps a inteligentními aplikacemi lokátoru:

> [!div class="nextstepaction"]
> [Glosář služby Azure Maps](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946
---
title: Rozšířená geometriía pro injson | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak Microsoft Azure Maps rozšiřuje specifikaci geometrií tak, aby představovala určitá.
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 75ad83411edfdfe7545e8f80df17fea56e317ee0
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911639"
---
# <a name="extended-geojson-geometries"></a>Rozšířená geometriía pro injson

Azure Maps poskytuje seznam výkonných rozhraní API pro vyhledávání v rámci geografických funkcí.
Tato rozhraní API se standardizací podle [specifikace geografické JSON][1] pro reprezentaci geografických funkcí (například hranice stavu, trasy).  

[Specifikace injson][1] pro geometrií podporuje pouze následující:

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Vyberte
* Mnohoúhelník

Některá Azure Maps rozhraní API (například: [Vyhledat uvnitř geometrie](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)) přijměte geometrií jako "Circle", které nejsou součástí [specifikace injson][1].

Tento článek poskytuje podrobné vysvětlení způsobu, jakým Azure Maps rozšiřuje [specifikace][1] pro geometrií, aby představoval konkrétní.

## <a name="circle"></a>Kruh

Geometrie `Circle` není podporována [specifikací injson][1]. K reprezentaci kruhu používáme objekt `GeoJSON Point Feature`.

`Circle` geometrie reprezentovaná pomocí objektu `GeoJSON Feature` __musí__ obsahovat následující:

- Center

    Střed kružnice je reprezentován pomocí objektu `GeoJSON Point`.

- Poloměr

    `radius` kružnice je reprezentovaná pomocí vlastností `GeoJSON Feature`. Hodnota poloměru je v _metrech_ a musí být typu `double`.

- SubType

    Geometrie kruh také musí obsahovat vlastnost `subType`. Tato vlastnost musí být součástí vlastností `GeoJSON Feature`a její hodnota by měla být _Circle_ .

#### <a name="example"></a>Příklad:

Tady je postup, jak znázornit kruh na střed (zeměpisná šířka: 47,639754, zeměpisná délka:-122,126986) s poloměrem větším než 100 měřičů pomocí objektu `GeoJSON Feature`:

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

Geometrie `Rectangle` není podporována [specifikací injson][1]. K reprezentaci obdélníku používáme objekt `GeoJSON Polygon Feature`. Rozšíření Rectangle je primárně používáno modulem nástrojů pro kreslení sady web SDK.

`Rectangle` geometrie reprezentovaná pomocí objektu `GeoJSON Polygon Feature` __musí__ obsahovat následující:

- Roh

    Rohy obdélníku jsou reprezentovány pomocí souřadnic objektu `GeoJSON Polygon`. Mělo by se jednat o pět souřadnic, jeden pro každý roh a pátou souřadnici, která je stejná jako první, aby se zavřel kroužek mnohoúhelníku. Předpokládá se, že tyto souřadnice budou zarovnány a otočeny požadovaným vývojářem.

- SubType

    Geometrie obdélníku musí také obsahovat vlastnost `subType`. Tato vlastnost musí být součástí vlastností `GeoJSON Feature`a její hodnota by měla být _Rectangle_ .

### <a name="example"></a>Příklad:

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
> [Glosář Azure Maps](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946

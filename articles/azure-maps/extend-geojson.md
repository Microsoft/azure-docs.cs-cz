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
ms.openlocfilehash: 98db10f0fc7a417f39d4bb00e77af6bdea034a03
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79276397"
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
* Postupně

Některá Azure Maps rozhraní API akceptují geometrií, která nejsou součástí [specifikace pro injson][1]. Například [vyhledávání uvnitř geometrie](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) API přijímá kružnice a mnohoúhelníky.

Tento článek poskytuje podrobné vysvětlení způsobu, jakým Azure Maps rozšiřuje [specifikace][1] pro geometrií, aby představoval konkrétní.

## <a name="circle"></a>Kruh

Geometrie `Circle` není podporována [specifikací injson][1]. K reprezentaci kruhu používáme objekt `GeoJSON Point Feature`.

Geometrie `Circle` reprezentovaná pomocí objektu `GeoJSON Feature` __musí__ obsahovat následující souřadnice a vlastnosti:

- Center

    Střed kružnice je reprezentován pomocí objektu `GeoJSON Point`.

- Zaoblen

    `radius` kružnice je reprezentovaná pomocí vlastností `GeoJSON Feature`. Hodnota poloměru je v _metrech_ a musí být typu `double`.

- SubType

    Geometrie kruhu musí také obsahovat vlastnost `subType`. Tato vlastnost musí být součástí vlastností `GeoJSON Feature`a její hodnota by měla být _Circle_ .

#### <a name="example"></a>Příklad

Tady je postup, jak znázornit kruh pomocí objektu `GeoJSON Feature`. Prostředte si kruh v šířce: 47,639754 a zeměpisná délka:-122,126986 a přiřaďte mu poloměr, který se rovná 100 měřičů:

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

## <a name="rectangle"></a>Plocha

Geometrie `Rectangle` není podporována [specifikací injson][1]. K reprezentaci obdélníku používáme objekt `GeoJSON Polygon Feature`. Rozšíření Rectangle je primárně používáno modulem nástrojů pro kreslení sady web SDK.

Geometrie `Rectangle` reprezentovaná pomocí objektu `GeoJSON Polygon Feature` __musí__ obsahovat následující souřadnice a vlastnosti:

- Roh

    Rohy obdélníku jsou reprezentovány pomocí souřadnic objektu `GeoJSON Polygon`. Mělo by existovat pět souřadnic, jeden pro každý roh. A, pátou souřadnici, která je shodná s první souřadnicí, pro zavření kroužku mnohoúhelníku. Předpokládá se, že tyto souřadnice budou zarovnané a že je vývojář může otočit tak, jak je chtěli.

- SubType

    Geometrie obdélníku musí také obsahovat vlastnost `subType`. Tato vlastnost musí být součástí vlastností `GeoJSON Feature`a její hodnota by měla být _Rectangle_ .

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
> [Glosář Azure Maps](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946

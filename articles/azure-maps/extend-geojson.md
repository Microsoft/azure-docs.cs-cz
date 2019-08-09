---
title: Rozšířené geometriíy pro injson v Azure Maps | Microsoft Docs
description: Naučte se, jak roztáhnout geometrií v injson v Azure Maps
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 52325248d21a5d5112c9a7f9497c3e03fdf102a4
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881982"
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
* Postupně

Některá Azure Maps rozhraní API (například: [Hledat v geometrii](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)) Přijměte geometrií jako "Circle", které nejsou součástí [specifikace injson][1].

Tento článek poskytuje podrobné vysvětlení způsobu, jakým Azure Maps rozšiřuje [specifikace][1] pro geometrií, aby představoval konkrétní.

## <a name="circle"></a>Kruh

Geometrie není podporována specifikací biojson. [][1] `Circle` K reprezentaci `GeoJSON Point Feature` kruhu slouží objekt.

Geometrie reprezentovaná `GeoJSON Feature` pomocí objektu __musí__ obsahovat následující: `Circle`

- Na střed

    Střed kružnice je reprezentován pomocí `GeoJSON Point` objektu.

- Radius

    Kruh `radius` je reprezentován pomocí `GeoJSON Feature`vlastností. Hodnota poloměru je v _metrech_ a musí být typu `double`.

- SubType

    Geometrie kruh také musí obsahovat vlastnost `subType`. Tato vlastnost musí být součástí `GeoJSON Feature`vlastností a její hodnota by měla být _Circle_ .

#### <a name="example"></a>Příklad

Tady je postup, jak znázornit kruh na střed (zeměpisná šířka: 47,639754, zeměpisná délka:-122,126986) s poloměrem rovnající se 100 měřiči pomocí `GeoJSON Feature` objektu:

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

Geometrie není podporována specifikací biojson. [][1] `Rectangle` K reprezentaci `GeoJSON Polygon Feature` obdélníku používáme objekt. Rozšíření Rectangle je primárně používáno modulem nástrojů pro kreslení sady web SDK.

Geometrie reprezentovaná `GeoJSON Polygon Feature` pomocí objektu __musí__ obsahovat následující: `Rectangle`

- Roh

    Rohy obdélníku jsou reprezentovány pomocí souřadnic `GeoJSON Polygon` objektu. Mělo by se jednat o pět souřadnic, jeden pro každý roh a pátou souřadnici, která je stejná jako první, aby se zavřel kroužek mnohoúhelníku. Předpokládá se, že tyto souřadnice budou zarovnány a otočeny požadovaným vývojářem.

- SubType

    Geometrie obdélníku musí také obsahovat `subType` vlastnost. Tato vlastnost musí být součástí `GeoJSON Feature`vlastností a její hodnota by měla být _Rectangle_ .

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
## <a name="next-steps"></a>Další postup

Další informace o datech o injson v Azure Maps:

> [!div class="nextstepaction"]
> [Formát geografického geografického formátu](geofence-geojson.md)

Projděte si Glosář běžných technických podmínek spojených s Azure Maps a inteligentními aplikacemi lokátoru:

> [!div class="nextstepaction"]
> [Glosář Azure Maps](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946

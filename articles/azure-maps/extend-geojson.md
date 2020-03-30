---
title: Rozšířené geometrie GeoJSON | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak Mapy Microsoft Azure rozšiřují spec GeoJSON tak, aby reprezentovaly určité geometrie.
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 98db10f0fc7a417f39d4bb00e77af6bdea034a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276397"
---
# <a name="extended-geojson-geometries"></a>Rozšířené geometrie GeoJSON

Azure Maps poskytuje seznam výkonných rozhraní API pro vyhledávání v geografických funkcích a podél nich. Tato api se řídí [standardním specifikací GeoJSON][1] představujících geografické funkce.  

[Spec GeoJSON][1] podporuje pouze následující geometrie:

* Geometrycollection
* Linestring
* Multilinestring
* Multipoint
* Multipolygon
* Bod
* Mnohoúhelník

Některá rozhraní API Map Azure přijímají geometrie, které nejsou součástí [specifikace GeoJSON][1]. Například [rozhraní API pro hledání uvnitř geometrie](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) přijímá circle a mnohonoly.

Tento článek obsahuje podrobné vysvětlení, jak Azure Maps rozšiřuje [geojson specifikace][1] představují určité geometrie.

## <a name="circle"></a>Kruh

Geometrie `Circle` není podporována [specifikací GeoJSON][1]. Používáme `GeoJSON Point Feature` objekt k reprezentaci kruhu.

Geometrie `Circle` reprezentovaná objektem `GeoJSON Feature` __musí__ obsahovat následující souřadnice a vlastnosti:

- Střed

    Střed kruhu je reprezentován `GeoJSON Point` pomocí objektu.

- Poloměr

    Kruh `radius` je reprezentován `GeoJSON Feature`pomocí vlastností společnosti . Hodnota poloměru je v _metrech_ `double`a musí být typu .

- Podtypu

    Geometrie kruhu musí `subType` také obsahovat vlastnost. Tato vlastnost musí být `GeoJSON Feature`součástí vlastností společnosti a její hodnota by měla být _Circle._

#### <a name="example"></a>Příklad

Zde je návod, jak budete reprezentovat `GeoJSON Feature` kruh pomocí objektu. Vystředíme kruh v zeměpisné šířce: 47.639754 a zeměpisné délky: -122.126986 a přiřadíme mu poloměr rovnající se 100 metrům:

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

Geometrie `Rectangle` není podporována [specifikací GeoJSON][1]. Používáme `GeoJSON Polygon Feature` objekt k reprezentaci obdélníku. Rozšíření obdélníku je primárně používáno modulem kreslicích nástrojů sady Web SDK.

Geometrie `Rectangle` reprezentovaná objektem `GeoJSON Polygon Feature` __musí__ obsahovat následující souřadnice a vlastnosti:

- Rohy

    Rohy obdélníku jsou reprezentovány pomocí `GeoJSON Polygon` souřadnic objektu. Mělo by tam být pět souřadnic, jeden pro každý roh. A pátá souřadnice, která je stejná jako první souřadnice, zavřete polygonový kroužek. Předpokládá se, že tyto souřadnice zarovnat a že vývojář může otočit je podle potřeby.

- Podtypu

    Geometrie obdélníku `subType` musí také obsahovat vlastnost. Tato vlastnost musí být `GeoJSON Feature`součástí vlastností společnosti a její hodnota by měla být _Rectangle_

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

Další informace o datech GeoJSON v Mapách Azure:

> [!div class="nextstepaction"]
> [Geofence GeoJSON formát](geofence-geojson.md)

Projděte si glosář běžných technických termínů spojených s Mapami Azure a aplikacemi pro zjišťování polohy:

> [!div class="nextstepaction"]
> [Glosář služby Azure Maps](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946

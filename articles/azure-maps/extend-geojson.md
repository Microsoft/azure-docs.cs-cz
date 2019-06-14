---
title: Rozšíření GeoJSON geometrie ve službě Azure Maps | Dokumentace Microsoftu
description: Zjistěte, jak rozšířit GeoJSON geometrie ve službě Azure Maps
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: be3c31951c4721a861f9239c5220419dec11b6bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60799152"
---
# <a name="extending-geojson-geometries"></a>Rozšíření GeoJSON geometrie

Služba Azure Maps poskytuje seznam výkonné rozhraní API pro hledání vnitřního/podél zeměpisné funkce.
Tato rozhraní API používají jako standard [specifikaci GeoJSON] [ 1] představující geografické funkce (například: stav hranice, trasy).  

[Specifikaci GeoJSON] [ 1] podporuje pouze následující geometrie:

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Bod
* Mnohoúhelník

Některá rozhraní API Azure Maps (například: [Hledání uvnitř geometrie](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)) přijměte geometrie jako "Kruh", které nejsou součástí [specifikaci GeoJSON][1].

Tento článek poskytuje podrobné vysvětlení na tom, jak Azure Maps rozšiřuje [specifikaci GeoJSON] [ 1] k reprezentaci některých geometrie.

### <a name="circle"></a>Kruh

`Circle` Geometrie není podporována [specifikaci GeoJSON][1]. Používáme `GeoJSON Feature` objekt reprezentující kruh.

A `Circle` vyjadřuje geometrie `GeoJSON Feature` objekt __musí__ obsahovat následující:

1. Na střed
   >Středu kruhu je reprezentována pomocí `GeoJSON Point` typu.

2. Radius
   >Na kruh `radius` se vyjadřuje `GeoJSON Feature`jeho vlastnosti. Hodnota radius _měřiče_ a musí být typu `double`.

3. SubType
   >Geometrie kruh také musí obsahovat vlastnost `subType`. Tato vlastnost musí být součástí vlastnosti `GeoJSON Feature`a její hodnota by měla být _Circle_


#### <a name="example"></a>Příklad:

Zde je, jak budete představují zarovnaný na střed v kruhu (zeměpisná šířka: 47.639754, zeměpisná délka:-122.126986) poloměru rovna 100 měřiče využití, používání `GeoJSON Feature` objektu:

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

[1]: https://tools.ietf.org/html/rfc7946

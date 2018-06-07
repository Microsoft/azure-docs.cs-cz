---
title: Rozšíření GeoJSON geometrie v rámci služby Azure Maps | Microsoft Docs
description: Zjistěte, jak rozšířit GeoJSON geometrie v rámci služby Azure Maps
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 2cc0e29615ad4fc19040055d847435a9dffa9c95
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654794"
---
# <a name="extending-geojson-geometries"></a>Rozšíření GeoJSON geometrie

Azure Maps obsahuje seznam výkonné rozhraní API pro vyhledávání uvnitř nebo podél zeměpisné funkce.
Tato rozhraní API standardizovat na [GeoJSON specifikace] [ 1] pro představující funkce zeměpisné (například: stav hranice, trasy).  

[GeoJSON specifikace] [ 1] podporuje pouze následující geometrie:

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* bod
* mnohoúhelníku

Některé rozhraní API map Azure (například: [vyhledávání uvnitř geometrie](https://docs.microsoft.com/en-us/rest/api/maps/search/postsearchinsidegeometry)) přijměte geometrie jako "Kruh", které nejsou součástí [GeoJSON specifikace][1].

Tento článek obsahuje podrobné vysvětlení na tom, jak Azure mapy rozšiřuje [GeoJSON specifikace] [ 1] představují určité geometrie.

### <a name="circle"></a>kruhu.

`Circle` Geometrie není podporována [GeoJSON specifikace][1]. Používáme `GeoJSON Feature` objekt představující kruh.

A `Circle` geometrie reprezentovaný pomocí `GeoJSON Feature` objekt __musí__ obsahovat následující:

1. Na střed
   >Na kruh center je reprezentována pomocí `GeoJSON Point` typu.

2. Radius
   >Na kruh `radius` je reprezentována pomocí `GeoJSON Feature`na vlastnosti. Hodnota poloměru je v _měřidla_ a musí být typu `double`.

3. Podtyp
   >Kruh geometry, musí také obsahovat `subType` vlastnost. Tato vlastnost musí být součástí `GeoJSON Feature`na vlastnosti a jeho hodnota by měla být _kruhu._


#### <a name="example"></a>Příklad:

Zde je, jak budete představují kruh zarovnaný na střed v (šířky: 47.639754, zeměpisné délky:-122.126986) o poloměru rovna 100 měřidla, pomocí `GeoJSON Feature` objektu:

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

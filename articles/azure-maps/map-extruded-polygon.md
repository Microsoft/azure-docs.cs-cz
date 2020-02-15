---
title: Přidání vrstvy vysunutí mnohoúhelníku na mapu | Mapy Microsoft Azure
description: Postup přidání vrstvy vysunutí mnohoúhelníku do webové sady SDK Microsoft Azure Maps.
author: farah-alyasari
ms.author: v-faalya
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 38a7e6c5f4c139343e735ae82616c11a224ae7ca
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209677"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>Přidání vrstvy vysunutí mnohoúhelníku na mapu

V tomto článku se dozvíte, jak použít vrstvu vysunutí mnohoúhelníku k vykreslení oblastí `Polygon` a `MultiPolygon` funkcí geometrií jako vytlačené obrazce. Sada Azure Maps Web SDK podporuje vykreslování geometrií kroužků, jak je definováno v [rozšířeném schématu](extend-geojson.md#circle)pro zobrazení. JSON. Tyto kroužky je možné transformovat na mnohoúhelníky při vykreslování na mapě. Všechny funkce geometrií se můžou snadno aktualizovat při zabalení pomocí nástroje [Atlas. Třída Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)

## <a name="use-a-polygon-extrusion-layer"></a>Použití vrstvy vysunutí mnohoúhelníku

Připojte [vrstvu vysunutí mnohoúhelníku](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest) ke zdroji dat. Pak ho načtete na mapě. Vrstva vysunutí mnohoúhelníku vykresluje oblasti `Polygon` a `MultiPolygon` funkcí jako vytlačené obrazce. Vlastnosti `height` a `base` vrstvy vysunutí mnohoúhelníku definují základní vzdálenost od základu a výšky vytlačeného obrazce v **měřičích**. Následující kód ukazuje, jak vytvořit mnohoúhelník, přidat ho do zdroje dat a vykreslit pomocí třídy vrstev vytlačení mnohoúhelníku.

> [!Note]
> Hodnota `base` definovaná ve vrstvě vytlačení mnohoúhelníku by měla být menší nebo rovna hodnotě `height`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vytlačený mnohoúhelník" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>vytlačený mnohoúhelník</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.</iframe>


## <a name="add-data-driven-polygons"></a>Přidat mnohoúhelníky řízené daty

Mapu kartogramy lze vykreslit pomocí vrstvy vysunutí mnohoúhelníku. Nastavte `height` a vlastnosti `fillColor` vrstvy vytlačení na měření statistické proměnné v `Polygon` a `MultiPolygon` funkcí geometrií. Následující ukázka kódu ukazuje vytlačenou mapu kartogramyu U. S na základě měření hustoty populace podle stavu.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vytlačená mapa kartogramy" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Viz Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>v <a href='https://codepen.io/azuremaps/pen/eYYYNox'>mapě vytlačené</a> perem.
</iframe>

## <a name="add-a-circle-to-the-map"></a>Přidání kruhu k mapě

Azure Maps používá rozšířenou verzi schématu injson, která poskytuje definici pro kruhy, jak je uvedeno [zde](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle). Vytlačený kruh lze na mapě vykreslit vytvořením funkce `point` s vlastností `subType` `Circle` a hodnotou číslovaného `Radius`, která představuje poloměr v **metrech**. Příklad:

```Javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-105.203135, 39.664087]
    },
    "properties": {
        "subType": "Circle",
        "radius": 1000
    }
} 
```

Azure Maps webová sada SDK převede tyto `Point` funkce na `Polygon` funkce v digestoři. Tyto funkce `Point` lze na mapě vykreslovat pomocí vrstvy vytlačení mnohoúhelníku, jak je znázorněno v následujícím příkladu kódu.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mnohoúhelník prostorů pro pomocí dronů" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Prohlédněte si Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>v oblasti <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>vzdušného prostoru</a> pera na pomocí dronů.
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>Přizpůsobení vrstvy vysunutí mnohoúhelníku

Vrstva vytlačení mnohoúhelníku má několik možností stylů. Tady je nástroj pro jejich vyzkoušení.

<br/>

<iframe height='700' scrolling='no' title='PoogBRJ' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na Azure Maps <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ</a> pera na CodePen (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'></a>.
</iframe>

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Mnohoúhelník](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [vrstva vysunutí mnohoúhelníku](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

Další zdroje informací:

> [!div class="nextstepaction"]
> [Azure Maps rozšíření pro injson Specification](extend-geojson.md#circle)

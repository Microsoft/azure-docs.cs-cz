---
title: Přidat mnohoúhelníkovou vrstvu k mapě | Mapy Microsoft Azure
description: Naučte se přidávat mnohoúhelníky nebo kružnice do map. Podívejte se, jak používat Azure Maps Web SDK k přizpůsobení geometrických tvarů a usnadnění jejich aktualizace a údržby.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 74c8ba4ed1f228b9b3ba90b46c47f538d71ff409
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91310454"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Přidat mnohoúhelníkovou vrstvu k mapě

V tomto článku se dozvíte, jak vykreslovat oblasti `Polygon` a `MultiPolygon` funkce geometrií na mapě pomocí mnohoúhelníkové vrstvy. Sada Azure Maps Web SDK také podporuje vytváření geometrií kroužků, jak je definováno v [rozšířeném schématu pro stanovení biojson](extend-geojson.md#circle). Tyto kružnice jsou transformované na mnohoúhelníky při vykreslování na mapě. Všechny funkce geometrií se dají snadno aktualizovat při zabalení do nástroje [Atlas. Třída Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape)

## <a name="use-a-polygon-layer"></a>Použít mnohoúhelníkovou vrstvu 

Když je mnohoúhelníková vrstva připojená ke zdroji dat a načte se na mapě, vykreslí oblast s `Polygon` funkcemi a `MultiPolygon` . Chcete-li vytvořit mnohoúhelník, přidejte ho do zdroje dat a vykreslete ho pomocí mnohoúhelníkové vrstvy pomocí třídy [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer) .

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a rectangular polygon.
dataSource.add(new atlas.data.Feature(
    new atlas.data.Polygon([[
        [-73.98235, 40.76799],
        [-73.95785, 40.80044],
        [-73.94928, 40.7968],
        [-73.97317, 40.76437],
        [-73.98235, 40.76799]
    ]])
));

//Create and add a polygon layer to render the polygon to the map, below the label layer.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    fillOpacity: 0.7
}), 'labels');
```

Níže je uvedená úplná a spuštěná ukázka výše uvedeného kódu.

<br/>

<iframe height='500' scrolling='no' title='Přidání mnohoúhelníku k mapě ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero a <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>přidejte mnohoúhelník na mapu </a> pomocí Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Použít mnohoúhelník a čáru vrstev společně

Spojnicová vrstva se používá k vykreslení obrysu mnohoúhelníků. Následující ukázka kódu vykreslí mnohoúhelník jako předchozí příklad, ale nyní přidá řádkovou vrstvu. Tato vrstva čáry je druhá vrstva připojená ke zdroji dat.  

<br/>

<iframe height='500' scrolling='no' title='Mnohoúhelníková a Spojnicová vrstva pro přidání mnohoúhelníku' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Pokud <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>chcete přidat mnohoúhelník</a> podle Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>, podívejte se na mnohoúhelník a vrstvu čáry.
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Naplnění mnohoúhelníku vzorem

Kromě naplnění mnohoúhelníku barvou můžete mnohoúhelník vyplnit pomocí vzorku obrázku. Načtěte vzorek obrázku do prostředků mapy Sprite obrázku a pak na tento obrázek odkázat pomocí `fillPattern` vlastnosti mnohoúhelníkové vrstvy.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vzorek výplně mnohoúhelníku" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Pomocí Azure Maps () na CodePen se podívejte na <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>tvar výplně mnohoúhelníku</a> pera <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'>CodePen</a>
</iframe>


> [!TIP]
> Sada Azure Maps Web SDK nabízí několik přizpůsobitelných šablon obrázků, které můžete použít jako vzory výplní. Další informace najdete v dokumentu [použití šablon obrázků](how-to-use-image-templates-web-sdk.md) .

## <a name="customize-a-polygon-layer"></a>Přizpůsobení vrstvy mnohoúhelníku

Vrstva mnohoúhelníku má pouze několik možností stylů. Tady je nástroj pro jejich vyzkoušení.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na Azure Maps <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> pera <a href='https://codepen.io/azuremaps'>@azuremaps</a> na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Přidání kruhu k mapě

Azure Maps používá rozšířenou verzi schématu injson, která poskytuje definici pro kruhy, jak je uvedeno [zde](extend-geojson.md#circle). Kruh se na mapě vykreslí vytvořením `Point` funkce. `Point`Má `subType` vlastnost s hodnotou `"Circle"` a `radius` vlastností s číslem, které představuje poloměr v metrech. 

```javascript
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

Rozhraní Azure Maps Web SDK tyto `Point` funkce převede na `Polygon` funkce. Tyto funkce se pak vykreslují na mapě pomocí mnohoúhelníku a vrstev čáry, jak je znázorněno v následujícím příkladu kódu.

<br/>

<iframe height='500' scrolling='no' title='Přidání kruhu k mapě' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero a <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>přidejte do mapy</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>Usnadnění aktualizace geometrie

`Shape`Třída obaluje [geometrii](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry) nebo [funkci](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature) a usnadňuje aktualizaci a údržbu těchto funkcí. Chcete-li vytvořit instanci proměnné tvaru, předejte geometrii nebo sadu vlastností konstruktoru tvaru.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

Následující ukázka kódu ukazuje, jak zabalit objekt informující o středníku objektu JSON s třídou Shape. Při změně hodnoty poloměru v obrazci se kroužek automaticky vykresluje na mapě.

<br/>

<iframe height='500' scrolling='no' title='Aktualizovat vlastnosti obrazce' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Prohlédněte si <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>vlastnosti obrazce aktualizace</a> pera podle Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Mnohoúhelník](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon)

> [!div class="nextstepaction"]
> [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)

Další příklady kódu pro přidání do map najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Vytvoření zdroje dat](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Přidání místního okna](map-add-popup.md)

> [!div class="nextstepaction"]
> [Použití výrazů pro styly založené na datech](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Používání šablon obrázků](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Přidání řádkové vrstvy](map-add-line-layer.md)

Další prostředky:

> [!div class="nextstepaction"]
> [Azure Maps rozšíření pro injson Specification](extend-geojson.md#circle)
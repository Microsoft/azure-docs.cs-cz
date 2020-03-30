---
title: Přidání vrstvy polygonu do mapy | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak vykreslit mnohonožku a více mnohonožců na mapě v sadě Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9191f054ca3c7374bcbc7bec46573289a512612c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535048"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Přidání vrstvy polygonu do mapy

Tento článek ukazuje, jak vykreslit `Polygon` oblasti a `MultiPolygon` geometrie prvků na mapě pomocí vrstvy polygonu. Sada Azure Maps Web SDK také podporuje vytváření geometrií Circle, jak je definováno v [rozšířeném schématu GeoJSON](extend-geojson.md#circle). Tyto kruhy jsou transformovány do polygonů při vykreslení na mapě. Všechny geometrie prvků lze snadno aktualizovat, když jsou zabaleny [atlasem. Třída tvarů.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)

## <a name="use-a-polygon-layer"></a>Použití vrstvy vícenoponů 

Když je vrstva polygonu připojena ke zdroji dat a načtena na mapě, vykreslí oblast s `Polygon` prvky a `MultiPolygon` prvky. Chcete-li vytvořit polygon, přidejte jej do zdroje dat a vykreslete jej pomocí vrstvy polygonu pomocí třídy [PolygonLayer.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

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

Níže je kompletní a spuštěný ukázka výše uvedeného kódu.

<br/>

<iframe height='500' scrolling='no' title='Přidání polygonu do mapy ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Přidat polygon na mapě</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Společné použití vrstvy polygonu a čáry

Čárová vrstva se používá k vykreslení obrysu polygonů. Následující ukázka kódu vykreslí mnohonožce jako v předchozím příkladu, ale nyní přidá řádkovou vrstvu. Tato vrstva čáry je druhá vrstva připojená ke zdroji dat.  

<iframe height='500' scrolling='no' title='Polygon a čárová vrstva pro přidání polygonu' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>pero polygon a liniové vrstvy přidat polygon</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Vyplnění polygonu vzorkem

Kromě vyplnění polygonu barvou můžete k vyplnění polygonu použít vzorek obrazu. Načtěte vzorek obrazu do zdrojů pohyblivosti obrázků `fillPattern` v mapách a pak na tento obraz nazvěte vlastnost vrstvy polygonu.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vzor náplně polygonu" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na vzorek náplně pera<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>polygon</a> podle Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> Webová sada Azure Maps SDK poskytuje několik přizpůsobitelných šablon bitových obrázků, které můžete použít jako vzorky výplně. Další informace naleznete v dokumentu [Jak používat šablony obrázků.](how-to-use-image-templates-web-sdk.md)

## <a name="customize-a-polygon-layer"></a>Přizpůsobení vrstvy polygonu

Vrstva mnohonožku má pouze několik možností stylů. Zde je nástroj, jak je vyzkoušet.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na Pen <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Přidání kruhu do mapy

Azure Maps používá rozšířenou verzi schématu GeoJSON, která poskytuje definici kruhů, jak je uvedeno [zde](extend-geojson.md#circle). Kruh je vykreslen na mapě vytvořením `Point` prvku. To `Point` má `subType` vlastnost s `"Circle"` hodnotou `radius` a vlastnost s číslem, které představuje poloměr v metrech. 

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

Sada Azure Maps Web SDK převádí tyto `Point` funkce na `Polygon` funkce. Potom tyto funkce jsou vykresleny na mapě pomocí polygona a čárové hladiny, jak je znázorněno v následujícím vzorku kódu.

<br/>

<iframe height='500' scrolling='no' title='Přidání kruhu do mapy' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero Přidat kruh<a href='https://codepen.io/azuremaps'>@azuremaps</a>na <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>mapu</a> pomocí Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>Snadné aktualizaci geometrie

Třída `Shape` zabalí [geometrii](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) nebo [prvek](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) a usnadňuje aktualizaci a údržbu těchto prvků. Chcete-li vytvořit instanci proměnné tvaru, předejte konstruktoru obrazce geometrii nebo sadu vlastností.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

Následující ukázka kódu ukazuje, jak obtékat kruh GeoJSON objekt s třídou tvaru. Když se hodnota poloměru mění ve tvaru, kružnice se automaticky vykresluje na mapě.

<br/>

<iframe height='500' scrolling='no' title='Aktualizovat vlastnosti obrazce' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>vlastnosti obrazce Aktualizace</a> pera podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách použitých v tomto článku:

> [!div class="nextstepaction"]
> [Mnohoúhelník](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Vrstva polygonu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Možnosti vícevrstvé vrstvy](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

Další příklady kódu, které chcete přidat do map, najdete v následujících článcích:

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
> [Rozšíření specifikace Azure Maps GeoJSON](extend-geojson.md#circle)
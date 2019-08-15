---
title: Přidat mnohoúhelníkovou vrstvu pro Azure Maps | Microsoft Docs
description: Jak přidat mnohoúhelníkovou vrstvu do Azure Maps webové sady SDK.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ca6c0f5e6fde5a31655ed17f4a016bf44216643f
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976144"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Přidat mnohoúhelníkovou vrstvu k mapě

V tomto článku se dozvíte, jak vykreslovat `Polygon` oblasti `MultiPolygon` a funkce geometrií na mapě pomocí mnohoúhelníkové vrstvy. Sada Azure Maps Web SDK také podporuje vytváření geometrií kroužků, jak je definováno v [rozšířeném schématu](extend-geojson.md#circle)pro stanovení biojson. Tyto kružnice jsou transformované na mnohoúhelníky při vykreslování na mapě. Všechny funkce geometrií je také možné snadno aktualizovat, pokud jsou zabaleny pomocí nástroje [Atlas. Třída Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)

## <a name="use-a-polygon-layer"></a>Použít mnohoúhelníkovou vrstvu 

Když je mnohoúhelníková vrstva připojena ke zdroji dat a načtena na mapě, vykreslí oblast `Polygon` funkcí a. `MultiPolygon` Následující kód ukazuje, jak vytvořit mnohoúhelník, přidat ho do zdroje dat a vykreslit ho pomocí mnohoúhelníkové vrstvy pomocí třídy [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) .

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

//Create and add a polygon layer to render the polygon to the map.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    opacaty: 0.5
}));
```

Níže je uvedená ukázka kompletního spuštění kódu výše uvedené funkce.

<br/>

<iframe height='500' scrolling='no' title='Přidání mnohoúhelníku k mapě ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero a <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>přidejte mnohoúhelník na mapu</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Použít mnohoúhelník a čáru vrstev společně

Spojnicová vrstva se dá použít k vykreslení obrysu mnohoúhelníků. Následující ukázka kódu vykreslí mnohoúhelník jako předchozí příklad, ale nyní přidá řádkovou vrstvu jako druhou vrstvu připojenou ke zdroji dat.  

<iframe height='500' scrolling='no' title='Mnohoúhelníková a Spojnicová vrstva pro přidání mnohoúhelníku' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Pokud chcete přidat mnohoúhelník podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>, podívejte se na <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>mnohoúhelník a vrstvu čáry</a> .
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Naplnění mnohoúhelníku vzorem

Kromě naplnění mnohoúhelníku barvou lze také použít vzor obrázku. Načtěte vzorek obrázku do prostředků mapy Sprite obrázku a pak na tento obrázek odkázat pomocí `fillPattern` vlastnosti mnohoúhelníkové vrstvy.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vzorek výplně mnohoúhelníku" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>se podívejte na <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>tvar výplně mnohoúhelníku</a> pera.
</iframe>


> [!TIP]
> Sada Azure Maps Web SDK nabízí několik přizpůsobitelných šablon obrázků, které můžete použít jako vzory výplní. Další informace najdete v dokumentu [použití šablon obrázků](how-to-use-image-templates-web-sdk.md) .

## <a name="customize-a-polygon-layer"></a>Přizpůsobení vrstvy mnohoúhelníku

Vrstva mnohoúhelníku má pouze několik možností stylů. Tady je nástroj pro jejich vyzkoušení.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/LXvxpg/'></a> Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>LXvxpg pera na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Přidání kruhu k mapě

Azure Maps používá rozšířenou verzi schématu injson, která poskytuje definici pro kruhy, jak je uvedeno [zde](extend-geojson.md#circle). Kruh lze na mapě `Point` vykreslit vytvořením funkce, která `subType` má `"Circle"` vlastnost s hodnotou a `radius` vlastností, která má číslo, které představuje poloměr v metrech. Příklad:

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

Azure Maps webová sada SDK převede tyto `Point` `Polygon` funkce na funkce v rámci pokrývání a lze je vykreslit na mapě pomocí mnohoúhelníkových a spojnicových vrstev, jak je znázorněno v následující ukázce kódu.

<br/>

<iframe height='500' scrolling='no' title='Přidání kruhu k mapě' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero a <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>přidejte do mapy</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>Usnadnění aktualizace geometrie

Třída obaluje geometrii nebo [funkci](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) a usnadňuje jejich aktualizaci a údržbu. [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) `Shape` Obrazec lze vytvořit předáním do geometrie a sady vlastností nebo předáním funkce, jak je znázorněno v následujícím kódu.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

Následující ukázka kódu ukazuje, jak zabalit objekt informující o středníku objektu JSON se třídou Shape a snadno aktualizovat vlastnost RADIUS pomocí posuvníku. Jak se změní hodnota poloměru v obrazci, vykreslování kružnice se automaticky aktualizuje na mapě.

<br/>

<iframe height='500' scrolling='no' title='Aktualizovat vlastnosti obrazce' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Prohlédněte si <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>vlastnosti obrazce aktualizace</a> pera podle Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další postup

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Mnohoúhelník](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

Další příklady kódu pro přidání do map najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Vytvoření zdroje dat](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Přidat automaticky otevírané okno](map-add-popup.md)

> [!div class="nextstepaction"]
> [Použití výrazů stylu založených na datech](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Používání šablon obrázků](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Přidat řádkovou vrstvu](map-add-line-layer.md)

Další prostředky:

> [!div class="nextstepaction"]
> [Azure Maps rozšíření pro injson Specification](extend-geojson.md#circle)
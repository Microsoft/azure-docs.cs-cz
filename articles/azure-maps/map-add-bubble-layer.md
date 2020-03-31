---
title: Přidání bublinové vrstvy do mapy | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak přidat vrstvu Bublina do mapy pomocí sady Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7ae11734eb804715f3eb1b5edcb02fc328dafec8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77208552"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Přidání bublinové vrstvy do mapy

Tento článek ukazuje, jak vykreslit data bodů ze zdroje dat jako bublinovou vrstvu na mapě. Vrstvy bublin vykreslují body jako kružnice na mapě s pevným poloměrem obrazových bodů. 

> [!TIP]
> Vrstvy bublin ve výchozím nastavení vykreslí souřadnice všech geometrií ve zdroji dat. Chcete-li vrstvu omezit tak, aby vykreslovala pouze prvky geometrie bodů, nastavte `filter` vlastnost hladiny na `['==', ['geometry-type'], 'Point']` nebo `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` pokud chcete zahrnout také prvky Vícebodového bodu.

## <a name="add-a-bubble-layer"></a>Přidání vrstvy bublin

Následující kód načte pole bodů do zdroje dat. Poté připojí datové body k [bublinové vrstvě](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest). Bublinová vrstva vykreslí poloměr každé bubliny pěti obrazovými body a barvou výplně bílou. A barva tahu modré a šířka tahu šest pixelů. 

```javascript
//Add point locations.
var points = [
    new atlas.data.Point([-73.985708, 40.75773]),
    new atlas.data.Point([-73.985600, 40.76542]),
    new atlas.data.Point([-73.985550, 40.77900]),
    new atlas.data.Point([-73.975550, 40.74859]),
    new atlas.data.Point([-73.968900, 40.78859])
];

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Add multiple points to the data source.
dataSource.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(dataSource, null, {
    radius: 5,
    strokeColor: "#4288f7",
    strokeWidth: 6, 
    color: "white" 
}));
```

Níže je kompletní ukázka spuštěného kódu výše uvedených funkcí.

<br/>

<iframe height='500' scrolling='no' title='Zdroj dat vrstvy bublin' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na Pero <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="show-labels-with-a-bubble-layer"></a>Zobrazení popisků s bublinovou vrstvou

Tento kód ukazuje, jak použít bublinovou vrstvu k vykreslení bodu na mapě. A jak použít vrstvu symbolu k vykreslení popisku. Chcete-li skrýt ikonu vrstvy `image` symbolů, nastavte `'none'`vlastnost voleb ikon na .

<br/>

<iframe height='500' scrolling='no' title='Vícevrstvý zdroj dat' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>vícevrstvé DataSource</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-bubble-layer"></a>Přizpůsobení bublinové vrstvy

Vrstva Bublina má pouze několik možností stylů. Zde je nástroj, jak je vyzkoušet.

<br/>

<iframe height='700' scrolling='no' title='Volby bublinové vrstvy' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>možnosti bublinové vrstvy</a> pera podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách použitých v tomto článku:

> [!div class="nextstepaction"]
> [Vrstva bublin](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Možnosti bublinové vrstvy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

Další ukázky kódu, které chcete přidat do map, naleznete v následujících článcích:

> [!div class="nextstepaction"]
> [Vytvoření zdroje dat](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy symbolů](map-add-pin.md)

> [!div class="nextstepaction"]
> [Použití výrazů pro styly založené na datech](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Ukázky kódu](https://docs.microsoft.com/samples/browse/?products=azure-maps)

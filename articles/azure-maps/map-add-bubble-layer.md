---
title: Přidat bublinovou vrstvu do mapy | Mapy Microsoft Azure
description: Naučte se vykreslovat body na mapách jako kruhy s pevnými velikostmi. Postup pro přidání a přizpůsobení bublinových vrstev pro tento účel najdete v tématu How to use the Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: cae29dcc0d334a2296199da0d8e3bc4562e275d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895324"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Přidání bublinové vrstvy do mapy

V tomto článku se dozvíte, jak vykreslovat data bodů ze zdroje dat jako bublinovou vrstvu na mapě. Bubliny vrstev vykreslí body jako kružnice na mapě s pevným poloměrem v pixelech. 

> [!TIP]
> Bublinové vrstvy ve výchozím nastavení budou kreslit souřadnice všech geometrií ve zdroji dat. Chcete-li omezit vrstvu tak, aby vykreslí pouze funkce geometrie bodu, nastavte `filter` vlastnost vrstvy na `['==', ['geometry-type'], 'Point']` nebo, `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` Pokud chcete zahrnout i funkce systému MultiPoint.

## <a name="add-a-bubble-layer"></a>Přidání vrstvy bublin

Následující kód načte pole bodů do zdroje dat. Datové body se pak připojí k [bublinové vrstvě](/javascript/api/azure-maps-control/atlas.layer.bubblelayer). Bublinová vrstva vykresluje poloměr každého bublinu s pěti obrazovými body a barvou výplně bílá. A, Barva tahu modrou a šířka tahu šest pixelů. 

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

Níže je uvedená ukázka kompletního spuštění kódu výše uvedené funkce.

<br/>

<iframe height='500' scrolling='no' title='BubbleLayer zdroj dat' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Pomocí Azure Maps () na CodePen si prohlédněte <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>zdroj dat pera BubbleLayer</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="show-labels-with-a-bubble-layer"></a>Zobrazit popisky s bublinovou vrstvou

Tento kód ukazuje, jak použít bublinovou vrstvu pro vykreslení bodu na mapě. A jak použít vrstvu symbolů pro vykreslení popisku. Chcete-li skrýt ikonu vrstvy symbolů, nastavte `image` vlastnost možností ikony na `'none'` .

<br/>

<iframe height='500' scrolling='no' title='MultiLayer zdroj dat' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Pomocí Azure Maps () na CodePen si prohlédněte <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>zdroj dat pera MultiLayer</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="customize-a-bubble-layer"></a>Přizpůsobení bublinové vrstvy

Bublinová vrstva má pouze několik možností stylu. Tady je nástroj pro jejich vyzkoušení.

<br/>

<iframe height='700' scrolling='no' title='Možnosti bublinové vrstvy' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>Možnosti vrstvy bublinového</a> pera podle Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [BubbleLayer](/javascript/api/azure-maps-control/atlas.layer.bubblelayer)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions)

Další ukázky kódu pro přidání do vašich map najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Vytvoření zdroje dat](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy symbolů](map-add-pin.md)

> [!div class="nextstepaction"]
> [Použití výrazů pro styly založené na datech](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Ukázky kódu](/samples/browse/?products=azure-maps)
---
title: Přidat bublinovou vrstvu do Azure Maps | Microsoft Docs
description: Postup přidání bublinové vrstvy do mapy JavaScriptu
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 516e4f35c88ae9c0e2d63e8a4ee40eb57c05ac29
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639035"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Přidání bublinové vrstvy do mapy

V tomto článku se dozvíte, jak můžete vykreslit data bodů ze zdroje dat jako bublinovou vrstvu na mapě. Bubliny vrstev vykreslí body jako kružnice na mapě s použitím poloměru s pevným pixelem. 

> [!TIP]
> Bublinové vrstvy ve výchozím nastavení budou kreslit souřadnice všech geometrií ve zdroji dat. Chcete-li omezit vrstvu tak, aby vykreslí pouze funkce geometrie bodu `filter` , nastavte vlastnost vrstvy na `['==', ['geometry-type'], 'Point']` nebo `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` , pokud chcete zahrnout i funkce systému MultiPoint.

## <a name="add-a-bubble-layer"></a>Přidání vrstvy bublin

<iframe height='500' scrolling='no' title='BubbleLayer zdroj dat' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>si prohlédněte <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>zdroj dat pera BubbleLayer</a> .
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt mapy. Pokyny najdete v tématu [vytvoření mapy](./map-create.md) .

V druhém bloku kódu je pole objektů [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) definováno a přidáno do objektu [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) .

[Bublinová vrstva](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) vykresluje data založená na bodech zabalená ve [zdroji dat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako kružnice na mapě. Poslední blok kódu vytvoří bublinovou vrstvu a přidá ji do mapy. Viz vlastnosti bublinové vrstvy na [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

Pole objektů Point, zdroj dat a bublinová vrstva jsou vytvořeny a přidány do mapy v rámci funkce [naslouchacího procesu události](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) , aby se zajistilo, že se kroužek zobrazí po úplném načtení mapy.

## <a name="show-labels-with-a-bubble-layer"></a>Zobrazit popisky s bublinovou vrstvou

<iframe height='500' scrolling='no' title='MultiLayer zdroj dat' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>si prohlédněte <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>zdroj dat pera MultiLayer</a> .
</iframe>

Výše uvedený kód ukazuje, jak vizualizovat a označovat data na mapě. První blok kódu výše sestaví objekt mapy. Pokyny najdete v tématu [vytvoření mapy](./map-create.md) .

Druhý blok kódu vytvoří objekt [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) . Potom vytvoří objekt zdroje dat pomocí třídy [zdroje dat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) a přidá bod do zdroje dat.

[Bublinová vrstva](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) vykresluje data založená na bodech zabalená ve [zdroji dat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako kružnice na mapě. Třetí blok kódu vytvoří bublinovou vrstvu a přidá ji do mapy. Viz vlastnosti bublinové vrstvy na [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

[Symbolová vrstva](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) používá text nebo ikony pro vykreslení dat na základě bodu zabalených ve [zdroji dat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako symboly na mapě. Poslední blok kódu vytvoří a přidá na mapu symbolovou vrstvu, která vykreslí textový popisek pro bublinu. Viz Vlastnosti vrstvy symbolů na [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions).

Zdroj dat a vrstvy jsou vytvořeny a přidány do mapy v rámci funkce [naslouchacího procesu události](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) , aby se zajistilo, že se data zobrazí po úplném načtení mapy.

## <a name="customize-a-bubble-layer"></a>Přizpůsobení bublinové vrstvy

Bublinová vrstva má pouze několik možností stylu. Tady je nástroj pro jejich vyzkoušení.

<br/>

<iframe height='700' scrolling='no' title='Možnosti bublinové vrstvy' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>Možnosti vrstvy bublinového</a> pera podle<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

Další ukázky kódu pro přidání do vašich map najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Přidat vrstvu symbolů](map-add-pin.md)

> [!div class="nextstepaction"]
> [Použití výrazů stylu založených na datech](data-driven-style-expressions-web-sdk.md)
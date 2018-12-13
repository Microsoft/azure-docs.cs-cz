---
title: Přidat vrstvu bublinu ke službě Azure Maps | Dokumentace Microsoftu
description: Jak přidat vrstvu bublin mapy jazyka Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 895f6ce728ce608184bf6f68be3b73d5dc384d79
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2018
ms.locfileid: "52892167"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Přidat vrstvu bublin k mapě

Tento článek popisuje, jak může vykreslit data bodu ze zdroje dat jako vrstvu na mapě bublinu. Bublinu vrstvy bodů takto kruhy na mapě s protokolem radius opravených pixelů. 

> [!TIP]
> Vykreslí souřadnice všechny geometrie ve zdroji dat se bubliny vrstvy ve výchozím nastavení. K omezení vrstvu tak, aby pouze vykreslí bod geometrie funkce set `filter` vlastnost vrstva `['==', '$type', 'Point']`

## <a name="add-a-bubble-layer"></a>Přidat vrstvu bublinový

<iframe height='500' scrolling='no' title='Zdroj dat BubbleLayer' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt Map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

V druhém bloku kódu, pole [bodu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) objekty je definovaný a přidán do [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) objektu.

A [bublinu vrstvy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) vykreslí zabalené v datového bodu [zdroj dat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako kruhy na mapě. Poslední blok kódu vytvoří bublinu vrstvy a přidá jej do mapy. Zobrazit vlastnosti vrstvy na bublinu [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

Pole objektů bodu, zdroje dat a vrstvy bublinu vytvořen a přidán do mapování v rámci [naslouchací proces událostí](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funkce, která se ujistěte, že po plně načtení mapy, zobrazí se kruh.

## <a name="show-labels-with-a-bubble-layer"></a>Zobrazit popisky s vrstvou bublinový

<iframe height='500' scrolling='no' title='Zdroj dat s více vrstvami' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>MultiLayer DataSource</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Výše uvedený kód se dozvíte, jak vizualizovat a popisku dat na mapě. První blok výše uvedený kód vytvoří objekt map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

Vytvoří druhý bloku kódu, [bodu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) objektu. Poté vytvoří objekt zdroje dat pomocí [zdroj dat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) třídy a přidá do zdroje dat je bod.

A [bublinu vrstvy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) vykreslí zabalené v datového bodu [zdroj dat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako kruhy na mapě. Třetí bloku kódu vytvoří bublinu vrstvy a přidá jej do mapy. Zobrazit vlastnosti vrstvy na bublinu [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

A [symbol vrstvy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) používá k vykreslení dat na základě bodu zabalené v textu nebo ikony [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako symboly na mapě. Poslední blok kódu vytvoří a přidává další vrstvu symbol, který vykreslí textový popisek pro bublin mapy. Zobrazit vlastnosti vrstvy symbolu v [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions).

Vytvořen a přidán do mapování v rámci zdroje dat a vrstvy [naslouchací proces událostí](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funkce, která se ujistěte, že data se zobrazí po plně načtení na mapě.

## <a name="customize-a-bubble-layer"></a>Přizpůsobení bublinu vrstvy

Vrstvy bublinu má jenom pár možností stylu. Zde je nástroj, který je vyzkoušet.

<br/>

<iframe height='700' scrolling='no' title='Možnosti bublinu vrstvy' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>bublinový možností vrstvy</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku:

> [!div class="nextstepaction"]
> [BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

Naleznete v následujících článcích pro další ukázky kódu pro přidání do vaše mapy:

> [!div class="nextstepaction"]
> [Přidat vrstvu symbol](./map-add-pin.md)
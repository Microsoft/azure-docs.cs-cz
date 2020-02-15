---
title: Přidání panelu nástrojů pro kreslení na mapu | Mapy Microsoft Azure
description: Postup přidání panelu nástrojů pro kreslení na mapu pomocí Azure Maps Web SDK
author: farah-alyasari
ms.author: v-faalya
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cb0f70bc42c9ac0f7026c910593950516f027a88
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209745"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>Přidání panelu nástrojů nástrojů pro kreslení na mapu

V tomto článku se dozvíte, jak používat modul nástrojů pro kreslení a jak zobrazit panel nástrojů kreslení na mapě. Ovládací prvek [DrawingToolbar](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) přidá panel nástrojů kreslení na mapě. Naučíte se, jak vytvářet mapy jenom s jedním a všemi nástroji pro kreslení a jak přizpůsobit vykreslování obrazových tvarů ve Správci výkresu.

## <a name="add-drawing-toolbar"></a>Přidání panelu nástrojů pro kreslení

Následující kód vytvoří instanci Správce výkresu a zobrazí panel nástrojů na mapě.

```Javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

Níže je uvedená ukázka kompletního běžícího kódu pro výše uvedené funkce:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Přidání panelu nástrojů pro kreslení" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Prohlédněte si <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>panel nástrojů přidat kreslení</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="limit-displayed-toolbar-options"></a>Možnosti zobrazeného panelu nástrojů omezení

Následující kód vytvoří instanci Správce výkresu a v mapě zobrazí panel nástrojů s pouhým kreslicím nástrojem mnohoúhelníku. 

```Javascript
//Create an instance of the drawing manager and display the drawing toolbar with polygon drawing tool.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'light',
            buttons: ["draw-polygon"]
        })
    });
```

Níže je uvedená ukázka kompletního běžícího kódu pro výše uvedené funkce:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Přidat kreslicí nástroj mnohoúhelníku" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Prohlédněte si pero <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>Přidání mnohoúhelníkového kreslicího nástroje</a> po Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="change-drawing-rendering-style"></a>Změnit styl vykreslování kresby

Následující kód získá vrstvy vykreslování ze Správce výkresu a upraví jejich možnosti pro změnu stylu vykreslování pro kreslení. V tomto případě budou body vykresleny s modrou ikonou značek. Čáry budou červené a čtyři pixelů na šířku. Mnohoúhelníky budou mít zelenou barvu výplně a oranžový obrys.

```Javascript
var layers = drawingManager.getLayers();
    layers.pointLayer.setOptions({
        iconOptions: {
            image: 'marker-blue'
        }
    });
    layers.lineLayer.setOptions({
        strokeColor: 'red',
        strokeWidth: 4
    });
    layers.polygonLayer.setOptions({
        fillColor: 'green'
    });
    layers.polygonOutlineLayer.setOptions({
        strokeColor: 'orange'
    });
```

Níže je uvedená ukázka kompletního běžícího kódu pro výše uvedené funkce:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Změnit styl vykreslování kresby" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>styl vykreslování kresby změnit</a> u Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Další kroky

Naučte se používat další funkce modulu nástrojů pro kreslení:

> [!div class="nextstepaction"]
> [Získat data obrazce](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Reagovat na události vykreslování](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Typy interakcí a klávesové zkratky](drawing-tools-interactions-keyboard-shortcuts.md)

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Panel nástrojů kreslení](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Správce výkresu](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

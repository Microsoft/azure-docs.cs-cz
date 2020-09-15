---
title: Přidat panel nástrojů pro kreslení na mapu | Mapy Microsoft Azure
description: Postup přidání panelu nástrojů pro kreslení na mapu pomocí Azure Maps Web SDK
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-javascript
ms.openlocfilehash: a156896faff4262d9b8bd3cef10817a5b919749d
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90086144"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>Přidání panelu nástrojů nástrojů pro kreslení na mapu

V tomto článku se dozvíte, jak používat modul nástrojů pro kreslení a jak zobrazit panel nástrojů kreslení na mapě. Ovládací prvek [DrawingToolbar](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar) přidá panel nástrojů kreslení na mapě. Naučíte se, jak vytvářet mapy jenom s jedním a všemi nástroji pro kreslení a jak přizpůsobit vykreslování obrazových tvarů ve Správci výkresu.

## <a name="add-drawing-toolbar"></a>Přidání panelu nástrojů pro kreslení

Následující kód vytvoří instanci Správce výkresu a zobrazí panel nástrojů na mapě.

```javascript
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

<iframe height="500" style="width: 100%;" scrolling="no" title="Přidání panelu nástrojů pro kreslení" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>panel nástrojů přidat kreslení</a> pomocí Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="limit-displayed-toolbar-options"></a>Možnosti zobrazeného panelu nástrojů omezení

Následující kód vytvoří instanci Správce výkresu a v mapě zobrazí panel nástrojů s pouhým kreslicím nástrojem mnohoúhelníku. 

```javascript
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

<iframe height="500" style="width: 100%;" scrolling="no" title="Přidat kreslicí nástroj mnohoúhelníku" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Prohlédněte si pero <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>Přidání mnohoúhelníkového kreslicího nástroje</a> pomocí Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="change-drawing-rendering-style"></a>Změnit styl vykreslování kresby

Styl nakreslených tvarů lze přizpůsobit načtením základních vrstev Správce výkresu pomocí `drawingManager.getLayers()` funkce a poté nastavením možností na jednotlivých vrstvách. Táhla přetažení, která se zobrazí pro souřadnice při úpravách obrazce, jsou značky HTML. Styl úchytů přetažení lze přizpůsobit předáním možností značek HTML do `dragHandleStyle` `secondaryDragHandleStyle` možností a Správce výkresu.  

Následující kód získá vrstvy vykreslování ze Správce výkresu a upraví jejich možnosti pro změnu stylu vykreslování pro kreslení. V tomto případě budou body vykresleny s modrou ikonou značek. Čáry budou červené a čtyři pixelů na šířku. Mnohoúhelníky budou mít zelenou barvu výplně a oranžový obrys. Poté změní styly úchytů pro přetahování na čtvercové ikony. 

```javascript
//Get rendering layers of drawing manager.
var layers = drawingManager.getLayers();

//Change the icon rendered for points.
layers.pointLayer.setOptions({
    iconOptions: {
        image: 'marker-blue'
    }
});

//Change the color and width of lines.
layers.lineLayer.setOptions({
    strokeColor: 'red',
    strokeWidth: 4
});

//Change fill color of polygons.
layers.polygonLayer.setOptions({
    fillColor: 'green'
});

//Change the color of polygon outlines.
layers.polygonOutlineLayer.setOptions({
    strokeColor: 'orange'
});

//Update the style of the drag handles that appear when editting.
drawingManager.setOptions({
    //Primary drag handle that represents coordinates in the shape.
    dragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="15" height="15" viewBox="0 0 15 15" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="15" height="15" style="stroke:black;fill:white;stroke-width:4px;"/></svg>',
        draggable: true
    },

    //Secondary drag hanle that represents mid-point coordinates that users can grab to add new cooridnates in the middle of segments.
    secondaryDragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="10" height="10" viewBox="0 0 10 10" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="10" height="10" style="stroke:white;fill:black;stroke-width:4px;"/></svg>',
        draggable: true
    }
});  
```

Níže je uvedená ukázka kompletního běžícího kódu pro výše uvedené funkce:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Změnit styl vykreslování kresby" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>styl vykreslování kresby změnit</a> u Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Další kroky

Naučte se používat další funkce modulu nástrojů pro kreslení:

> [!div class="nextstepaction"]
> [Získání dat tvaru](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Reagovat na události vykreslování](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Typy interakcí a klávesové zkratky](drawing-tools-interactions-keyboard-shortcuts.md)

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Panel nástrojů kreslení](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)

> [!div class="nextstepaction"]
> [Správce výkresu](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

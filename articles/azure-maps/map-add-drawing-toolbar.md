---
title: Přidání panelu nástrojů výkresu do mapy | Mapy Microsoft Azure
description: Jak přidat panel nástrojů výkresu do mapy pomocí sady Azure Maps Web SDK
author: philmea
ms.author: philmea
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: bebf1ddfbca3aec5a551193609381cf3510bc3ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334498"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>Přidání panelu nástrojů pro kreslení do mapy

V tomto článku se zobrazí postup použití modulu Nástroje kreslení a zobrazení panelu nástrojů výkresu na mapě. Ovládací [prvek DrawingToolbar](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) přidá panel nástrojů výkresu na mapě. Dozvíte se, jak vytvářet mapy pouze s jedním a všemi kreslicími nástroji a jak přizpůsobit vykreslování obrazců výkresů ve správci výkresů.

## <a name="add-drawing-toolbar"></a>Přidání panelu nástrojů pro kreslení

Následující kód vytvoří instanci správce výkresu a zobrazí panel nástrojů na mapě.

```javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

Níže je kompletní ukázka spuštěného kódu výše uvedených funkcí:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Přidání panelu nástrojů pro kreslení" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na panel nástrojů Přidání<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>pera</a> pomocí Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="limit-displayed-toolbar-options"></a>Omezit zobrazené volby panelu nástrojů

Následující kód vytvoří instanci správce výkresu a zobrazí panel nástrojů pouze s nástrojem pro kreslení mnohoměru na mapě. 

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

Níže je kompletní ukázka spuštěného kódu výše uvedených funkcí:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Přidání nástroje pro kreslení polygonů" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na nástroj <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>pro přidání vícenohého kreslicího nástroje</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="change-drawing-rendering-style"></a>Změna stylu vykreslení výkresu

Styl nakreslených obrazců lze přizpůsobit načtením podkladových hladin správce výkresu pomocí `drawingManager.getLayers()` funkce a nastavením voleb v jednotlivých hladinách. Táhla přetažení, která se při úpravách obrazce zobrazují pro souřadnice, jsou značky HTML. Styl úchytů lze přizpůsobit předáním voleb značek `dragHandleStyle` `secondaryDragHandleStyle` HTML do možností a správce výkresu.  

Následující kód získá hladiny vykreslování od správce výkresu a upraví jejich možnosti pro změnu stylu vykreslování pro kreslení. V takovém případě budou body vykresleny modrou ikonou značky. Čáry budou červené a čtyři pixely široké. Polygony budou mít zelenou barvu výplně a oranžový obrys. Potom změní styly úchyty přetažení na čtvercové ikony. 

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

Níže je kompletní ukázka spuštěného kódu výše uvedených funkcí:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Změna stylu vykreslení výkresu" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>styl vykreslování změna</a> pera podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Další kroky

Přečtěte si, jak používat další funkce modulu kreslicích nástrojů:

> [!div class="nextstepaction"]
> [Získání dat tvaru](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Reagovat na události vykreslování](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Typy interakcí a klávesové zkratky](drawing-tools-interactions-keyboard-shortcuts.md)

Další informace o třídách a metodách použitých v tomto článku:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Panel nástrojů Kreslení](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Správce výkresů](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

---
title: Modul kreslicích nástrojů | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak nastavit data možností kreslení pomocí sady Microsoft Azure Maps Web SDK
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 711609f9382e2153cbc738d544933796dbbe2e99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334315"
---
# <a name="use-the-drawing-tools-module"></a>Použití modulu nástrojů pro kreslení

Webová sada Azure Maps Web SDK poskytuje *modul kreslicích nástrojů*. Tento modul usnadňuje kreslení a úpravy obrazců na mapě pomocí vstupního zařízení, jako je například myš nebo dotyková obrazovka. Základní třídou tohoto modulu je [správce výkresů](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-). Správce výkresů poskytuje všechny možnosti potřebné k kreslení a úpravám obrazců na mapě. Lze jej použít přímo a je integrován s vlastním uzp. Můžete také použít předdefinovanou třídu [panelu nástrojů výkresu.](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>Načítání modulu kreslicích nástrojů na webové stránce

1. Vytvořte nový soubor HTML a [implementujte mapu jako obvykle](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).
2. Načtěte modul kreslicích nástrojů Azure Maps. Můžete jej načíst jedním ze dvou způsobů:
    - Použijte globálně hostovizované, Azure Content Delivery Network verze modulu služeb Azure Maps. Přidejte odkaz na šablonu stylů JavaScriptu a CSS v `<head>` prvku souboru:

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
        ```

    - Nebo můžete načíst modul kreslicích nástrojů pro zdrojový kód Azure Maps Web SDK místně pomocí balíčku [npm pro kreslení azure-maps-drawing-tools](https://www.npmjs.com/package/azure-maps-drawing-tools) a pak ho hostovat pomocí aplikace. Tento balíček také obsahuje definice jazyka TypeScript. Pomocí tohoto příkazu:
    
        > **npm nainstalovat azure-maps-kreslení-nástroje**
    
        Potom přidejte odkaz na šablonu stylů JavaScriptu a CSS do `<head>` prvku souboru:

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>Přímé použití správce výkresu

Po načtení modulu kreslicích nástrojů v aplikaci můžete povolit možnosti kreslení a úprav pomocí [správce výkresů](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-). Můžete určit volby pro správce výkresu při jeho vytváření `drawingManager.setOptions()` instancí nebo alternativně použít funkci.

### <a name="set-the-drawing-mode"></a>Nastavení režimu kreslení

Následující kód vytvoří instanci správce výkresu a nastaví možnost **režimu** kreslení. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

Níže uvedený kód je úplným spuštěným příkladem nastavení režimu kreslení správce výkresu. Kliknutím na mapu začněte kreslit polygon.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Nakreslení polygonu" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>nakreslit polygon</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="set-the-interaction-type"></a>Nastavení typu interakce

Správce výkresů podporuje tři různé způsoby interakce s mapou pro kreslení tvarů.

* `click`- Souřadnice jsou přidány, když kliknete myší nebo dotykem.
* `freehand `- Souřadnice jsou přidány, když je myš nebo dotek přetažen na mapě. 
* `hybrid`- Souřadnice jsou přidány, když myši nebo dotyku je klikli nebo přetáhli.

Následující kód umožňuje režim kreslení polygonů a nastavuje typ interakce `freehand`výkresu, který by měl správce výkresu dodržovat . 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

 Tento ukázkový kód implementuje funkce kreslení polygon na mapě. Stačí podržet levé tlačítko myši a volně ho přetáhnout.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Kreslení z volné ruky" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>pero volné</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>ruky kreslení pomocí Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="customizing-drawing-options"></a>Přizpůsobení možností výkresu

Předchozí příklady ukázaly, jak přizpůsobit možnosti výkresu při vytváření instancí Správce výkresů. Pomocí funkce můžete také nastavit možnosti Správce výkresů. `drawingManager.setOptions()` Níže je nástroj pro testování přizpůsobení všech možností pro správce výkresů pomocí funkce setOptions.

<br/>

<iframe height="685" title="Přizpůsobení správce výkresů" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>data obrazce Pero získat</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Další kroky

Přečtěte si, jak používat další funkce modulu kreslicích nástrojů:

> [!div class="nextstepaction"]
> [Přidání panelu nástrojů pro kreslení](map-add-drawing-toolbar.md)

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
> [Správce výkresů](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Panel nástrojů Kreslení](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

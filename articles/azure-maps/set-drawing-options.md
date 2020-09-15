---
title: Modul nástrojů pro kreslení | Mapy Microsoft Azure
description: V tomto článku se naučíte, jak nastavit data možností kreslení pomocí webové sady SDK Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-javascript
ms.openlocfilehash: 82c5d87be084e85b6de9f890bd042babca9df476
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90090495"
---
# <a name="use-the-drawing-tools-module"></a>Použití modulu nástrojů pro kreslení

Sada Azure Maps Web SDK poskytuje *modul nástrojů pro kreslení*. Tento modul usnadňuje vykreslování a úpravu obrazců na mapě pomocí vstupního zařízení, jako je například myš nebo dotyková obrazovka. Základní třídou tohoto modulu je [Správce výkresu](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#setoptions-drawingmanageroptions-). Správce kreslení poskytuje všechny schopnosti potřebné k vykreslování a úpravám obrazců na mapě. Dá se použít přímo a je integrovaný do vlastního uživatelského rozhraní panelu nástrojů. Můžete také použít integrovanou třídu nástrojů pro [Kreslení](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar) . 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>Načtení modulu nástrojů pro kreslení na webové stránce

1. Vytvořte nový soubor HTML a [implementujte mapování obvyklým způsobem](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).
2. Načtěte modul nástrojů Azure Maps Drawing Tools. Můžete ho načíst jedním ze dvou způsobů:
    - Použijte globálně hostovanou verzi Content Delivery Network služby Azure Maps Services v rámci Azure. Do prvku souboru přidejte odkaz na šablonu stylů JavaScript a CSS `<head>` :

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
        ```

    - Nebo můžete modul nástrojů pro kreslení pro Azure Maps zdrojového kódu sady web SDK načíst místně pomocí balíčku [Azure-Maps-Drawing-Tools](https://www.npmjs.com/package/azure-maps-drawing-tools) npm a potom ho hostovat s vaší aplikací. Tento balíček obsahuje také definice TypeScript. Použijte tento příkaz:
    
        > **NPM instalace Azure-Maps-Drawings-Tools**
    
        Pak přidejte odkaz na šablonu stylů JavaScript a CSS v `<head>` prvku souboru:

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>Použití Správce kreslení přímo

Po načtení modulu nástrojů pro kreslení do aplikace můžete povolit možnosti kreslení a úprav pomocí [správce kreslení](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#setoptions-drawingmanageroptions-). Můžete zadat možnosti pro správce kreslení při vytváření instance nebo `drawingManager.setOptions()` funkci nebo funkci použít.

### <a name="set-the-drawing-mode"></a>Nastavení režimu kreslení

Následující kód vytvoří instanci Správce výkresu a nastaví možnost **režim** kreslení. 

```javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

Níže uvedený kód je úplným příkladem, jak nastavit režim vykreslování Správce výkresu. Kliknutím na mapu začněte kreslit mnohoúhelník.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Nakreslit mnohoúhelník" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se, že pero <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>nakreslí mnohoúhelník</a> o Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="set-the-interaction-type"></a>Nastavení typu interakce

Správce kreslení podporuje tři různé způsoby interakce s mapou k vykreslování tvarů.

* `click` -Souřadnice se přidávají při kliknutí myší nebo dotykem.
* `freehand ` -Souřadnice se přidávají při přetahování myši nebo dotyku na mapě. 
* `hybrid` -Souřadnice se přidávají při kliknutí nebo přetahování myši nebo dotykem.

Následující kód povoluje režim kreslení mnohoúhelníku a nastavuje typ interakce kresby, na kterou má Správce výkresu dodržet `freehand` . 

```javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

 Tato ukázka kódu implementuje funkci kreslení mnohoúhelníku na mapě. Stačí, když podržíte levé tlačítko myši, a přetáhnete ho kolem, volně.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vykreslování volných rukou" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Přečtěte si <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>Free-hand drawing</a> , jak Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>vykreslování na ruky.
</iframe>


### <a name="customizing-drawing-options"></a>Přizpůsobení možností kreslení

Předchozí příklady ukázaly, jak přizpůsobit možnosti kreslení při vytváření instancí správce kreslení. Možnosti Správce kreslení můžete také nastavit pomocí `drawingManager.setOptions()` funkce. Níže je nástroj pro otestování přizpůsobení všech možností pro Správce výkresu pomocí funkce setOptions.

<br/>

<iframe height="685" title="Přizpůsobení Správce výkresu" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Podívejte se, <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>jak pero získá data</a> z Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Další kroky

Naučte se používat další funkce modulu nástrojů pro kreslení:

> [!div class="nextstepaction"]
> [Přidání panelu nástrojů pro kreslení](map-add-drawing-toolbar.md)

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
> [Správce výkresu](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [Panel nástrojů kreslení](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)

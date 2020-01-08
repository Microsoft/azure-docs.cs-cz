---
title: Použití modulu nástrojů pro kreslení v Azure Maps | Microsoft Docs
description: Nastavení dat možností kreslení pomocí Azure Maps Web SDK
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0ac9bc775798a14e6431718bc602d8ff41288c10
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75408156"
---
# <a name="use-the-drawing-tools-module"></a>Použití modulu nástrojů pro kreslení

Sada Azure Maps Web SDK poskytuje *modul nástrojů pro kreslení*. Tento modul usnadňuje vykreslování a úpravu obrazců na mapě pomocí vstupního zařízení, jako je třeba myš na dotykovou obrazovku. Základní třídou tohoto modulu je [Správce výkresu](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-) a poskytuje všechny schopnosti potřebné k vykreslování a úpravám obrazců na mapě. Správce výkresu se dá použít přímo a integrovat s vlastním uživatelským rozhraním panelu nástrojů nebo můžete využít předdefinovanou třídu [nástrojů pro kreslení](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) . 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>Načtení modulu nástrojů pro kreslení na webové stránce

1. Vytvořte nový soubor HTML a [implementujte mapování obvyklým způsobem](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).
2. Načtěte modul nástrojů Azure Maps Drawing Tools. Můžete ho načíst jedním ze dvou způsobů:
    - Použijte globálně hostovanou verzi Content Delivery Network služby Azure Maps Services v rámci Azure. Do prvku `<head>` souboru přidejte odkaz na šablonu stylů JavaScript a CSS:

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0.1/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0.1/atlas-drawing.min.js"></script>
        ```

    - Alternativně načtěte modul nástrojů pro kreslení pro zdrojový kód Azure Maps Web SDK místně pomocí balíčku [Azure-Maps-Drawing-Tools](https://www.npmjs.com/package/azure-maps-drawing-tools) npm a potom ho hostovat s vaší aplikací. Tento balíček obsahuje také definice TypeScript. Použijte tento příkaz:
    
        > **NPM instalace Azure-Maps-Drawings-Tools**
    
        Pak přidejte odkaz na šablonu stylů JavaScript a CSS v prvku `<head>` souboru:

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>Použití Správce kreslení přímo

Teď, když je modul nástrojů pro kreslení načtený do vaší aplikace, můžete použít [správce kreslení](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-) a povolit možnosti vykreslování a úprav v rámci mapy. Můžete zadat možnosti pro správce kreslení při vytváření instance nebo případně použít funkci `drawingManager.setOptions()`.

### <a name="set-the-drawing-mode"></a>Nastavení režimu kreslení

Následující kód vytvoří instanci Správce výkresu a nastaví možnost **režim** kreslení. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

Níže uvedený kód je úplným příkladem, jak nastavit režim vykreslování Správce výkresu. Kliknutím na mapu začněte kreslit mnohoúhelník.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Nakreslit mnohoúhelník" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se, že pero <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>nakreslí mnohoúhelník</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="set-the-interaction-type"></a>Nastavení typu interakce

Správce kreslení podporuje tři různé způsoby interakce s mapou k vykreslování tvarů.

* `click` – souřadnice se přidávají při kliknutí myší nebo dotykem.
* Souřadnice `freehand ` jsou přidány při přetahování myši nebo dotyku na mapě. 
* `hybrid` – souřadnice se přidávají při kliknutí nebo přetahování myši nebo dotykem.

Následující kód povoluje režim kreslení mnohoúhelníku a nastavuje typ interakce kresby, kterou má správce kreslení dodržet `freehand`. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

Níže je příklad kódu, který implementuje funkce, které vám umožní nakreslit mnohoúhelník na mapě volně, a přitom držet levé tlačítko myši a přetahovat ho kolem. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vykreslování volných rukou" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Přečtěte si, jak Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a> <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>vykreslování na ruky</a> .
</iframe>


### <a name="customizing-drawing-options"></a>Přizpůsobení možností kreslení

Předchozí příklady ukázaly, jak přizpůsobit možnosti kreslení při vytváření instancí správce kreslení. Možnosti Správce kreslení můžete také nastavit pomocí funkce `drawingManager.setOptions()`. Níže je nástroj pro otestování přizpůsobení všech možností pro Správce výkresu pomocí funkce setOptions.

<br/>

<iframe height="685" title="Přizpůsobení Správce výkresu" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Podívejte se, <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>jak pero získá data o tvaru</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Další kroky

Naučte se používat další funkce modulu nástrojů pro kreslení:

> [!div class="nextstepaction"]
> [Přidat panel nástrojů pro kreslení](map-add-drawing-toolbar.md)

> [!div class="nextstepaction"]
> [Získat data obrazce](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Reagovat na události vykreslování](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Typy interakcí a klávesové zkratky](drawing-tools-interactions-keyboard-shortcuts.md)

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Správce výkresu](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Panel nástrojů kreslení](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

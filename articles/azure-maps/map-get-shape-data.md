---
title: Získat data z obrazců na mapě | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak získat data obrazce vykreslená na mapě pomocí webové sady SDK Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: d74a7906157eed33487a5f88082701da1b2269dc
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988545"
---
# <a name="get-shape-data"></a>Získání dat tvaru

V tomto článku se dozvíte, jak získat data tvarů vykreslených na mapě. Používáme funkci **drawingManager. GetSource ()** uvnitř [Správce výkresu](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--). K dispozici jsou různé scénáře, kdy chcete extrahovat data o nesprávném formátu JSON vykresleného obrazce a použít ho jinde.  


## <a name="get-data-from-drawn-shape"></a>Získat data z vykresleného obrazce

Následující funkce získá zdrojová data vykresleného obrazce a vytvoří výstup na obrazovku. 

```Javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

Níže je ukázka kompletního běžícího kódu, kde můžete nakreslit obrazec pro otestování funkčnosti:

<br/>

<iframe height="686" title="Získání dat tvaru" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Podívejte se, <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>jak pero získá data o tvaru</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Další kroky

Naučte se používat další funkce modulu nástrojů pro kreslení:

> [!div class="nextstepaction"]
> [Reagovat na události vykreslování](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Typy interakcí a klávesové zkratky](drawing-tools-interactions-keyboard-shortcuts.md)

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Správce výkresu](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Panel nástrojů kreslení](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

---
title: Získání dat z obrazců na mapě | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak získat data obrazců nakreslená na mapě pomocí sady Microsoft Azure Maps Web SDK.
author: philmea
ms.author: philmea
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 88db018575f92e777223f383c65cd6db51ba515a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334417"
---
# <a name="get-shape-data"></a>Získání dat tvaru

Tento článek ukazuje, jak získat data obrazců, které jsou nakresleny na mapě. Používáme funkci **drawingManager.getSource()** uvnitř [správce výkresu](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--). Existují různé scénáře, kdy chcete extrahovat geojson data nakresleného tvaru a použít ji jinde.  


## <a name="get-data-from-drawn-shape"></a>Získání dat z nakresleného tvaru

Následující funkce získá nakreslená data obrazce a vytiskne je na obrazovku. 

```Javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

Níže je kompletní ukázka spuštěného kódu, kde můžete nakreslit obrazec a otestovat funkce:

<br/>

<iframe height="686" title="Získání dat tvaru" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>data obrazce Pero získat</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Další kroky

Přečtěte si, jak používat další funkce modulu kreslicích nástrojů:

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

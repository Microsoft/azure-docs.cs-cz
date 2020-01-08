---
title: Získat data obrazců ze Správce výkresu v Azure Maps | Microsoft Docs
description: Jak získat data obrazce pomocí Azure Maps Web SDK
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: bc8efe46453f76d353842b32bd52b41838979b49
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432826"
---
# <a name="get-shape-data"></a>Získání dat tvaru

V tomto článku se dozvíte, jak získat data obrazců vykreslená na mapě pomocí funkce **drawingManager. GetSource ()** [nástroje Drawing Manager](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--) . Může existovat několik scénářů, ve kterých můžete chtít extrahovat data o nestandardních JSON vykresleného obrazce a využít je jinde.  


## <a name="get-data-from-drawn-shape"></a>Získat data z vykresleného obrazce

Následující funkce získá zdrojová data vykresleného obrazce a vytvoří výstup na obrazovce. 

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
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Správce výkresu](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Panel nástrojů kreslení](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

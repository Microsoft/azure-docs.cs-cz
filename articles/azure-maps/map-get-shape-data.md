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
ms.openlocfilehash: e7706fba1efad1bd0ce7110e129dcf113689af9a
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309834"
---
# <a name="get-shape-data"></a>Získat data obrazce

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

<iframe height="686" title="Získat data obrazce" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Podívejte se, <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>jak pero získá data</a> z Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Správce výkresu](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Panel nástrojů kreslení](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

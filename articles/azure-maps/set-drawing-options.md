---
title: Nastavení možností kreslení v Azure Maps | Microsoft Docs
description: Nastavení dat možností kreslení pomocí Azure Maps Web SDK
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: d85525274db7818737b62ad4e9ea2026b8aef3b2
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309812"
---
# <a name="set-drawing-options"></a>Nastavení možností kreslení

V tomto článku se dozvíte, jak se v různých možnostech [správce kreslení](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-) mění prostředí uživatele. Můžete zadat možnosti pro správce kreslení při vytváření instance nebo můžete použít funkci **drawingManager. setOptions ()** k nastavení možností.


## <a name="set-drawing-mode"></a>Nastavit režim kreslení

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
Podívejte se, že pero <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>nakreslí mnohoúhelník</a> o<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="set-interaction-type"></a>Nastavit typ interakce

Následující kód nastaví typ interakce kresby, na kterou má Správce výkresu dodržet. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

Níže je ukázka kódu implementující funkce, které vám umožní kreslit na mapě volně, a přitom podržíte levé tlačítko myši a přetáhnete ho kolem. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vykreslování volných rukou" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Přečtěte si <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'></a> , jak Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>vykreslování na ruky.
</iframe>


## <a name="customizing-drawing-options"></a>Přizpůsobení možností kreslení

Předchozí příklady ukázaly, jak přizpůsobit možnosti kreslení při vytváření instancí správce kreslení. Možnosti Správce kreslení můžete také nastavit pomocí funkce **drawingManager. setOptions ()** . Níže je nástroj pro otestování přizpůsobení všech možností pro Správce výkresu pomocí funkce setOptions.

<br/>

<iframe height="685" title="Přizpůsobení Správce výkresu" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Podívejte se, <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>jak pero získá data</a> z Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Další postup

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Správce výkresu](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Panel nástrojů kreslení](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

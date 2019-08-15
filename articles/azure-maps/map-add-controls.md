---
title: Přidat ovládací prvky do Azure Maps | Microsoft Docs
description: Postup přidání ovládacího prvku Lupa, ovládacího prvku sklonu, otočení ovládacího prvku a výběru stylu na mapu v Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 7267f77ed3d296ac586dcfd0f525b94d5e6eb7a0
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976228"
---
# <a name="add-controls-to-a-map"></a>Přidání ovládacích prvků do mapy

V tomto článku se dozvíte, jak přidat ovládací prvky do mapy. Naučíte se také, jak vytvořit mapu se všemi ovládacími prvky a [Výběr stylu](https://docs.microsoft.com/azure/azure-maps/choose-map-style).

## <a name="add-zoom-control"></a>Přidat ovládací prvek Lupa

Ovládací prvek přiblížení přidá tlačítka pro přiblížení a oddálení mapy. Následující ukázka kódu vytvoří instanci třídy [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) a přidá ji do pravého dolního rohu mapy.

```javascript
//Construct a zoom control and add it to the map.
map.controls.add(new atlas.control.ZoomControl(), {
    position: 'bottom-right'
});
```

Níže je uvedená ukázka kompletního spuštění kódu výše uvedené funkce.

<br/>

<iframe height='500' scrolling='no' title='Přidání ovládacího prvku lupy' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Přečtěte si pero <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>Přidání ovládacího prvku Lupa</a> podle Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-pitch-control"></a>Přidat ovládací prvek sklonu

Ovládací prvek sklonu přidá tlačítka pro naklonění sklonu k mapě vzhledem k horizontu. Následující ukázka kódu vytvoří instanci třídy [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) a přidá ji do pravého horního rohu mapy.

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

Níže je uvedená ukázka kompletního spuštění kódu výše uvedené funkce.

<br/>

<iframe height='500' scrolling='no' title='Přidání ovládacího prvku sklon' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Přečtěte si pero <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>Přidání ovládacího prvku sklonu</a> pomocí<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-compass-control"></a>Přidat ovládací prvek kompas

Ovládací prvek kompas přidá tlačítko pro otočení mapy. Následující ukázka kódu vytvoří instanci třídy [ovládacího prvku kompas](/javascript/api/azure-maps-control/atlas.control.compasscontrol) a přidá ji do levého dolního rohu mapy.

```javascript
//Construct a compass control and add it to the map.
map.controls.add(new atlas.control.Compass(), {
    position: 'bottom-left'
});
```

Níže je uvedená ukázka kompletního spuštění kódu výše uvedené funkce.

<br/>

<iframe height='500' scrolling='no' title='Přidání ovládacího prvku otočit' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Přečtěte si pero <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>Přidání ovládacího prvku otočit</a> pomocí Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="a-map-with-all-controls"></a>Mapa se všemi ovládacími prvky

Následující ukázka kódu přidá ovládací prvky pro výběr stylu, Lupa, rozteč a kompas do pravého dolního rohu mapy. Všimněte si, jak se automaticky stackují. Pořadí řídicích objektů ve skriptu určuje pořadí, ve kterém se zobrazí na mapě. Chcete-li změnit pořadí ovládacích prvků na mapě, můžete změnit jejich pořadí ve skriptu.

<br/>

<iframe height='500' scrolling='no' title='Mapa se všemi ovládacími prvky' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Přečtěte si pero <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>a mapu se všemi ovládacími prvky</a> pomocí<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ovládací prvek pro výběr stylu je definován třídou [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) . Další informace o použití ovládacího prvku pro výběr stylu naleznete v tématu [Volba stylu mapy](choose-map-style.md).

## <a name="customize-controls"></a>Přizpůsobení ovládacích prvků

Tady je nástroj pro otestování různých možností přizpůsobení ovládacích prvků.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Možnosti ovládacího prvku navigace" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Prohlédněte si <a href='https://codepen.io/azuremaps/pen/LwBZMx/'>možnosti ovládacího prvku navigace</a> perem podle<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Chcete-li vytvořit přizpůsobené ovládací prvky navigace, vytvořte třídu, která rozšiřuje z `atlas.Control` třídy, nebo vytvořte prvek HTML a umístěte jej nad div mapy. Tento ovládací prvek uživatelského rozhraní volá funkci `setCamera` Maps a přesune mapu. 

## <a name="next-steps"></a>Další postup

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Ovládací prvek kompas](/javascript/api/azure-maps-control/atlas.control.compasscontrol)

> [!div class="nextstepaction"]
> [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 

> [!div class="nextstepaction"]
> [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 

Úplný kód najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Přidat PIN kód](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Přidat automaticky otevírané okno](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Přidat řádkovou vrstvu](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Přidat mnohoúhelníkovou vrstvu](map-add-shape.md)

> [!div class="nextstepaction"]
> [Přidat bublinovou vrstvu](map-add-bubble-layer.md)


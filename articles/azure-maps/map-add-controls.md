---
title: Přidat ovládací prvky do mapy | Mapy Microsoft Azure
description: Postup přidání ovládacího prvku Lupa, ovládacího prvku sklonu, otočení ovládacího prvku a výběru stylu na mapu v Microsoft Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 2a225d4ea19cccea114e47cc61a8055d28e8cd99
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895286"
---
# <a name="add-controls-to-a-map"></a>Přidání ovládacích prvků do mapy

V tomto článku se dozvíte, jak přidat ovládací prvky do mapy. Naučíte se také, jak vytvořit mapu se všemi ovládacími prvky a [Výběr stylu](./choose-map-style.md).

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

<iframe height='500' scrolling='no' title='Přidání ovládacího prvku lupy' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Přečtěte si pero <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>Přidání ovládacího prvku Lupa</a> podle Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-pitch-control"></a>Přidat ovládací prvek sklonu

Ovládací prvek sklonu přidá tlačítka pro naklonění sklonu k mapě vzhledem k horizontu. Následující ukázka kódu vytvoří instanci třídy [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) . Přidá PitchControl do pravého horního rohu mapy.

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

Níže je uvedená ukázka kompletního spuštění kódu výše uvedené funkce.

<br/>

<iframe height='500' scrolling='no' title='Přidání ovládacího prvku sklon' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Přečtěte si pero <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>Přidání ovládacího prvku sklonu</a> pomocí Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
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

<iframe height='500' scrolling='no' title='Přidání ovládacího prvku otočit' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Přečtěte si pero <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>Přidání ovládacího prvku otočit</a> pomocí Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="a-map-with-all-controls"></a>Mapa se všemi ovládacími prvky

Do pole lze vložit více ovládacích prvků a přidat je do mapy všechny najednou a umístit ve stejné oblasti mapy pro zjednodušení vývoje. Následující postup přidá na mapu standardní navigační ovládací prvky pomocí tohoto přístupu.

```javascript
map.controls.add([
    new atlas.control.ZoomControl(),
    new atlas.control.CompassControl(),
    new atlas.control.PitchControl(),
    new atlas.control.StyleControl()
], {
    position: "top-right"
});
```

Následující ukázka kódu přidá ovládací prvky přiblížení, kompasu, rozteč a výběr stylu do pravého horního rohu mapy. Všimněte si, jak se automaticky stackují. Pořadí řídicích objektů ve skriptu určuje pořadí, ve kterém se zobrazí na mapě. Chcete-li změnit pořadí ovládacích prvků na mapě, můžete změnit jejich pořadí v poli.

<br/>

<iframe height='500' scrolling='no' title='Mapa se všemi ovládacími prvky' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Přečtěte si pero <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>a mapu se všemi ovládacími prvky</a> pomocí Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ovládací prvek pro výběr stylu je definován třídou [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) . Další informace o použití ovládacího prvku pro výběr stylu naleznete v tématu [Volba stylu mapy](choose-map-style.md).

## <a name="customize-controls"></a>Přizpůsobení ovládacích prvků

Tady je nástroj pro otestování různých možností přizpůsobení ovládacích prvků.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Možnosti ovládacího prvku navigace" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Prohlédněte si <a href='https://codepen.io/azuremaps/pen/LwBZMx/'>možnosti ovládacího prvku navigace</a> perem podle Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Chcete-li vytvořit přizpůsobené ovládací prvky navigace, vytvořte třídu, která rozšiřuje z `atlas.Control` třídy, nebo vytvořte prvek HTML a umístěte jej nad div mapy. Tento ovládací prvek uživatelského rozhraní volá `setCamera` funkci Maps a přesune mapu. 

## <a name="next-steps"></a>Další kroky

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
> [Přidání špendlíku](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Přidání místního okna](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Přidání řádkové vrstvy](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Přidání mnohoúhelníkové vrstvy](map-add-shape.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy bublin](map-add-bubble-layer.md)
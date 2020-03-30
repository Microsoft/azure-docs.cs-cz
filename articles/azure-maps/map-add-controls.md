---
title: Přidání ovládacích prvků do mapy | Mapy Microsoft Azure
description: Jak přidat ovládání lupy, ovládání rozteče, otočení ovládacího prvku a výběr stylu do mapy v Mapách Microsoft Azure.
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 094dc9fd01ec71f378a173a2b4fa64cc672d7c97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334569"
---
# <a name="add-controls-to-a-map"></a>Přidání ovládacích prvků do mapy

Tento článek ukazuje, jak přidat ovládací prvky do mapy. Dozvíte se také, jak vytvořit mapu se všemi ovládacími prvky a [výběrem stylu](https://docs.microsoft.com/azure/azure-maps/choose-map-style).

## <a name="add-zoom-control"></a>Přidat ovládací prvek lupy

Ovládání přiblížením přidává tlačítka pro přiblížení a oddálení mapy. Následující ukázka kódu vytvoří instanci třídy [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) a přidá ji do pravého dolního rohu mapy.

```javascript
//Construct a zoom control and add it to the map.
map.controls.add(new atlas.control.ZoomControl(), {
    position: 'bottom-right'
});
```

Níže je kompletní ukázka spuštěného kódu výše uvedených funkcí.

<br/>

<iframe height='500' scrolling='no' title='Přidání ovládacího prvku lupy' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na ovládací prvek pero <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>přidání zoom</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-pitch-control"></a>Přidat ovládání rozteče

Ovládání rozteče přidává tlačítka pro naklápění rozteče na mapu vzhledem k horizontu. Následující ukázka kódu vytvoří instanci třídy [PitchControl.](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) Přidá PitchControl do pravého horního rohu mapy.

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

Níže je kompletní ukázka spuštěného kódu výše uvedených funkcí.

<br/>

<iframe height='500' scrolling='no' title='Přidání ovládacího prvku rozteč' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na ovládací prvek pera <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>přidání rozteč</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-compass-control"></a>Přidání ovládacího prvku kompasu

Ovládací prvek kompasu přidá tlačítko pro otáčení mapy. Následující ukázka kódu vytvoří instanci třídy [Compass Control](/javascript/api/azure-maps-control/atlas.control.compasscontrol) a přidá ji do levého dolního rohu mapy.

```javascript
//Construct a compass control and add it to the map.
map.controls.add(new atlas.control.Compass(), {
    position: 'bottom-left'
});
```

Níže je kompletní ukázka spuštěného kódu výše uvedených funkcí.

<br/>

<iframe height='500' scrolling='no' title='Přidání ovládacího prvku pro otočení' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>ovládací prvek Pen Adding a rotate</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="a-map-with-all-controls"></a>Mapa se všemi ovládacími prvky

Více ovládacích prvků lze vložit do pole a přidat do mapy najednou a umístit do stejné oblasti mapy pro zjednodušení vývoje. Následující přidá standardní navigační ovládací prvky na mapě pomocí tohoto přístupu.

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

Následující ukázka kódu přidá ovládací prvky pro výběr přiblížení, kompasu, rozteče a stylu do pravého horního rohu mapy. Všimněte si, jak se automaticky stohovat. Pořadí řídicích objektů ve skriptu určuje pořadí, ve kterém se zobrazují na mapě. Chcete-li změnit pořadí ovládacích prvků na mapě, můžete změnit jejich pořadí v poli.

<br/>

<iframe height='500' scrolling='no' title='Mapa se všemi ovládacími prvky' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na mapu pera se<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>všemi ovládacími prvky</a> podle Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ovládací prvek výběr stylu je definován [stylecontrol](/javascript/api/azure-maps-control/atlas.control.stylecontrol) třídy. Další informace o použití ovládacího prvku výběr stylu naleznete v [tématu výběr stylu mapy](choose-map-style.md).

## <a name="customize-controls"></a>Přizpůsobení ovládacích prvků

Zde je nástroj pro testování různých možností přizpůsobení ovládacích prvků.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Možnosti ovládacího prvku navigace" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/LwBZMx/'>možnosti ovládacího prvku Navigace</a> perem podle Map Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Pokud chcete vytvořit vlastní navigační ovládací prvky, vytvořte `atlas.Control` třídu, která se rozšiřuje z třídy, nebo vytvořte element HTML a umístěte jej nad mapovou div. Mít tento ovládací prvek `setCamera` uI volání mapy funkce přesunout mapu. 

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách použitých v tomto článku:

> [!div class="nextstepaction"]
> [Ovládání kompasu](/javascript/api/azure-maps-control/atlas.control.compasscontrol)

> [!div class="nextstepaction"]
> [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 

> [!div class="nextstepaction"]
> [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 

Úplný kód naleznete v následujících článcích:

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


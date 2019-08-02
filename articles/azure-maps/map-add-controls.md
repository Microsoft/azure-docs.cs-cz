---
title: Přidat mapové ovládací prvky v Azure Maps | Microsoft Docs
description: Postup přidání ovládacího prvku Lupa, ovládacího prvku sklonu, otočení ovládacího prvku a výběru stylu na mapu v Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 7a504b8df199a3a461d5eb4e5b7238462b4c438f
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638767"
---
# <a name="add-map-controls-to-azure-maps"></a>Přidat mapové ovládací prvky do Azure Maps

V tomto článku se dozvíte, jak přidat mapové ovládací prvky do mapy. Naučíte se také, jak vytvořit mapu se všemi ovládacími prvky a [Výběr stylu](https://docs.microsoft.com/azure/azure-maps/choose-map-style).

## <a name="add-zoom-control"></a>Přidat ovládací prvek Lupa

<iframe height='500' scrolling='no' title='Přidání ovládacího prvku lupy' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Přečtěte si pero <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>Přidání ovládacího prvku Lupa</a> podle Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

První blok kódu vytvoří objekt mapy pomocí mechanismu anonymního ověřování. Pokyny k vytvoření mapy najdete v tématu [vytvoření mapy](./map-create.md) .

Ovládací prvek Lupa přidává možnost přiblížení nebo oddálení mapy. Druhý blok kódu vytvoří objekt ovládacího prvku zvětšení pomocí ZoomControla Atlas [](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) a přidá ho k mapě pomocí ovládacího prvku mapové [ovládací prvky. přidat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metodu. Ovládací prvek přiblížení je v rámci **naslouchacího procesu** pro mapování událostí, aby se zajistilo jeho načtení po úplném načtení mapy.

## <a name="add-pitch-control"></a>Přidat ovládací prvek sklonu

<iframe height='500' scrolling='no' title='Přidání ovládacího prvku sklon' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Přečtěte si pero <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>Přidání ovládacího prvku sklonu</a> pomocí<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () na <a href='https://codepen.io'>CodePen</a>.
</iframe>

První blok kódu vytvoří objekt mapy pomocí mechanismu anonymního ověřování. Pokyny k vytvoření mapy najdete v tématu [vytvoření mapy](./map-create.md) .

Ovládací prvek sklonu přidá možnost změny sklonu mapy. Druhý blok kódu vytvoří objekt ovládacího prvku pro sklon s použitím PitchControla [](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) Atlas a přidá jej do mapy pomocí ovládacího prvku mapové [ovládací prvky. přidat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metodu. Ovládací prvek sklonu je v rámci **naslouchacího procesu** pro mapování událostí, aby se zajistilo jeho načtení po úplném načtení mapy.

## <a name="add-compass-control"></a>Přidat ovládací prvek kompas

<iframe height='500' scrolling='no' title='Přidání ovládacího prvku otočit' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Přečtěte si pero <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>Přidání ovládacího prvku otočit</a> pomocí Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

První blok kódu vytvoří objekt mapy pomocí mechanismu anonymního ověřování. Pokyny k vytvoření mapy najdete v tématu [vytvoření mapy](./map-create.md) .

Druhý blok kódu vytvoří objekt ovládacího prvku kompas pomocí [ovládacího prvku](/javascript/api/azure-maps-control/atlas.control.compasscontrol)The kompas kompas. Také přidá ovládací prvek kompas na mapu pomocí ovládacího prvku mapové metody [. Add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) . Ovládací prvek kompas je v rámci **naslouchacího procesu** pro mapování událostí, aby se zajistilo jeho načtení po úplném načtení mapy.

## <a name="a-map-with-all-controls"></a>Mapa se všemi ovládacími prvky

<iframe height='500' scrolling='no' title='Mapa se všemi ovládacími prvky' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Přečtěte si pero <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>a mapu se všemi ovládacími prvky</a> pomocí<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () na <a href='https://codepen.io'>CodePen</a>.
</iframe>

První blok kódu vytvoří objekt mapy pomocí mechanismu anonymního ověřování. Pokyny k vytvoření mapy najdete v tématu [vytvoření mapy](./map-create.md) .

Druhý blok kódu vytvoří objekt ovládacího prvku kompas pomocí CompassControla Atlas [](/javascript/api/azure-maps-control/atlas.control.compasscontrol) a přidá ho k mapě pomocí ovládacího prvku mapové [ovládací prvky. přidat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metodu.

Třetí blok kódu vytvoří objekt ovládacího prvku zvětšení pomocí ZoomControla Atlas a [](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) přidá ho k mapě pomocí [ovládacího prvku mapové ovládací prvky. přidat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metodu.

Čtvrtý blok kódu vytvoří objekt ovládacího prvku pro sklon pomocí PitchControlu [](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) Atlas a přidá ho k mapě pomocí ovládacího prvku mapové [ovládací prvky. přidat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metodu.

Poslední blok kódu vytvoří objekt pro výběr stylu pomocí StyleControlu Atlas a přidá [](/javascript/api/azure-maps-control/atlas.control.stylecontrol) ho k mapě pomocí ovládacího prvku mapové metody [. Add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) . Všechny řídicí objekty jsou přidány v rámci **naslouchacího procesu mapování událostí** , aby bylo zajištěno, že budou načteny po úplném načtení mapy.

Pořadí řídicích objektů ve skriptu určuje pořadí, ve kterém se zobrazí na mapě. Chcete-li změnit pořadí ovládacích prvků na mapě, můžete změnit jejich pořadí ve skriptu.

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest)

Úplný kód najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Přidat PIN kód](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Přidat automaticky otevírané okno](./map-add-popup.md)

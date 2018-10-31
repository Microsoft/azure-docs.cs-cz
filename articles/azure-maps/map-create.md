---
title: Vytvořte mapu s Azure Maps | Dokumentace Microsoftu
description: Jak vytvořit mapu jazyka Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 659f6def7e6bb045606b7214a4b8b4cab693117b
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50248163"
---
# <a name="create-a-map"></a>Vytvoření mapy

Tento článek popisuje způsoby, jak vytvořit mapu a animovat mapy.  

## <a name="understand-the-code"></a>Vysvětlení kódu

Existují dva způsoby, jak můžete vytvořit mapu. Fotoaparát na mapě můžete nastavit tak, že zadáte středový bod a úroveň přiblížení nebo je můžete nastavit fotoaparát mezí mapy zadáním směřující na jihozápad a severovýchod ohraničující body.

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Nastavení fotoaparátu/kamery

<iframe height='500' scrolling='no' title='Vytvořte mapu prostřednictvím CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Vytvořte mapu prostřednictvím `CameraOptions` </a>pomocí Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu [objekt Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) byla vytvořena přes `new atlas.Map()` a jsou nastaveny Centrování a přiblížení. Vlastnosti mapy, jako je úroveň Centrování a přiblížení jsou součástí [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraoptions?view=azure-iot-typescript-latest).

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Nastavit fotoaparát hranice

<iframe height='500' scrolling='no' title='Vytvořte mapu prostřednictvím CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Vytvořte mapu prostřednictvím `CameraBoundsOptions` </a>pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu [objekt Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) je vytvořená prostřednictvím `new atlas.Map()`. Mapy – vlastnosti, jako `CameraBoundsOptions` lze definovat prostřednictvím [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) funkce třídy Map. Hranice a odsazení vlastnosti nastavené pomocí `setCamera`.

### <a name="animate-map-view"></a>Animace zobrazení mapy

<iframe height='500' scrolling='no' title='Animace zobrazení mapy' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/WayvbO/'>animace zobrazení mapy</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu, vytvoří první blok kódu [objekt Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) prostřednictvím `new atlas.Map()`. Vlastnosti mapy, jako je úroveň Centrování a přiblížení jsou součástí [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraoptions?view=azure-iot-typescript-latest). `CameraOptions` lze definovat v konstruktoru mapy nebo prostřednictvím [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) funkce třídy Map. [Mapování styl](https://review.docs.microsoft.com/azure/azure-maps/supported-map-styles?branch=pr-en-us-54960) je nastavena na `road`.

Druhý blok kódu vytvoří animovat mapy funkce, který animuje změnu v zobrazení mapy definováním [AnimateOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.animationoptions?view=azure-iot-typescript-latest) prostřednictvím [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) funkce. Funkce se aktivuje tlačítko "animace mapy ke generování náhodných zvětšení při každé klikněte na tlačítko.

## <a name="try-out-the-code"></a>Vyzkoušejte si kód

Podívejte se na výše uvedeném ukázkovém kódu. Můžete upravit na kód jazyka JavaScript **JS kartu** na levé straně a najdete v zobrazení mapy, změní se na **výsledek kartu** na pravé straně. Můžete také kliknout na **upravit na CodePen** tlačítko a úpravy kódu v CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Příklady kódu pro přidání funkce do vaší aplikace:

> [!div class="nextstepaction"]
> [Vyberte styl mapy](choose-map-style.md)

> [!div class="nextstepaction"]
> [Přidat ovládací prvky mapy](map-add-controls.md)

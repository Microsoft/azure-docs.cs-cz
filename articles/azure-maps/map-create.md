---
title: Vytvoření mapy pomocí Azure Maps | Microsoft Docs
description: Postup vytvoření mapy JavaScriptu
author: jingjing-z
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 8a4f67290e93d8b296added9023fe9b6947ba02c
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638676"
---
# <a name="create-a-map"></a>Vytvoření mapy

V tomto článku se dozvíte, jak vytvořit mapu a animovat mapu.  

## <a name="understand-the-code"></a>Vysvětlení kódu

Existují dva způsoby, jak můžete vytvořit mapu. Můžete nastavit kameru mapy zadáním středového bodu a úrovně přiblížení nebo můžete nastavit meze kamery pro mapu tím, že zadáte bodově a severovýchodně ohraničující body.

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Nastavení kamery

<iframe height='500' scrolling='no' title='Vytvoření mapy prostřednictvím CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Prohlédněte si pero <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>vytvořením mapy `CameraOptions` prostřednictvím </a>Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu je [objekt mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) vytvořen pomocí `new atlas.Map()` a nastavení Center a přiblížení. Vlastnosti mapy, jako je například střed a úroveň přiblížení, jsou součástí [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions).

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Nastavení hranic kamery

<iframe height='500' scrolling='no' title='Vytvoření mapy prostřednictvím CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Prohlédněte si pero <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>vytvořením mapy `CameraBoundsOptions` prostřednictvím </a>Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu je [objekt mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) vytvořen pomocí `new atlas.Map()`. Vlastnosti mapy, jako `CameraBoundsOptions` je například, lze definovat prostřednictvím funkce [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) třídy map. Vlastnosti bounds a odsazení jsou nastaveny pomocí `setCamera`.

### <a name="animate-map-view"></a>Animovat zobrazení mapy

<iframe height='500' scrolling='no' title='Animovat zobrazení mapy' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/WayvbO/'>zobrazení mapy animace</a> perem Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu vytvoří první blok kódu [objekt map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) prostřednictvím `new atlas.Map()`. Vlastnosti mapy, jako je například střed a úroveň přiblížení, jsou součástí [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions). `CameraOptions`lze definovat v konstruktoru mapy nebo prostřednictvím funkce [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) třídy map. [Styl mapy](https://docs.microsoft.com/azure/azure-maps/supported-map-styles) je nastaven na `road`hodnotu.

Druhý blok kódu vytvoří funkci animace mapy, která animuje změnu v zobrazení mapy definováním [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions) prostřednictvím funkce [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) . Funkce je aktivována tlačítkem animace map, aby při každém kliknutí vygenerovala náhodný stupeň přiblížení.

## <a name="try-out-the-code"></a>Vyzkoušejte si kód

Podívejte se na vzorový kód uvedený výše. JavaScriptový kód můžete upravit na **kartě js** vlevo a vidět změny zobrazení mapy na **kartě výsledek** na pravé straně. Můžete také kliknout na tlačítko **Upravit při CodePen** a upravit kód v CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Další postup

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Další funkce pro přidání funkcí do aplikace najdete v příkladech kódu:

> [!div class="nextstepaction"]
> [Zvolit styl mapy](choose-map-style.md)

> [!div class="nextstepaction"]
> [Přidat mapové ovládací prvky](map-add-controls.md)

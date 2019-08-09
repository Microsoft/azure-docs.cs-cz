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
ms.openlocfilehash: 9f1188fcf2aa24791ca5181f5c94a1b0f6b15dc1
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882070"
---
# <a name="create-a-map"></a>Vytvoření mapy

V tomto článku se dozvíte, jak vytvořit mapu a animovat mapu.  

## <a name="loading-a-map"></a>Načítání mapy

Chcete-li načíst mapu, vytvořte novou instanci [třídy map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest). Při inicializaci mapování ID elementu DIV pro vykreslení mapy a sady možností, které mají být použity při načítání mapy, jsou předány. Pokud v `atlas` oboru názvů nejsou zadány výchozí informace o ověřování, bude nutné tyto informace při načítání mapy zadat v možnostech mapování. Mapa načítá několik zdrojů asynchronně pro výkon. V takovém případě po vytvoření instance mapy připojte `ready` událost nebo `load` k mapě a pak přidejte jakýkoliv další kód, který komunikuje s mapou v této obslužné rutině události. `ready` Událost se aktivuje, jakmile bude mít mapa dostatek prostředků, aby je bylo možné používat programově. `load` Událost je aktivována po úplném načtení počátečního zobrazení mapy. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Základní načtení mapy" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>základní načtení mapy</a> perem pomocí Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Na stejné stránce může být načteno více map a každá z nich může používat stejné nebo jiné nastavení ověřování a jazyka.

## <a name="show-a-single-copy-of-the-world"></a>Zobrazit jednu kopii světa

Když se mapa zmenší na celé obrazovce, zobrazí se ve vodorovném směru více kopií světa. To je skvělé pro většinu scénářů, ale u některých aplikací může být žádoucí zobrazit jenom jednu kopii světa. To lze provést nastavením možnosti Maps `renderWorldCopies` na. `false`

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> pomocí Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="controlling-the-map-camera"></a>Řízení videokamery mapy

Existují dva způsoby, jak lze zobrazovanou oblast mapy nastavit pomocí kamery. Můžete nastavit možnosti kamery, jako je například střed a Lupa, při načítání mapy nebo zavolat `setCamera` možnost kdykoli po načtení mapy do programové aktualizace zobrazení mapy.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Nastavení kamery

V následujícím kódu je vytvořen [objekt mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) a jsou nastaveny možnosti centrování a přiblížení. Vlastnosti mapy, jako je například střed a úroveň přiblížení, jsou [](/javascript/api/azure-maps-control/atlas.cameraoptions)součástí CameraOptionsu.

<br/>

<iframe height='500' scrolling='no' title='Vytvoření mapy prostřednictvím CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Prohlédněte si pero <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>vytvořením mapy `CameraOptions` prostřednictvím </a>Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Nastavení hranic kamery

V následujícím kódu je [objekt mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) vytvořen pomocí `new atlas.Map()`. Vlastnosti mapy, jako `CameraBoundsOptions` je například, lze definovat prostřednictvím funkce [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) třídy map. Vlastnosti bounds a odsazení jsou nastaveny pomocí `setCamera`.

<br/>

<iframe height='500' scrolling='no' title='Vytvoření mapy prostřednictvím CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Prohlédněte si pero <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>vytvořením mapy `CameraBoundsOptions` prostřednictvím </a>Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Animovat zobrazení mapy

V následujícím kódu vytvoří první blok kódu mapu a nastaví styl mapy, hodnoty středu a přiblížení. Ve druhém bloku kódu je pro tlačítko animace vytvořena obslužná rutina události Click. Po kliknutí na toto tlačítko je volána funkce setCamera s některými náhodnými hodnotami pro [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions), [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Animovat zobrazení mapy' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/WayvbO/'>zobrazení mapy animace</a> perem Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>Vyzkoušejte si kód

Podívejte se na vzorový kód uvedený výše. JavaScriptový kód můžete upravit na **kartě js** vlevo a vidět změny zobrazení mapy na **kartě výsledek** na pravé straně. Můžete také kliknout na tlačítko **Upravit při CodePen** a upravit kód v CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Další postup

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)

Další funkce pro přidání funkcí do aplikace najdete v příkladech kódu:

> [!div class="nextstepaction"]
> [Změnit styl mapy](choose-map-style.md)

> [!div class="nextstepaction"]
> [Přidat ovládací prvky do mapy](map-add-controls.md)

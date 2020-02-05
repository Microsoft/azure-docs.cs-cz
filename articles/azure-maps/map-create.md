---
title: Vytvoření mapy pomocí Azure Maps | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak na webové stránce vykreslit mapu pomocí webové sady SDK Microsoft Azure Maps.
author: jingjing-z
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 578abae5b206b31674b00b9d27ef34174b93759f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988579"
---
# <a name="create-a-map"></a>Vytvoření mapy

V tomto článku se dozvíte, jak vytvořit mapu a animovat mapu.  

## <a name="loading-a-map"></a>Načítání mapy

Chcete-li načíst mapu, vytvořte novou instanci [třídy map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest). Při inicializaci mapy předejte ID elementu DIV pro vykreslení mapy a předání sady možností pro použití při načítání mapy. Pokud nejsou v oboru názvů `atlas` zadány výchozí informace o ověřování, bude nutné tyto informace při načítání mapy zadat v možnostech mapování. Mapa načítá několik zdrojů asynchronně pro výkon. V takovém případě po vytvoření instance mapy připojte k mapě událost `ready` nebo `load` a pak přidejte jakýkoliv další kód, který komunikuje s mapou, do obslužné rutiny události. Událost `ready` aktivuje, jakmile bude mít mapa dostatek prostředků, aby bylo možné je interagovat programově. Událost `load` je aktivována po úplném načtení počátečního zobrazení mapy. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Základní načtení mapy" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>základní načtení mapy</a> perem Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Na stejné stránce můžete načíst několik map. Více map na stejné stránce může používat stejné nebo jiné nastavení ověřování a jazyka.

## <a name="show-a-single-copy-of-the-world"></a>Zobrazit jednu kopii světa

Když se mapa zmenší na celé obrazovce, zobrazí se ve vodorovném směru více kopií světa. Tato možnost je vhodná pro některé scénáře, ale u ostatních aplikací je žádoucí zobrazit jednu kopii světa. Toto chování je implementováno nastavením možnosti `renderWorldCopies` mapování na hodnotu `false`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="controlling-the-map-camera"></a>Řízení videokamery mapy

Existují dva způsoby, jak nastavit zobrazovanou oblast mapy pomocí kamery mapy. Při načítání mapy lze nastavit možnosti kamery. Nebo můžete zavolat možnost `setCamera` kdykoli po načtení mapy do programové aktualizace zobrazení mapy.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Nastavení kamery

V následujícím kódu je vytvořen [objekt mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) a jsou nastaveny možnosti centrování a přiblížení. Vlastnosti mapy, jako je například střed a úroveň přiblížení, jsou součástí [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions).

<br/>

<iframe height='500' scrolling='no' title='Vytvoření mapy prostřednictvím CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Prohlédněte si pero <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>vytvořením mapy prostřednictvím `CameraOptions` </a>Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Nastavení hranic kamery

V následujícím kódu je [objekt mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) vytvořen pomocí `new atlas.Map()`. Vlastnosti mapy, jako je například `CameraBoundsOptions`, lze definovat prostřednictvím funkce [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) třídy map. Vlastnosti bounds a odsazení jsou nastaveny pomocí `setCamera`.

<br/>

<iframe height='500' scrolling='no' title='Vytvoření mapy prostřednictvím CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Prohlédněte si pero <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>vytvořením mapy prostřednictvím `CameraBoundsOptions` </a>Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Animovat zobrazení mapy

V následujícím kódu vytvoří první blok kódu mapu a nastaví styly pro vložení a zvětšení mapy. Ve druhém bloku kódu je pro tlačítko animace vytvořena obslužná rutina události Click. Po kliknutí na toto tlačítko je funkce `setCamera` volána s některými náhodnými hodnotami pro rozhraní [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) a [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Animovat zobrazení mapy' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/WayvbO/'>zobrazení mapy animace</a> perem Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>Vyzkoušejte si kód

Podívejte se na ukázky kódu. Kód jazyka JavaScript můžete upravit na **kartě js** a zobrazit změny zobrazení mapy na **kartě výsledek**. Můžete také kliknout na **Upravit v CodePen**, v pravém horním rohu a upravit kód v CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Další kroky

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

> [!div class="nextstepaction"]
> [Ukázky kódu](https://docs.microsoft.com/samples/browse/?products=azure-maps)

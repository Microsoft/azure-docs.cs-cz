---
title: Vytvoření mapy pomocí Azure Maps | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak vykreslit mapu na webové stránce pomocí sady Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: cfeff430e5313c8728582c4790c9aca9482d63aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534912"
---
# <a name="create-a-map"></a>Vytvoření mapy

Tento článek ukazuje způsoby, jak vytvořit mapu a animovat mapu.  

## <a name="loading-a-map"></a>Načítání mapy

Chcete-li načíst mapu, vytvořte novou instanci [třídy Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map). Při inicializaci mapy předavte ID elementu DIV, abyste mapu vykreslili, a předaj sadu možností, které se použijí při načítání mapy. Pokud v oboru `atlas` názvů nejsou zadány výchozí informace o ověřování, bude nutné tyto informace zadat v možnostech mapy při načítání mapy. Mapa načte několik prostředků asynchronně pro výkon. Jako takové po vytvoření instance mapy, připojit `ready` nebo `load` událost na mapě a pak přidat další kód, který spolupracuje s mapou obslužné rutiny události. Událost `ready` se aktivuje, jakmile má mapa dostatek prostředků načtených pro programovou interakci. Událost `load` se aktivuje po dokončení počátečního zobrazení mapy. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Základní zatížení mapy" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>načítání mapy</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>Pero Základní podle Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Na stejnou stránku můžete načíst více map. Více map na stejné stránce může používat stejné nebo jiné nastavení ověřování a jazyka.

## <a name="show-a-single-copy-of-the-world"></a>Zobrazení jediné kopie světa

Když je mapa oddálena na širokoúhlé obrazovce, více kopií světa se zobrazí vodorovně. Tato možnost je skvělá pro některé scénáře, ale pro jiné aplikace je žádoucí vidět jednu kopii světa. Toto chování je implementováno nastavením možnosti mapy `renderWorldCopies` na `false`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="map-options"></a>Možnosti mapy

Při vytváření mapy existuje několik různých typů možností, které lze předat přizpůsobit, jak funguje mapa, jak je uvedeno níže.

- [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions) a [CameraBoundOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions) se používají k určení oblasti, kterou by se měla zobrazit mapa.
- [ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions) slouží k určení, jak by měl pracovat s mapy, které moc mapy.
- [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) se používají k určení mapy by měly být stylizované a vykresleny.
- [UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions) se používají k určení, jak by měla mapa dosáhnout, když uživatel pracuje s mapou. 

Tyto možnosti lze také aktualizovat po načtení `setStyle`mapy `setUserInteraction` pomocí funkce `setCamera`, `setServiceOptions`, a. 

## <a name="controlling-the-map-camera"></a>Ovládání mapové kamery

Zobrazenou oblast mapy lze nastavit dvěma způsoby pomocí kamery mapy. Při načítání mapy můžete nastavit možnosti fotoaparátu. Nebo můžete tuto `setCamera` možnost zavolat kdykoli po načtení mapy, abyste programově aktualizovali zobrazení mapy.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Nastavení fotoaparátu

Mapa kamera určuje, co se zobrazí ve výřezu plátna mapy. Možnosti fotoaparátu lze předat možnosti mapy při inicializování nebo předání do funkce mapy. `setCamera`

```javascript
//Set the camera options when creating the map.
var map = new atlas.Map('map', {
    center: [-122.33, 47.6],
    zoom: 12

    //Additional map options.
};

//Update the map camera at anytime using setCamera function.
map.setCamera({
    center: [-110, 45],
    zoom: 5 
});
```

V následujícím kódu je vytvořen [objekt Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) a jsou nastaveny volby prostřed a zvětšení. Vlastnosti mapování, například na střed a úroveň zvětšení, jsou součástí [možnosti cameraoptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions).

<br/>

<iframe height='500' scrolling='no' title='Vytvoření mapy pomocí možnosti fotoaparátu' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero Vytvořit mapu<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/qxKBMN/'> `CameraOptions` pomocí </a>služby Azure založené na poloze ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Nastavení hranic kamery

K aktualizaci mapové kamery lze použít ohraničovací rámeček. Pokud byl ohraničovací rámeček vypočítán z bodových dat, je často užitečné také určit hodnotu odsazení obrazových bodů v možnostech fotoaparátu, aby se zohlednila velikost ikony. To pomůže zajistit, aby body nespadly z okraje výřezu mapy.

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

V následujícím kódu je [objekt Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) `new atlas.Map()`vytvořen pomocí . Vlastnosti mapy, jako `CameraBoundsOptions` je například lze definovat pomocí [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) funkce Map třídy. Hranice a vlastnosti odsazení jsou nastaveny pomocí `setCamera`.

<br/>

<iframe height='500' scrolling='no' title='Vytvoření mapy pomocí cameraboundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero Vytvořit<a href='https://codepen.io/azuremaps'>@azuremaps</a>mapu <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'> `CameraBoundsOptions` pomocí </a>Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Animovat zobrazení mapy

Při nastavování možností kamery na mapě lze také nastavit [možnosti animace.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.animationoptions) Tyto volby určují typ animace a dobu trvání, kterou by mělo být nutné přesunout kameru.

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

V následujícím kódu vytvoří první blok kódu mapu a nastaví styly mapy enter a zoom. V druhém bloku kódu je pro tlačítko animate vytvořena obslužná rutina události kliknutí. Po klepnutí na toto tlačítko je `setCamera` funkce volána s některými náhodnými hodnotami pro [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) a [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Animovat zobrazení mapy' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/WayvbO/'>zobrazení mapy</a> animace<a href='https://codepen.io/azuremaps'>@azuremaps</a>pera pomocí Map Azure ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>Vyzkoušejte kód

Podívejte se na vzorky kódu. Kód JavaScriptu můžete upravit na **kartě JS** a zobrazit změny zobrazení mapy na **kartě Výsledek**. Můžete také kliknout **na upravit na CodePen**, v pravém horním rohu a upravit kód v CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách použitých v tomto článku:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Možnosti fotoaparátu](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [Možnosti animace](/javascript/api/azure-maps-control/atlas.animationoptions)

Podívejte se na příklady kódu pro přidání funkcí do aplikace:

> [!div class="nextstepaction"]
> [Změna stylu mapy](choose-map-style.md)

> [!div class="nextstepaction"]
> [Přidání ovládacích prvků do mapy](map-add-controls.md)

> [!div class="nextstepaction"]
> [Ukázky kódu](https://docs.microsoft.com/samples/browse/?products=azure-maps)

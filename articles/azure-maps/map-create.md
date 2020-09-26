---
title: Vytvoření mapy pomocí Azure Maps | Mapy Microsoft Azure
description: Zjistěte, jak přidat mapy k webovým stránkám pomocí Azure Maps webové sady SDK. Seznamte se s možnostmi pro animace, styly, kamery, služby a interakce uživatele.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 62aa8f966126d95af003478e7f43d3ccea2b48cd
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91310403"
---
# <a name="create-a-map"></a>Vytvoření mapy

V tomto článku se dozvíte, jak vytvořit mapu a animovat mapu.  

## <a name="loading-a-map"></a>Načítání mapy

Chcete-li načíst mapu, vytvořte novou instanci [třídy map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map). Při inicializaci mapy předejte ID elementu DIV pro vykreslení mapy a předání sady možností pro použití při načítání mapy. Pokud v oboru názvů nejsou zadány výchozí informace o ověřování `atlas` , bude nutné tyto informace při načítání mapy zadat v možnostech mapování. Mapa načítá několik zdrojů asynchronně pro výkon. V takovém případě po vytvoření instance mapy připojte `ready` `load` událost nebo k mapě a pak přidejte jakýkoliv další kód, který komunikuje s mapou, do obslužné rutiny události. `ready`Událost se aktivuje, jakmile bude mít mapa dostatek prostředků, aby je bylo možné používat programově. `load`Událost je aktivována po úplném načtení počátečního zobrazení mapy. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Základní načtení mapy" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>základní načtení mapy</a> perem pomocí Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Na stejné stránce můžete načíst několik map. Více map na stejné stránce může používat stejné nebo jiné nastavení ověřování a jazyka.

## <a name="show-a-single-copy-of-the-world"></a>Zobrazit jednu kopii světa

Když se mapa zmenší na celé obrazovce, zobrazí se ve vodorovném směru více kopií světa. Tato možnost je vhodná pro některé scénáře, ale u ostatních aplikací je žádoucí zobrazit jednu kopii světa. Toto chování je implementováno nastavením možnosti mapy `renderWorldCopies` na `false` .

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> pomocí Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="map-options"></a>Možnosti mapování

Při vytváření mapy je k dispozici několik různých typů možností, které mohou být předány, aby bylo možné přizpůsobit, jak mapa funguje, jak je uvedeno níže.

- [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions) a [CameraBoundOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions) se používají k určení oblasti, kterou má mapa zobrazovat.
- [ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions) slouží k určení, jak má mapa spolupracovat se službami, které mapu nasazuje.
- [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) se používají k určení mapování, které by mělo být ve stylu a vykreslené.
- [UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions) slouží k určení způsobu, jakým má mapování dosáhnout při interakci uživatele s mapou. 

Tyto možnosti lze také aktualizovat poté, co byla mapa načtena pomocí `setCamera` funkcí, `setServiceOptions` , `setStyle` a `setUserInteraction` . 

## <a name="controlling-the-map-camera"></a>Řízení videokamery mapy

Existují dva způsoby, jak nastavit zobrazovanou oblast mapy pomocí kamery mapy. Při načítání mapy lze nastavit možnosti kamery. Nebo můžete zavolat `setCamera` možnost kdykoli po načtení mapy do programové aktualizace zobrazení mapy.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Nastavení kamery

Mapový fotoaparát řídí, co se zobrazí v zobrazení plátna mapy. Možnosti kamery lze předat do možností mapy při inicializaci nebo předávat do `setCamera` funkce Maps.

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

V následujícím kódu je vytvořen [objekt mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) a jsou nastaveny možnosti centrování a přiblížení. Vlastnosti mapy, jako je například střed a úroveň přiblížení, jsou součástí [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions).

<br/>

<iframe height='500' scrolling='no' title='Vytvoření mapy prostřednictvím CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Prohlédněte si pero <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>vytvořením mapy `CameraOptions` prostřednictvím </a>Azure Location Based Services ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Nastavení hranic kamery

K aktualizaci kamery mapy lze použít ohraničující rámeček. Pokud se ohraničující pole vypočítalo z dat bodu, často je užitečné zadat hodnotu odsazení v pixelech v možnostech kamery, aby se zohlednila velikost ikony. To pomůže zajistit, že body nespadají do okraje zobrazení mapy.

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

V následujícím kódu je [objekt mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) vytvořen pomocí `new atlas.Map()` . Vlastnosti mapy, jako je například, `CameraBoundsOptions` lze definovat prostřednictvím funkce [SetCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) třídy map. Vlastnosti bounds a odsazení jsou nastaveny pomocí `setCamera` .

<br/>

<iframe height='500' scrolling='no' title='Vytvoření mapy prostřednictvím CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Prohlédněte si pero <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>vytvořením mapy `CameraBoundsOptions` prostřednictvím </a>Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Animovat zobrazení mapy

Při nastavování možností kamery mapy lze nastavit také [Možnosti animace](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.animationoptions) . Tyto možnosti určují typ animace a doby trvání, kterou by měl trvat při přesunu kamery.

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

V následujícím kódu vytvoří první blok kódu mapu a nastaví styly pro vložení a zvětšení mapy. Ve druhém bloku kódu je pro tlačítko animace vytvořena obslužná rutina události Click. Po kliknutí na toto tlačítko `setCamera` je funkce volána s některými náhodnými hodnotami pro [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) a [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Animovat zobrazení mapy' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/WayvbO/'>zobrazení mapy animace</a> perem Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="request-transforms"></a>Transformace požadavků

Někdy je vhodné, aby bylo možné upravovat požadavky HTTP provedené mapovým ovládacím prvkem. Příklad:

- Přidejte další záhlaví do požadavků dlaždic. To se často provádí pro služby chráněné heslem.
- Upravte adresy URL tak, aby se spouštěly požadavky prostřednictvím proxy služby.

[Možnosti služby](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions) mapy mají a `transformRequest` , které lze použít k úpravě všech požadavků, které jsou provedeny v mapě před jejich provedením. `transformRequest`Možnost je funkce, která přijímá dva parametry, adresu URL řetězce a typ prostředku řetězec, který označuje, k čemu je požadavek použit. Tato funkce musí vracet [requestParameters](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.requestparameters) výsledek.

```JavaScript
transformRequest: (url: string, resourceType: string) => RequestParameters
```

Následující příklad ukazuje, jak použít to pro úpravu všech požadavků na velikost přidáním `https://example.com` uživatelského jména a hesla jako hlavičky do žádosti.

```JavaScript
var map = new atlas.Map('myMap', {
    transformRequest: function (url, resourceType) {
        //Check to see if the request is to the specified endpoint.
        if (url.indexOf('https://examples.com') > -1) {
            //Add custom headers to the request.
            return {
                url: url,
                header: {
                    username: 'myUsername',
                    password: 'myPassword'
                }
            };
        }

        //Return the URL unchanged by default.
        return { url: url };
    },

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

## <a name="try-out-the-code"></a>Vyzkoušejte si kód

Podívejte se na ukázky kódu. Kód jazyka JavaScript můžete upravit na **kartě js** a zobrazit změny zobrazení mapy na **kartě výsledek**. Můžete také kliknout na **Upravit v CodePen**, v pravém horním rohu a upravit kód v CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)

Další funkce pro přidání funkcí do aplikace najdete v příkladech kódu:

> [!div class="nextstepaction"]
> [Změna stylu mapy](choose-map-style.md)

> [!div class="nextstepaction"]
> [Přidání ovládacích prvků do mapy](map-add-controls.md)

> [!div class="nextstepaction"]
> [Ukázky kódu](https://docs.microsoft.com/samples/browse/?products=azure-maps)

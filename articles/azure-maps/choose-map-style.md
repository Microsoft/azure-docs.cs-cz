---
title: Změnit styl mapy v Azure Maps | Mapy Microsoft Azure
description: V tomto článku se seznámíte s funkcemi, které jsou k dispozici v sadě Microsoft Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 815b76f2a1c8872ff01439b126cb2ba1cdf27953
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133363"
---
# <a name="change-the-style-of-the-map"></a>Změna stylu mapy

Mapa podporuje několik různých [možností stylu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) , které lze nastavit při inicializaci mapy nebo později pomocí `setStyle` funkce Maps. V tomto článku se dozvíte, jak tyto možnosti stylu použít k přizpůsobení vzhledu map. Naučte se nastavit styl při načítání mapy a Naučte se nastavit nový styl mapy pomocí ovládacího prvku Výběr stylu.

## <a name="set-the-style-options"></a>Nastavení možností stylu 

Možnosti stylu lze předat do mapy, pokud jsou inicializovány nebo aktualizovány později pomocí `setStyle` funkce Maps.

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: true

    //Additional map options.
};

//Update the style options at anytime using setStyle function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: false
});
```

Následující nástroj ukazuje, jak různé možnosti stylu mění způsob, jakým se mapa vykresluje. Pokud chcete zobrazit prostorové budovy, přiblížte se k hlavnímu městu. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Možnosti stylu mapy" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>Možnosti stylu mapy</a> pera podle Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="choose-a-base-map-style"></a>Zvolit základní styl mapy

Jedna z nejběžnějších možností stylu mapy se používá ke změně stylu základní mapy, která je ve stylu. Mnohé z [podporovaných stylů mapy v Azure Maps](supported-map-styles.md) jsou dostupné v sadě web SDK. 

### <a name="set-base-map-style-on-map-load"></a>Nastavit styl základní mapy při načítání mapy


Styl mapy lze zadat při inicializaci mapy nastavením `style` Možnosti. V následujícím kódu `style` je možnost mapy nastavena na hodnotu `grayscale_dark` při inicializaci.

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additiona map options
);
```

<br/>

<iframe height='500' scrolling='no' title='Nastavení stylu při načítání map' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Nastavení stylu při načtení mapy</a> pomocí Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="update-the-base-map-style"></a>Aktualizuje základní styl mapy.

 Styl mapy lze aktualizovat pomocí `setStyle` funkce a nastavením `style` Možnosti na požadovaný styl mapy.

```javascript
map.setStyle({ style: 'satellite' });
```

V následujícím kódu po načtení instance mapy je styl mapy aktualizován z `road` na k `satellite` použití funkce [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setstyle-styleoptions-) .

<br/>

<iframe height='500' scrolling='no' title='Aktualizace stylu' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se, jak pero <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>aktualizuje styl</a> o Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="add-the-style-picker"></a>Přidat výběr stylu

Ovládací prvek pro výběr stylu poskytuje snadné použití tlačítka s panelem s plovoucím panelem, který lze použít pro koncového uživatele ke změně stylu mapy. Výběr stylu má dvě různé možnosti rozložení. Ve výchozím nastavení používá výběr stylu `icons` rozložení a zobrazuje celý styl mapy jako vodorovný řádek ikon. 

<center>

![Rozložení ikony výběru stylu](media/choose-map-style/style-picker-icon-layout.png)</center>

Je volána druhá možnost rozložení `list` a zobrazí seznam stylů mapy.  

<center>

![Rozložení seznamu výběru stylu](media/choose-map-style/style-picker-list-layout.png)</center>


Následující kód ukazuje, jak vytvořit instanci ovládacího prvku pro výběr stylu a přidat ho do pravého horního rohu mapy. Výběr stylu je nastaven tak, aby měl tmavý styl a zobrazila vybraná několik stylů mapy pomocí vrstvy seznamu.

```javascript
map.controls.add(new atlas.control.StyleControl({
    mapStyles: ['road', 'night', 'grayscale_dark', 'grayscale_light'],
    layout: 'list',
    style: 'dark'
}), {
    position: 'top-right'
}); 
```

Následující kód přidá ovládací prvek pro výběr stylu s výchozím nastavením na mapu, aby uživatel mohl snadno přepínat mezi různými styly mapy. Přepněte styl mapy pomocí ovládacího prvku styl mapy v pravém horním rohu.

<br/>

<iframe height='500' scrolling='no' title='Přidání výběru stylu' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Přidání výběru stylu</a> pomocí Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Ve výchozím nastavení, při použití cenové úrovně S0 Azure Maps, ovládací prvek pro výběr stylu vypíše všechny dostupné styly. Pokud chcete snížit počet stylů v tomto seznamu, předejte pole stylů, které chcete zobrazit v seznamu, do `mapStyle` Možnosti výběru stylu. Pokud používáte S1 a chcete zobrazit všechny dostupné styly, nastavte `mapStyles` možnost výběru stylu na `"all"` .

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [StyleControlOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions)

Přidejte ovládací prvky do svých map:

> [!div class="nextstepaction"]
> [Přidání mapových ovládacích prvků](map-add-controls.md)

> [!div class="nextstepaction"]
> [Přidání špendlíku](map-add-pin.md)

---
title: Změna stylu mapy v Azure Mapách | Mapy Microsoft Azure
description: V tomto článku se dozvíte o funkcích souvisejících se stylem, které jsou k dispozici ve webové sadě Microsoft Azure Maps SDK.
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b1c5d9b5cd2b6b9bfecf8a0af79699061003eec1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335691"
---
# <a name="change-the-style-of-the-map"></a>Změna stylu mapy

Mapa podporuje několik různých [možností stylu,](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) které lze nastavit při inicializování mapy nebo později pomocí funkce mapy. `setStyle` Tento článek ukazuje, jak pomocí těchto možností stylu přizpůsobit vzhled mapy. Naučte se nastavit styl při načítání mapy a naučte se nastavit nový styl mapy pomocí ovládacího prvku výběr stylu.

## <a name="set-the-style-options"></a>Nastavení voleb stylu 

Možnosti stylu mohou být předány do mapy, když `setStyle` je inicializována nebo aktualizována později pomocí funkce mapy.

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

Následující nástroj ukazuje, jak různé možnosti stylu mění způsob vykreslení mapy. Chcete-li vidět 3D budovy, přibližte se v blízkosti hlavního města. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Možnosti stylu mapy" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>možnosti</a> stylu<a href='https://codepen.io/azuremaps'>@azuremaps</a>mapy pera podle Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="choose-a-base-map-style"></a>Volba stylu základní mapy

Jedna z nejběžnějších možností stylu mapy se používá ke změně stylu základní mapy, která je stylizovaná. Mnoho [podporovaných stylů map v Mapách Azure](supported-map-styles.md) je k dispozici ve webové sdk. 

### <a name="set-base-map-style-on-map-load"></a>Nastavení stylu základní mapy na zatížení mapy


Styl mapy lze zadat při inicializaci `style` mapy nastavením volby. V následujícím kódu `style` je možnost mapy nastavena `grayscale_dark` na při inicializaci.

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additiona map options
);
```

<br/>

<iframe height='500' scrolling='no' title='Nastavení stylu při načtení mapy' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na nastavení stylu pera<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>na načtení mapy</a> pomocí Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="update-the-base-map-style"></a>Aktualizace stylu základní mapy

 Styl mapy lze aktualizovat `setStyle` pomocí funkce `style` a nastavení matné volby na požadovaný styl mapy.

```javascript
map.setStyle({ style: 'satellite' });
```

V následujícím kódu se po načtení instance mapy styl `road` `satellite` mapy aktualizuje z na funkci [setStyle.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setstyle-styleoptions-)

<br/>

<iframe height='500' scrolling='no' title='Aktualizace stylu' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero Aktualizace<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>stylu</a> podle Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="add-the-style-picker"></a>Přidání výběru stylu

Ovládací prvek výběr stylu poskytuje snadno použitelné tlačítko s informačním panelem, které může koncový uživatel použít ke změně stylu mapy. Výběr stylu má dvě různé možnosti rozložení. Ve výchozím nastavení výběr `icons` stylu používá rozložení a zobrazuje celý styl mapy jako vodorovný řádek ikon. 

<center>

![Rozložení ikony výběru stylu](media/choose-map-style/style-picker-icon-layout.png)</center>

Druhá možnost rozložení `list` se nazývá a zobrazí posuvný seznam stylů mapy.  

<center>

![Rozložení seznamu výběru stylů](media/choose-map-style/style-picker-list-layout.png)</center>


Následující kód ukazuje, jak vytvořit instanci ovládacího prvku výběr stylu a přidat ji do pravého horního rohu mapy. Výběr stylu je nastaven tak, aby měl tmavý styl a zobrazoval několik vybraných stylů mapy pomocí vrstvy seznamu.

```javascript
map.controls.add(new atlas.control.StyleControl({
    mapStyles: ['road', 'night', 'grayscale_dark', 'grayscale_light'],
    layout: 'list',
    style: 'dark'
}), {
    position: 'top-right'
}); 
```

Následující kód přidá ovládací prvek výběrstylu s výchozím nastavením na mapě, takže uživatel může snadno přepínat mezi různými styly mapy. Přepínejte styl mapy pomocí ovládacího prvku styl mapy v pravém horním rohu.

<br/>

<iframe height='500' scrolling='no' title='Přidání výběru stylu' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero přidání výběr<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>stylu</a> pomocí Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Ve výchozím nastavení při použití cenové úrovně S0 v Azure Maps ovládací prvek výběr stylu uvádí všechny dostupné styly. Chcete-li snížit počet stylů v tomto seznamu, přejděte do `mapStyle` volby výběru stylů pole stylů, které se mají zobrazit v seznamu. Pokud používáte S1 a chcete zobrazit všechny dostupné `mapStyles` styly, nastavte `"all"`volbu výběru stylů na .

## <a name="next-steps"></a>Další kroky

Chcete-li se dozvědět více o třídách a metodách použitých v tomto článku:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Možnosti stylu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [StyleControlOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions)

Přidání ovládacích prvků do map:

> [!div class="nextstepaction"]
> [Přidání mapových ovládacích prvků](map-add-controls.md)

> [!div class="nextstepaction"]
> [Přidání špendlíku](map-add-pin.md)

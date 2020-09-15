---
title: Změna stylu Azure Maps webového Ovládací prvek Mapa
description: Naučte se, jak změnit styl a možnosti mapy. Viz postup přidání ovládacího prvku pro výběr stylu na mapu v Azure Maps tak, aby uživatelé mohli přepínat mezi různými styly.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-javascript
ms.openlocfilehash: fe07d6d54296a3c55cb0b2cadb4d4ae0a311873b
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90089560"
---
# <a name="change-the-style-of-the-map"></a>Změna stylu mapy

Mapový ovládací prvek podporuje několik různých [možností stylu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) mapy a [základní styly mapy](supported-map-styles.md). Všechny styly lze nastavit při inicializaci mapového ovládacího prvku. Nebo můžete nastavit styly pomocí funkce mapového ovládacího prvku `setStyle` . V tomto článku se dozvíte, jak pomocí těchto možností stylu přizpůsobit vzhled mapy. Naučíte se také, jak implementovat ovládací prvek výběru stylu na mapě. Ovládací prvek pro výběr stylu umožňuje uživateli přepínat mezi různými základními styly.

## <a name="set-map-style-options"></a>Nastavení možností stylu mapy

Možnosti stylu lze nastavit při inicializaci webového ovládacího prvku. Nebo můžete aktualizovat možnosti stylu voláním funkce mapového ovládacího prvku `setStyle` . Chcete-li zobrazit všechny dostupné možnosti stylu, přečtěte si téma [Možnosti stylu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions).

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: false,
    showLogo = true,
    showFeedbackLink = true,
    style='road'

    //Additional map options.
};

//Update the style options at anytime using `setStyle` function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: true,
    showLogo = false,
    showFeedbackLink = false
});
```

Následující nástroj ukazuje, jak různé možnosti stylu mění způsob, jakým se mapa vykresluje. Pokud chcete zobrazit prostorové budovy, přiblížte se k hlavnímu městu.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Možnosti stylu mapy" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>Možnosti stylu mapy</a> pera podle Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="set-a-base-map-style"></a>Nastavit základní styl mapy

Můžete také inicializovat mapový ovládací prvek jedním ze [základních stylů mapy](supported-map-styles.md) , které jsou k dispozici v sadě web SDK. Potom můžete pomocí `setStyle` funkce aktualizovat základní styl s jiným stylem mapy.

### <a name="set-a-base-map-style-on-initialization"></a>Nastavení základního stylu mapy při inicializaci

Základní styly mapového ovládacího prvku lze nastavit během inicializace. V následujícím kódu `style` je možnost mapového ovládacího prvku nastavena na [ `grayscale_dark` základní styl mapy](supported-map-styles.md#grayscale_dark).  

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additional map options
);
```

<br/>

<iframe height='500' scrolling='no' title='Nastavení stylu při načítání map' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Nastavení stylu při načtení mapy</a> pomocí Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="update-the-base-map-style"></a>Aktualizuje základní styl mapy.

Základní styl mapy lze aktualizovat pomocí `setStyle` funkce a nastavením `style` Možnosti buď změnit na jiný základní styl mapy, nebo přidat další možnosti stylu.

```javascript
map.setStyle({ style: 'satellite' });
```

V následujícím kódu po načtení instance mapy je styl mapy aktualizován z `grayscale_dark` na k `satellite` použití funkce [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#setstyle-styleoptions-) .

<br/>

<iframe height='500' scrolling='no' title='Aktualizace stylu' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se, jak pero <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>aktualizuje styl</a> o Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-the-style-picker-control"></a>Přidat ovládací prvek pro výběr stylu

Ovládací prvek pro výběr stylu poskytuje snadné použití tlačítka s panelem s plovoucím panelem, který může koncový uživatel použít k přepínání mezi základními styly.

Výběr stylu má dvě různé možnosti rozložení: `icon` a `list` . Výběr stylu také umožňuje zvolit dvě různé možnosti ovládacího prvku pro výběr stylu `style` : `light` a `dark` . V tomto příkladu výběr stylu používá `icon` rozložení a v podobě ikon zobrazuje seznam stylů základní mapy. Výběr ovládacího prvku stylu obsahuje následující základní sadu stylů: `["road", "grayscale_light", "grayscale_dark", "night", "road_shaded_relief"]` . Další informace o možnostech ovládacího prvku Výběr stylu naleznete v tématu [možnosti ovládacího prvku stylu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions).

Následující obrázek ukazuje ovládací prvek pro výběr stylu zobrazený v `icon` rozložení.

:::image type="content" source="./media/choose-map-style/style-picker-icon-layout.png" alt-text="Rozložení ikony výběru stylu":::

Následující obrázek ukazuje ovládací prvek pro výběr stylu zobrazený v `list` rozložení.

:::image type="content" source="./media/choose-map-style/style-picker-list-layout.png" alt-text="Rozložení seznamu výběru stylu":::

> [!IMPORTANT]
> Ve výchozím nastavení ovládací prvek pro výběr stylu vypíše všechny styly, které jsou k dispozici v rámci cenové úrovně S0 Azure Maps. Pokud chcete snížit počet stylů v tomto seznamu, předejte pole stylů, které chcete zobrazit v seznamu, do `mapStyle` Možnosti výběru stylu. Pokud používáte S1 a chcete zobrazit všechny dostupné styly, nastavte `mapStyles` možnost výběru stylu na `"all"` .

Následující kód ukazuje, jak přepsat výchozí `mapStyles` seznam základních stylů. V tomto příkladu nastavujeme `mapStyles` možnost zobrazení seznamu základních stylů, které chceme zobrazit pomocí ovládacího prvku pro výběr stylu.

<br/>

<iframe height='500' scrolling='no' title='Přidání výběru stylu' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Přidání výběru stylu</a> pomocí Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

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

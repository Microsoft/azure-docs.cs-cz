---
title: Funkce stylů mapy | Mapy Microsoft Azure
description: V tomto článku se dozvíte o funkcích souvisejících s funkcemi, které jsou k dispozici v sadě Microsoft Azure Maps Web SDK.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: cabf39f017afe440c883a63db57643c5c5367128
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77189745"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Zvolit styl mapy v Azure Maps

Mnohé z [podporovaných stylů mapy v Azure Maps](./supported-map-styles.md) jsou dostupné v sadě web SDK. Tento článek ukazuje, jak používat funkce související se styly. Naučte se nastavit styl při načítání mapy a Naučte se nastavit nový styl mapy pomocí ovládacího prvku Výběr stylu.

## <a name="set-style-on-map-load"></a>Nastavit styl při načítání mapy

V následujícím kódu je možnost `style` v mapě nastavena na `grayscale_dark` při inicializaci.

<br/>

<iframe height='500' scrolling='no' title='Nastavení stylu při načítání map' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Nastavení pera styl při načtení mapy</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="update-the-style"></a>Aktualizace stylu

V následujícím kódu po načtení instance mapy je styl mapy aktualizován z `road` na `satellite` pomocí funkce [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) .

<br/>

<iframe height='500' scrolling='no' title='Aktualizace stylu' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se, jak pero <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>aktualizuje styl</a> o Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-the-style-picker"></a>Přidat výběr stylu

Následující kód přidá do mapy [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) , takže uživatel může snadno přepínat mezi různými styly mapy. Přepněte styl mapy pomocí ovládacího prvku styl mapy v pravém horním rohu.

<br/>

<iframe height='500' scrolling='no' title='Přidání výběru stylu' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Přidání výběru stylu</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Ve výchozím nastavení, při použití cenové úrovně S0 Azure Maps, ovládací prvek pro výběr stylu vypíše všechny dostupné styly. Pokud chcete snížit počet stylů v tomto seznamu, předejte pole stylů, které chcete zobrazit v seznamu, do možnosti `mapStyle` výběru stylu. Pokud používáte S1 a chcete zobrazit všechny dostupné styly, nastavte `mapStyles` možnosti výběru stylu na `"all"`.

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Přidejte ovládací prvky do svých map:

> [!div class="nextstepaction"]
> [Přidat mapové ovládací prvky](map-add-controls.md)

> [!div class="nextstepaction"]
> [Přidat PIN kód](map-add-pin.md)

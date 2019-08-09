---
title: Funkce stylů mapy v Azure Maps | Microsoft Docs
description: Přečtěte si o funkcích souvisejících s Azure Maps stylu.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: eb667c398be0bd51e05a6b65d416d5bce54e4386
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881971"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Zvolit styl mapy v Azure Maps

Mnohé z [podporovaných stylů mapy v Azure Maps](./supported-map-styles.md) jsou dostupné v sadě web SDK. Tento článek ukazuje, jak použít funkce související se styly k nastavení stylu při načítání mapy, nastavení nového stylu a použití ovládacího prvku pro výběr stylu.

## <a name="set-style-on-map-load"></a>Nastavit styl při načítání mapy

V následujícím kódu `style` je možnost mapy `grayscale_dark` nastavena na hodnotu při inicializaci.

<br/>

<iframe height='500' scrolling='no' title='Nastavení stylu při načítání map' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Nastavení stylu při načtení mapy</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="update-the-style"></a>Aktualizace stylu

V následujícím kódu po načtení instance mapy je styl mapy aktualizován z `road` na k `satellite` použití funkce [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) mapy.

<br/>

<iframe height='500' scrolling='no' title='Aktualizace stylu' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se, jak pero <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>aktualizuje styl</a> o Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-the-style-picker"></a>Přidat výběr stylu

Následující kód přidá na mapu [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) , aby uživatel mohl snadno přepínat mezi různými styly mapy. 

<br/>

<iframe height='500' scrolling='no' title='Přidání výběru stylu' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Přidání výběru stylu</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Ve výchozím nastavení ovládací prvek pro výběr stylu zobrazí seznam všech dostupných stylů při použití cenové úrovně S0 Azure Maps ve výchozím nastavení. Pokud chcete snížit počet stylů v tomto seznamu, předejte pole stylů, které chcete zobrazit v seznamu, do `mapStyle` možnosti výběru stylu. Pokud používáte S1 a chcete zobrazit všechny dostupné styly, nastavte `mapStyles` možnost výběru stylu na. `"all"`

## <a name="next-steps"></a>Další postup

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Přidejte ovládací prvky do svých map:

> [!div class="nextstepaction"]
> [Přidat mapové ovládací prvky](map-add-controls.md)

> [!div class="nextstepaction"]
> [Přidat PIN kód](map-add-pin.md)

---
title: Přidat PIN kód s Azure Maps | Dokumentace Microsoftu
description: Postup přidání špendlíku na mapě jazyka Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 35b22e28a6a339af6f6f6e8db0655f2ae6de0118
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46122165"
---
# <a name="add-pins-to-the-map"></a>Přidat PIN kódy do mapy

Tento článek ukazuje, jak přidat do mapy kódu pin.  

## <a name="understand-the-code"></a>Vysvětlení kódu

<iframe height='500' scrolling='no' title='Přidání špendlíku na mapě' src='//codepen.io/azuremaps/embed/ZrVpEa/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/ZrVpEa/'>přidání špendlíku na mapě</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt Map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

V druhém bloku kódu pin je vytvořen a přidán do mapy. Je PIN kód [funkce](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) z [bodu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) s [PinProperties](https://docs.microsoft.com/javascript/api/azure-maps-control/models.pinproperties?view=azure-iot-typescript-latest) jako jeho vlastnost funkce. Použití `new atlas.data.Feature(new atlas.data.Point())` k vytvoření PIN kódu a definovat jeho vlastnosti. Vrstva PIN kód je pole PIN kódů. Použití [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins) funkce třídy mapy přidat vrstvu PIN kódu do mapy a definovat vlastnosti vrstvy PIN kód. Zobrazit vlastnosti vrstvy PIN kód na [PinLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.pinlayeroptions?view=azure-iot-typescript-latest).

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Naleznete v následujících článcích pro další ukázky kódu pro přidání do vaše mapy:

> [!div class="nextstepaction"]
> [Přidání vyskakovacího okna](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Přidání obrazce](./map-add-shape.md)
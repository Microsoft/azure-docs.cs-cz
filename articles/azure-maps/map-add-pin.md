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
ms.openlocfilehash: 0dafb09e1704e8e446b034975f0c25a740050599
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382580"
---
# <a name="add-pins-to-the-map"></a>Přidat PIN kódy do mapy

Tento článek ukazuje, jak přidat do mapy kódu pin.  

## <a name="understand-the-code"></a>Vysvětlení kódu

<iframe height='500' scrolling='no' title='Přidání špendlíku na mapě' src='//codepen.io/azuremaps/embed/ZrVpEa/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/ZrVpEa/'>přidání špendlíku na mapě</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

V druhém bloku kódu pin je vytvořen a přidán do mapy. Je PIN kód [funkce](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) z [bodu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) s [PinProperties](https://docs.microsoft.com/javascript/api/azure-maps-control/models.pinproperties?view=azure-iot-typescript-latest) jako jeho vlastnost funkce. Použití `new atlas.data.Feature(new atlas.data.Point())` k vytvoření PIN kódu a definovat jeho vlastnosti. Vrstva PIN kód je pole PIN kódů. Použití [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins) funkce třídy mapy přidat vrstvu PIN kódu do mapy a definovat vlastnosti vrstvy PIN kód. Zobrazit vlastnosti vrstvy PIN kód na [PinLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.pinlayeroptions?view=azure-iot-typescript-latest). 

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku: 
* [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)
    
Další příklady kódu pro přidání do vaše mapy najdete v následujících článcích: 
* [Přidání vyskakovacího okna](./map-add-popup.md)
* [Přidání obrazce](./map-add-shape.md)


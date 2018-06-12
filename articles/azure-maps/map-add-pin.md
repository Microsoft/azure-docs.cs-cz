---
title: Přidat pin s Azure mapy | Microsoft Docs
description: Postup přidání PIN kódu Javascript mapy
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 094abe08c0c88c7561185675ceb8529be2c87a0a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294644"
---
# <a name="add-pins-to-the-map"></a>Přidání kódů PIN do mapy

Tento článek ukazuje, jak přidat kód pin na mapu.  

## <a name="understand-the-code"></a>Vysvětlení kódu

<iframe height='500' scrolling='no' title='Přidat kód pin na mapu' src='//codepen.io/azuremaps/embed/ZrVpEa/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>V tématu pera <a href='https://codepen.io/azuremaps/pen/ZrVpEa/'>přidat pin na mapu</a> pomocí Azure mapy (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map. Můžete zobrazit [vytvoření mapy](./map-create.md) pokyny.

V druhé blok kódu pin je vytvořit a přidat do mapy. Kód pin je [funkce](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest) z [bodu](https://docs.microsoft.com/javascript/api/azure-maps-javascript/point?view=azure-iot-typescript-latest) s [PinProperties](https://docs.microsoft.com/javascript/api/azure-maps-javascript/pinproperties?view=azure-iot-typescript-latest) jako jeho vlastnost funkce. Použití `new atlas.data.Feature(new atlas.data.Point())` vytvořit kód pin a definovat jeho vlastnosti. Vrstva kódu pin je pole PIN kódů. Použití [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins) funkce třídy map přidat pin vrstvu mapy a definovat vlastnosti vrstvy PIN kód. Informace naleznete ve vlastnostech vrstvy PIN kódu v [PinLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/pinlayeroptions?view=azure-iot-typescript-latest). 

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku: 
* [mapy](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
    
Další příklady kódu pro přidání do vaší mapy najdete v následujících článcích: 
* [Přidat místní okno](./map-add-popup.md)
* [Přidání obrazce](./map-add-shape.md)


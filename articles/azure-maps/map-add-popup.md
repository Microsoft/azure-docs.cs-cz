---
title: Přidání vyskakovacího okna s Azure Maps | Dokumentace Microsoftu
description: Postup přidání automaticky otevíraného okna na mapu jazyka Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0f86578e33e5c6a2d6528e2deb1c8068a0c94d01
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2018
ms.locfileid: "42747101"
---
# <a name="add-a-popup-to-the-map"></a>Přidání vyskakovacího okna mapy

Tento článek ukazuje, jak přidat automaticky otevíraného okna na mapu.  

## <a name="understand-the-code"></a>Vysvětlení kódu

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='Přidání vyskakovacího okna mapy' src='//codepen.io/azuremaps/embed/zRyKxj/?height=545&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/zRyKxj/'>přidání vyskakovacího okna k mapě</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

Druhý bloku kódu vytvoří kód pin a přidejte ho do mapy. Můžete zobrazit [přidání špendlíku na mapě](./map-add-pin.md) pokyny.

Třetí bloku kódu vytvoří obsah, který se zobrazí v automaticky otevíraném okně. Obsah automaticky otevírané okno se prvek jazyka HTML. 

Čtvrtý bloku kódu vytvoří [místní nabídky objektu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) prostřednictvím `new atlas.Popup()`. Automaticky otevírané okno Vlastnosti, jako je obsah a pozici jsou součástí [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.popupoptions?view=azure-iot-typescript-latest). PopupOptions lze definovat v konstruktoru automaticky otevírané okno, nebo prostřednictvím [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) funkce třídy automaticky otevíraného okna.

Poslední blok kódu používá [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener) funkce třídy map pro naslouchání události mouseover na kódy PIN a používá [otevřete](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) funkce třídy automaticky otevírané okno Otevřít automaticky otevírané okno, pokud dojde k události.


## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku: 

* [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener)
* [Automaticky otevíraného okna](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [Otevřít](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open)
    * [Zavřít](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#close)
    
Další příklady kódu pro přidání do vaše mapy najdete v následujících článcích: 
* [Přidání obrazce](./map-add-shape.md)
* [Přidat vlastního HTML](./map-add-custom-html.md)

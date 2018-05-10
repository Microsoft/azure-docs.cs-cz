---
title: Přidat místní okno s Azure mapy | Microsoft Docs
description: Postup přidání místní okno Javascript mapy
services: azure-maps
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: codepen
ms.openlocfilehash: 7425081597bfa9379594597277555ee30809c4e6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="add-a-popup-to-the-map"></a>Přidat místní okno do mapy

Tento článek ukazuje, jak přidat místní okno s mapou.  

## <a name="understand-the-code"></a>Pochopení kódu

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='Přidat místní okno na mapu' src='//codepen.io/azuremaps/embed/zRyKxj/?height=545&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>V tématu pera <a href='https://codepen.io/azuremaps/pen/zRyKxj/'>přidat místní okno na mapu</a> pomocí Azure mapy (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map. Můžete zobrazit [vytvoření mapy](./map-create.md) pokyny.

Druhý blok kódu vytvoří kód pin a přidejte ji do mapy. Můžete zobrazit [přidat pin na mapě](./map-add-pin.md) pokyny.

Třetí blok kódu vytvoří obsah, který se má zobrazit v rámci místní okno. Místní obsah je HTML element. 

Vytvoří čtvrtý blok kódu [místní objekt](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest) prostřednictvím `new atlas.Popup()`. Místní nabídka Vlastnosti, například obsah a pozice jsou součástí [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popupoptions?view=azure-iot-typescript-latest). PopupOptions lze definovat v automaticky otevřeném okně konstruktor nebo prostřednictvím [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions) funkce místní třídy.

Používá posledního bloku kódu [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addeventlistener) funkce třídy map naslouchat myš nad událostí na kódy PIN a používá [otevřete](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open) funkce třídy místní nabídka otevřít automaticky otevřeném okně. Pokud dojde k události.


## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku: 

* [mapy](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addeventlistener)
* [Místní nabídka](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [Otevřete](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [Zavřete](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)

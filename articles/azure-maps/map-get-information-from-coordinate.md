---
title: Zobrazit informace o souřadnice s Azure mapy | Microsoft Docs
description: Postup zobrazení informací o adresu na mapě, když uživatel vybere souřadnice
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 3caae47f7f8f5f9c917e3a59513e6cd33cdcaeae
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34600489"
---
# <a name="get-information-from-a-coordinate"></a>Získání informací ze souřadnice

Tento článek ukazuje, jak telefonování adresu zpětného vyhledávání, a při kliknutí myší zobrazit adresu kliknutelnou umístění v místní okno. 

## <a name="understand-the-code"></a>Vysvětlení kódu

<iframe height='500' scrolling='no' title='Získání informací ze souřadnice' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>V tématu pera <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>získat informace z souřadnice</a> pomocí Azure mapy (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map. Můžete zobrazit [vytvoření mapy](./map-create.md) pokyny.

Druhý blok kódu aktualizuje styl ukazatelem myši na ukazatel.

Třetí blok kódu vytvoří místní okno. Můžete zobrazit [přidat místní okno na mapě](./map-add-popup.md) pokyny.

Poslední blok kódu přidá modul pro naslouchání událostí pro kliknutí myší. Po kliknutí myší, odešle [XMLHttpRequest](https://xhr.spec.whatwg.org/) k [mapy Reverse adres rozhraní API Azure Search](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse). Pro úspěšné odpovědi, shromažďuje adresy pro kliknutelnou umístění a definuje místní obsah a umístění prostřednictvím [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions) funkce třídy automaticky otevřeném okně.

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku: 
* [Zpětná adresa vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [mapy](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addeventlistener)
* [Místní nabídka](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [Otevřete](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [Zavřete](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)

Další příklady kódu pro přidání do vaší mapy najdete v následujících článcích: 
* [Zobrazit pokyny od A do B](./map-route.md)
* [Zobrazit provoz](./map-show-traffic.md)

---
title: Zobrazení informací o koordinují s Azure Maps | Dokumentace Microsoftu
description: Jak zobrazit informace o adresu na mapě, když uživatel vybere souřadnice
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b640346b0d6f490457e1e82a65c0d3f373d658d3
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/11/2018
ms.locfileid: "42054417"
---
# <a name="get-information-from-a-coordinate"></a>Získání informací ze souřadnice

Tento článek popisuje, jak telefonování adresa zpětného vyhledávání, a při kliknutí myší zobrazí adresu kliknutí na umístění v automaticky otevíraném okně. 

## <a name="understand-the-code"></a>Vysvětlení kódu

<iframe height='500' scrolling='no' title='Získání informací ze souřadnice' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>získání informací ze souřadnice</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

Druhý bloku kódu aktualizuje stylu ukazatele myši na ukazatel.

Třetí bloku kódu vytvoří automaticky otevíraného okna. Můžete zobrazit [přidání vyskakovacího okna na mapě](./map-add-popup.md) pokyny.

Poslední blok kódu přidá naslouchací proces událostí pro kliknutí myší. Po kliknutí myší, odešle [XMLHttpRequest](https://xhr.spec.whatwg.org/) k [mapy zpětná adresa rozhraní API Azure Search](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse). Pro úspěšné odpovědi shromažďuje adresy pro kliknutí na umístění a definuje obsah automaticky otevíraného okna a umístění prostřednictvím [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions) funkce třídy automaticky otevíraného okna

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku: 
* [Adresa zpětného vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addeventlistener)
* [Automaticky otevíraného okna](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [Otevřít](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [Zavřít](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)

Další příklady kódu pro přidání do vaše mapy najdete v následujících článcích: 
* [Zobrazení pokynů pro trasu z A do B](./map-route.md)
* [Zobrazení provozu](./map-show-traffic.md)

---
title: Zobrazení informací o koordinují s Azure Maps | Dokumentace Microsoftu
description: Jak zobrazit informace o adresu na mapě, když uživatel vybere souřadnice
author: jingjing-z
ms.author: jinzh
ms.date: 11/15/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 20616adf649924a13e80411aa5135889a175f442
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55750184"
---
# <a name="get-information-from-a-coordinate"></a>Získání informací ze souřadnice

Tento článek ukazuje, jak provést adresa zpětného vyhledávání, který zobrazuje adresu kliknutí na místní umístění.

Existují dva způsoby, jak vytvořit adresu zpětného vyhledávání. Jedním ze způsobů je dotaz [mapy zpětná adresa rozhraní API Azure Search](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) prostřednictvím modulu service. Druhý způsob je, aby [XMLHttpRequest](https://xhr.spec.whatwg.org/) rozhraní API najít adresu. Oba způsoby, jak jsou zkoumány níže.

## <a name="make-a-reverse-search-request-via-service-module"></a>Vytvořit žádost o zpětné vyhledávání prostřednictvím modulu service

<iframe height='500' scrolling='no' title='Získání informací ze souřadnice (modulu Service)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>získání informací ze souřadnice (modulu Service)</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

První blok kódu vytvoří objekt Map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

Řádek v druhé bloku kódu vytváří instanci služby klienta.

Třetí bloku kódu aktualizuje stylu ukazatele myši na ukazatel a [automaticky otevírané okno](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) objektu. Můžete zobrazit [přidání vyskakovacího okna na mapě](./map-add-popup.md) pokyny.

Čtvrtý bloku kódu přidá [naslouchací proces událostí](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) pro kliknutí myší. Po kliknutí myší vytvoří se souřadnicemi místem kliknutí vyhledávacího dotazu. Poté použije na mapě [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest) koncový bod pro dotazování adresu souřadnic.

Pro úspěšné odpovědi shromažďuje adresy pro kliknutí na umístění a definuje obsah automaticky otevíraného okna a umístění prostřednictvím [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) funkce třídy automaticky otevíraného okna.

Změna kurzor, objekt automaticky otevíraného okna a událost click jsou vytvořeny v objektu map [naslouchací proces událostí zatížení](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) zajistit mapování zatížení plně předtím, než mohou načíst informace o souřadnice.

## <a name="make-a-reverse-search-request-via-xmlhttprequest"></a>Vytvořit žádost o zpětné vyhledávání prostřednictvím XMLHttpRequest

<iframe height='500' scrolling='no' title='Získání informací ze souřadnice' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>získání informací ze souřadnice</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

První blok výše uvedený kód vytvoří objekt Map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

Druhý bloku kódu aktualizuje stylu ukazatele myši na ukazatel a [automaticky otevírané okno](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) objektu. Můžete zobrazit [přidání vyskakovacího okna na mapě](./map-add-popup.md) pokyny.

Třetí bloku kódu přidá naslouchací proces událostí pro kliknutí myší. Po kliknutí myší, odešle [XMLHttpRequest](https://xhr.spec.whatwg.org/) k [mapy zpětná adresa rozhraní API Azure Search](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) dotazu pro kliknutí na souřadnice adresy. Pro úspěšné odpovědi shromažďuje adresy pro kliknutí na umístění a definuje obsah automaticky otevíraného okna a umístění prostřednictvím [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) funkce třídy automaticky otevíraného okna.

Změna kurzor, objekt automaticky otevíraného okna a událost click jsou vytvořeny v objektu map [naslouchací proces událostí zatížení](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) zajistit mapování zatížení plně předtím, než mohou načíst informace o souřadnice.

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Automaticky otevíraného okna](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Naleznete v následujících článcích pro úplné příklady:

> [!div class="nextstepaction"]
> [Zobrazení pokynů pro trasu z A do B](./map-route.md)

> [!div class="nextstepaction"]
> [Zobrazení provozu](./map-show-traffic.md)

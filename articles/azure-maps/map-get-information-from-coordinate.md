---
title: Zobrazení informací o koordinují s Azure Maps | Dokumentace Microsoftu
description: Jak zobrazit informace o adresu na mapě, když uživatel vybere souřadnice
author: jingjing-z
ms.author: jinzh
ms.date: 08/31/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 331e687c40f21b0bf6074239969848c632682773
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2018
ms.locfileid: "43667477"
---
# <a name="get-information-from-a-coordinate"></a>Získání informací ze souřadnice

Tento článek popisuje, jak telefonování adresa zpětného vyhledávání, a při kliknutí myší zobrazí adresu kliknutí na umístění v automaticky otevíraném okně.

## <a name="understand-the-code"></a>Vysvětlení kódu

<iframe height='500' scrolling='no' title='Získání informací ze souřadnice (modulu Service)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>získání informací ze souřadnice (modulu Service)</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

Řádek v druhé bloku kódu vytváří instanci služby klienta.

Třetí bloku kódu aktualizuje stylu ukazatele myši na ukazatel.

Čtvrtý blok kódu vytvoří automaticky otevíraného okna. Můžete zobrazit [přidání vyskakovacího okna na mapě](./map-add-popup.md) pokyny.

Poslední blok kódu přidá naslouchací proces událostí pro kliknutí myší. Při kliknutí myší vytvoří vyhledávací dotaz s souřadnice místem kliknutí. Potom použije na mapě [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest#getsearchaddressreverse) koncový bod pro dotazování adresu souřadnice.

Pro úspěšné odpovědi shromažďuje adresy pro kliknutí na umístění a definuje obsah automaticky otevíraného okna a umístění prostřednictvím [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) funkce třídy automaticky otevíraného okna.

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku: 
* [Adresa zpětného vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener)
* [Automaticky otevíraného okna](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [Otevřít](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open)
    * [Zavřít](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#close)

Další příklady kódu pro přidání do vaše mapy najdete v následujících článcích:
* [Zobrazení pokynů pro trasu z A do B](./map-route.md)
* [Zobrazení provozu](./map-show-traffic.md)

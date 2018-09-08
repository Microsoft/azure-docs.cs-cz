---
title: Zobrazení informací o koordinují s Azure Maps | Dokumentace Microsoftu
description: Jak zobrazit informace o adresu na mapě, když uživatel vybere souřadnice
author: jingjing-z
ms.author: jinzh
ms.date: 09/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: d1baa4adc555e65c4a25928d19f201dba6109142
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44157680"
---
# <a name="get-information-from-a-coordinate"></a>Získání informací ze souřadnice

Tento článek popisuje, jak telefonování adresa zpětného vyhledávání, a při kliknutí myší zobrazí adresu kliknutí na umístění v automaticky otevíraném okně.

Existují dva způsoby, jak vytvořit adresu zpětného vyhledávání, jednou je pomocí dotazu [mapy zpětná adresa rozhraní API Azure Search](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) prostřednictvím služby modulu a druhý je tím, že [XMLHttpRequest](https://xhr.spec.whatwg.org/) rozhraní API k dotazování Adresa. Můžeme probírat i níže.

## <a name="use-the-service-module-to-make-a-reverse-address-search"></a>Používejte modul služby, aby adresa zpětného vyhledávání

### <a name="understand-the-code"></a>Vysvětlení kódu

<iframe height='500' scrolling='no' title='Získání informací ze souřadnice (modulu Service)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>získání informací ze souřadnice (modulu Service)</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

Řádek v druhé bloku kódu vytváří instanci služby klienta.

Třetí bloku kódu aktualizuje stylu ukazatele myši na ukazatel.

Čtvrtý blok kódu vytvoří automaticky otevíraného okna. Můžete zobrazit [přidání vyskakovacího okna na mapě](./map-add-popup.md) pokyny.

Poslední blok kódu přidá naslouchací proces událostí pro kliknutí myší. Při kliknutí myší vytvoří vyhledávací dotaz s souřadnice místem kliknutí. Potom použije na mapě [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest#getsearchaddressreverse) koncový bod pro dotazování adresu souřadnice.

Pro úspěšné odpovědi shromažďuje adresy pro kliknutí na umístění a definuje obsah automaticky otevíraného okna a umístění prostřednictvím [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) funkce třídy automaticky otevíraného okna.

## <a name="use-xmlhttprequest-to-make-a-reverse-address-search"></a>Používejte XMLHTTPRequest, aby adresa zpětného vyhledávání

### <a name="understand-the-code"></a>Vysvětlení kódu

<iframe height='500' scrolling='no' title='Získání informací ze souřadnice' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>získání informací ze souřadnice</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

Druhý bloku kódu aktualizuje stylu ukazatele myši na ukazatel.

Třetí bloku kódu vytvoří automaticky otevíraného okna. Můžete zobrazit [přidání vyskakovacího okna na mapě](./map-add-popup.md) pokyny.

Poslední blok kódu přidá naslouchací proces událostí pro kliknutí myší. Po kliknutí myší, odešle [XMLHttpRequest](https://xhr.spec.whatwg.org/) k [mapy zpětná adresa rozhraní API Azure Search](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse). Pro úspěšné odpovědi shromažďuje adresy pro kliknutí na umístění a definuje obsah automaticky otevíraného okna a umístění prostřednictvím [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) funkce třídy automaticky otevíraného okna

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

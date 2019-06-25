---
title: Zobrazení informací o koordinují s Azure Maps | Dokumentace Microsoftu
description: Jak zobrazit informace o adresu na mapě, když uživatel vybere souřadnice
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 3b635ab96f1438377cb0d282c17304c2531185be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66357806"
---
# <a name="get-information-from-a-coordinate"></a>Získání informací ze souřadnice

Tento článek ukazuje, jak provést adresa zpětného vyhledávání, který zobrazuje adresu kliknutí na místní umístění.

Existují dva způsoby, jak vytvořit adresu zpětného vyhledávání. Jedním ze způsobů je dotaz [mapy zpětná adresa rozhraní API Azure Search](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) prostřednictvím modulu service. Druhý způsob je využívat [Fetch API](https://fetch.spec.whatwg.org/) na vytvořit žádost o [mapy zpětná adresa rozhraní API Azure Search](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) najít adresu. Oba způsoby, jak jsou zkoumány níže.

## <a name="make-a-reverse-search-request-via-service-module"></a>Vytvořit žádost o zpětné vyhledávání prostřednictvím modulu service

<iframe height='500' scrolling='no' title='Získání informací ze souřadnice (modulu Service)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>získání informací ze souřadnice (modulu Service)</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map a nastaví mechanismus ověřování využívat klíč předplatného. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

Vytvoří druhý blok kódu `SubscriptionKeyCredentialPolicy` k ověření požadavků HTTP ve službě Azure Maps se klíč předplatného. Pak bude `atlas.service.MapsURL.newPipeline()` přijímá `SubscriptionKeyCredential` zásady a vytvoří [kanálu](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) instance. `searchURL` Představuje adresu URL ke službě Azure Maps [hledání](https://docs.microsoft.com/rest/api/maps/search) operace.

Aktualizace stylu ukazatele myši na ukazatel na třetí blok kódu a vytvoří [automaticky otevírané okno](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) objektu. Můžete zobrazit [přidání vyskakovacího okna na mapě](./map-add-popup.md) pokyny.

Čtvrtý bloku kódu přidá kliknutí myší [naslouchací proces událostí](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Když se aktivuje, vytvoří vyhledávací dotaz se souřadnicemi místem kliknutí. Poté použije modul služby [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-) metodu dotazu [získat adresu Reverse rozhraní API pro vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) adresy souřadnice. Kolekce funkcí GeoJSON z odpovědi se pak extrahuje pomocí `geojson.getFeatures()` metody.

Pátý bloku kódu nastaví obsah HTML, který automaticky otevírané okno pro zobrazení adresy odpovědi pro kliknutí na souřadnice polohy.

Změna kurzor, objekt automaticky otevíraného okna a událost click jsou vytvořeny v objektu map [naslouchací proces událostí zatížení](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) zajistit mapování zatížení plně předtím, než mohou načíst informace o souřadnice.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Vytvořit žádost o zpětné vyhledávání přes rozhraní Fetch API

Klikněte na mapě, vytvořte žádost na reverzní geokód pro danou lokaci pomocí načtení.

<iframe height='500' scrolling='no' title='Získání informací ze souřadnice' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>získání informací ze souřadnice</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map a nastaví mechanismus ověřování využívat klíč předplatného. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

Druhý bloku kódu aktualizuje stylu ukazatele myši na ukazatel a [automaticky otevírané okno](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) objektu. Můžete zobrazit [přidání vyskakovacího okna na mapě](./map-add-popup.md) pokyny.

Třetí bloku kódu přidá naslouchací proces událostí pro kliknutí myší. Po kliknutí myší, využívá [Fetch API](https://fetch.spec.whatwg.org/) k dotazu [mapy zpětná adresa rozhraní API Azure Search](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) kliknutí na souřadnice adresy. Pro úspěšné odpovědi shromažďuje adresy pro kliknutí na umístění a definuje obsah automaticky otevíraného okna a umístění prostřednictvím [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) funkce třídy automaticky otevíraného okna.

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

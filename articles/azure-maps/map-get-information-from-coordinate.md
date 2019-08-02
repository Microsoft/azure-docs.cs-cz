---
title: Zobrazit informace o souřadnicích pomocí Azure Maps | Microsoft Docs
description: Jak zobrazit informace o adrese na mapě, když uživatel vybere souřadnici
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: df0966569a753d5000414451a2b69f1e69449b2c
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638667"
---
# <a name="get-information-from-a-coordinate"></a>Získání informací ze souřadnice

Tento článek ukazuje, jak vytvořit reverzní hledání na základě adresy, které zobrazuje adresu kliknutí na překryvné okno.

Existují dva způsoby, jak provést zpětné vyhledávání v rámci adresy. Jedním ze způsobů je dotazování [rozhraní API pro hledání zpětné adresy Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) prostřednictvím modulu služby. Druhým způsobem je využít [rozhraní API pro načtení](https://fetch.spec.whatwg.org/) k vytvoření požadavku na [Azure Maps rozhraní API pro vyhledávání zpětného adres](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) , aby se vyhledala adresa. Níže jsou uvedené následující otázky:

## <a name="make-a-reverse-search-request-via-service-module"></a>Vytvoření požadavku zpětného vyhledávání přes modul služby

<iframe height='500' scrolling='no' title='Získání informací ze souřadnice (modul služby)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se, <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>jak pero získá informace ze souřadnice (modulu služby)</a> pomocí Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt mapy a nastaví mechanismus ověřování pro využití přístupového tokenu. Pokyny najdete v tématu [vytvoření mapy](./map-create.md) .

Druhý blok kódu vytvoří `TokenCredential` pro ověření požadavků HTTP pro Azure Maps pomocí přístupového tokenu. Pak předá `TokenCredential` `atlas.service.MapsURL.newPipeline()` a vytvoří instanci [kanálu](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) . Představuje adresu URL pro Azure Maps vyhledávacích operací. [](https://docs.microsoft.com/rest/api/maps/search) `searchURL`

Třetí blok kódu aktualizuje styl ukazatele myši na ukazatel a vytvoří [Překryvný](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) objekt. Pokyny najdete [v tématu Přidání místní nabídky na mapě](./map-add-popup.md) .

Čtvrtý blok kódu přidá [naslouchací proces události](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)kliknutí myší. Když se aktivuje, vytvoří vyhledávací dotaz s souřadnicemi bodu kliknutí. Pak použije metodu [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-) modulu služby k dotazování na [rozhraní API pro zpětné vyhledávání adres](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) pro adresu souřadnic. Kolekce funkcí pro injson z odpovědi se pak extrahuje pomocí `geojson.getFeatures()` metody.

Pátý blok kódu nastaví obsah místní nabídky HTML tak, aby zobrazoval adresu odpovědi pro kliknutím na souřadnicovou polohu.

Změna kurzoru, překryvný objekt a událost Click jsou vytvořeny v [naslouchací službě událostí načtení](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) mapy, aby bylo zajištěno, že se mapa kompletně načte před tím, než mohou být načteny informace o souřadnicích.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Vytvoření požadavku zpětného vyhledávání přes rozhraní API pro načtení

Klikněte na mapu a pomocí načíst převratte žádost o změnu geografického kódu pro toto umístění.

<iframe height='500' scrolling='no' title='Získání informací ze souřadnice' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero a <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Získejte informace od souřadnice</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt mapy a nastaví mechanismus ověřování pro využití přístupového tokenu. Pokyny najdete v tématu [vytvoření mapy](./map-create.md) .

Druhý blok kódu aktualizuje styl ukazatele myši na ukazatel a [Překryvný](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) objekt. Pokyny najdete [v tématu Přidání místní nabídky na mapě](./map-add-popup.md) .

Třetí blok kódu přidá naslouchací proces události pro kliknutí myší. Po kliknutí myší používá [rozhraní API pro načtení](https://fetch.spec.whatwg.org/) k dotazování [Azure Maps rozhraní API pro hledání zpětné adresy](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) pro kliknutí na adresu. Pro úspěšnou odpověď shromažďuje adresu pro kliknutí na umístění a definuje obsah automaticky otevíraného okna a umístění prostřednictvím funkce [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) místní třídy.

Změna kurzoru, překryvný objekt a událost Click jsou vytvořeny v [naslouchací službě událostí načtení](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) mapy, aby bylo zajištěno, že se mapa kompletně načte před tím, než mohou být načteny informace o souřadnicích.

## <a name="next-steps"></a>Další postup

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Oken](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Úplné příklady kódu najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Zobrazit směr od A do B](./map-route.md)

> [!div class="nextstepaction"]
> [Zobrazit provoz](./map-show-traffic.md)

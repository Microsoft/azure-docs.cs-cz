---
title: Zobrazit informace o souřadnici na mapě | Mapy Microsoft Azure
description: Přečtěte si, jak zobrazit informace o adrese na mapě, když uživatel vybere souřadnici.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 174bdc496e52a6ac8f2a2d631db92e0f21a819be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371425"
---
# <a name="get-information-from-a-coordinate"></a>Získání informací ze souřadnice

Tento článek ukazuje, jak provést vyhledávání reverzní adresy, která zobrazuje adresu umístění clickup.

Existují dva způsoby, jak provést zpětné vyhledávání adres. Jedním ze způsobů je dotaz [na rozhraní API pro vyhledávání reverzních adres Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) prostřednictvím modulu služby. Druhou možnosti je použití [rozhraní API pro načtení](https://fetch.spec.whatwg.org/) k vytvoření požadavku do [rozhraní API pro vyhledávání adres azure maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) a hledání adresy. Oba způsoby jsou zkoumány níže.

## <a name="make-a-reverse-search-request-via-service-module"></a>Vytvoření požadavku zpětného vyhledávání pomocí servisního modulu

<iframe height='500' scrolling='no' title='Získat informace z souřadnice (Servisní modul)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na informace o získání pera z<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>souřadnice (Service Module)</a> podle Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok vytvoří objekt mapy a nastaví mechanismus ověřování pro použití přístupového tokenu. Můžete vidět [vytvořit mapu](./map-create.md) pro pokyny.

Druhý blok kódu `TokenCredential` vytvoří k ověření http požadavků na Azure Maps s přístupovým tokenem. Potom předá `TokenCredential` `atlas.service.MapsURL.newPipeline()` a vytvoří [pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) instance. Představuje `searchURL` adresu URL pro operace Azure Maps [Search.](https://docs.microsoft.com/rest/api/maps/search)

Třetí blok kódu aktualizuje styl kurzoru myši na ukazatel a vytvoří místní [objekt.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) Pokyny najdete v místní [chod na mapě.](./map-add-popup.md)

Čtvrtý blok kódu přidá [posluchače události](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)kliknutím myši . Při aktivaci vytvoří vyhledávací dotaz se souřadnicemi bodu, na který jste klepnuli. Potom používá metodu [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-)k dotazování [na reverzní rozhraní API pro získání vyhledávací adresy](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) pro adresu souřadnic. Kolekce funkcí GeoJSON je pak `geojson.getFeatures()` extrahován pomocí metody z odpovědi.

Pátý blok kódu nastaví obsah místního obsahu HTML tak, aby zobrazoval adresu odpovědi pro pozici souřadnic, na kterou jste klepnuli.

Změna kurzoru, místní objekt a událost kliknutí jsou vytvořeny v [naslouchací proces události načtení](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)mapy . Tato struktura kódu zajišťuje, že mapování se plně načte před načtením informací o souřadnicích.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Vytvoření požadavku zpětného vyhledávání pomocí rozhraní Fetch API

Kliknutím na mapu můžete pomocí načtení provést požadavek na reverzní geokód pro toto umístění.

<iframe height='500' scrolling='no' title='Získání informací ze souřadnice' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na informace o získání<a href='https://codepen.io/azuremaps'>@azuremaps</a>pera <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>z souřadnice</a> podle Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt mapy a nastaví mechanismus ověřování pro použití přístupového tokenu. Můžete vidět [vytvořit mapu](./map-create.md) pro pokyny.

Druhý blok kódu aktualizuje styl kurzoru myši na ukazatel. Konkretivi objektu [vyskakovacího souboru.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) Pokyny najdete v místní [chod na mapě.](./map-add-popup.md)

Třetí blok kódu přidá posluchače události pro kliknutí myší. Po kliknutí myší použije [rozhraní API pro načtení](https://fetch.spec.whatwg.org/) k dotazování [rozhraní API pro vyhledávání reverzních adres Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) na adresu kliknutí na souřadnice. Pro úspěšnou odpověď shromažďuje adresu pro místo, na které jste klikli. Definuje obsah vyskakovacího panelu a pozici pomocí funkce [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) vyskakovací třídy.

Změna kurzoru, místní objekt a událost kliknutí jsou vytvořeny v [naslouchací proces události načtení](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)mapy . Tato struktura kódu zajišťuje, že se mapa plně načte před načtením informací o souřadnicích.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Doporučené postupy pro používání vyhledávací služby](how-to-use-best-practices-for-search.md)

Další informace o třídách a metodách použitých v tomto článku:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Úplné příklady kódu naleznete v následujících článcích:

> [!div class="nextstepaction"]
> [Zobrazení pokynů pro trasu z A do B](./map-route.md)

> [!div class="nextstepaction"]
> [Zobrazení provozu](./map-show-traffic.md)

---
title: Zobrazit informace o souřadnicích na mapě | Mapy Microsoft Azure
description: Naučte se, jak zobrazit informace o adrese na mapě, když uživatel vybere souřadnici.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 31e4004379340912051204786da592fe33a5bd63
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890747"
---
# <a name="get-information-from-a-coordinate"></a>Získání informací ze souřadnice

Tento článek ukazuje, jak vytvořit reverzní hledání na základě adresy, které zobrazuje adresu kliknutí na překryvné okno.

Existují dva způsoby, jak provést zpětné vyhledávání v rámci adresy. Jedním ze způsobů je dotazování [rozhraní API pro hledání zpětné adresy Azure Maps](/rest/api/maps/search/getsearchaddressreverse) prostřednictvím modulu služby. Druhým způsobem je použít [načtené rozhraní API](https://fetch.spec.whatwg.org/) k vytvoření požadavku na [Azure Maps rozhraní API pro hledání zpětného vyhledávání](/rest/api/maps/search/getsearchaddressreverse) adres k nalezení adresy. Níže jsou uvedené následující otázky:

## <a name="make-a-reverse-search-request-via-service-module"></a>Vytvoření požadavku zpětného vyhledávání přes modul služby

<iframe height='500' scrolling='no' title='Získání informací ze souřadnice (modul služby)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se, <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>jak pero získá informace ze souřadnice (modulu služby)</a> pomocí Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu vytvoří první blok objekt mapy a nastaví mechanismus ověřování pro použití přístupového tokenu. Pokyny najdete v tématu [vytvoření mapy](./map-create.md) .

Druhý blok kódu vytvoří `TokenCredential` pro ověření požadavků protokolu HTTP, které se mají Azure Maps pomocí přístupového tokenu. Pak předá `TokenCredential` `atlas.service.MapsURL.newPipeline()` a vytvoří instanci [kanálu](/javascript/api/azure-maps-rest/atlas.service.pipeline) . `searchURL`Představuje adresu URL pro Azure Maps [vyhledávacích](/rest/api/maps/search) operací.

Třetí blok kódu aktualizuje styl ukazatele myši na ukazatel a vytvoří [místní](/javascript/api/azure-maps-control/atlas.popup#open) objekt. Pokyny najdete [v tématu Přidání místní nabídky na mapě](./map-add-popup.md) .

Čtvrtý blok kódu přidá [naslouchací proces události](/javascript/api/azure-maps-control/atlas.map#events)kliknutí myší. Když se aktivuje, vytvoří vyhledávací dotaz s souřadnicemi bodu kliknutí. Pak použije metodu [getSearchAddressReverse](/javascript/api/azure-maps-rest/atlas.service.searchurl#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-)pro dotazování rozhraní [Get Search Reverted](/rest/api/maps/search/getsearchaddressreverse) pro adresu souřadnic. Kolekce funkcí pro injson se pak extrahuje pomocí `geojson.getFeatures()` metody z odpovědi.

Pátý blok kódu nastaví obsah místní nabídky HTML tak, aby zobrazoval adresu odpovědi pro kliknutím na souřadnicovou polohu.

Změna kurzoru, překryvný objekt a událost Click jsou vytvořeny v [naslouchací službě událostí načtení](/javascript/api/azure-maps-control/atlas.map#events)mapy. Tato struktura kódu zajišťuje před načtením informací o souřadnicích úplnou zátěž mapy.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Vytvoření požadavku zpětného vyhledávání přes rozhraní API pro načtení

Klikněte na mapu a pomocí načíst převratte žádost o změnu geografického kódu pro toto umístění.

<iframe height='500' scrolling='no' title='Získání informací ze souřadnice' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero a <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Získejte informace od souřadnice</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt mapy a nastaví mechanismus ověřování pro použití přístupového tokenu. Pokyny najdete v tématu [vytvoření mapy](./map-create.md) .

Druhý blok kódu aktualizuje styl ukazatele myši na ukazatel. Vytvoří instanci [překryvného](/javascript/api/azure-maps-control/atlas.popup#open) objektu. Pokyny najdete [v tématu Přidání místní nabídky na mapě](./map-add-popup.md) .

Třetí blok kódu přidá naslouchací proces události pro kliknutí myší. Kliknete-li na tlačítko myši, používá [rozhraní API načtení](https://fetch.spec.whatwg.org/) k dotazování [Azure Maps rozhraní API pro vyhledávání zpětné adresy](/rest/api/maps/search/getsearchaddressreverse) pro kliknutí na souřadnicovou adresu. V případě úspěšné odpovědi aplikace shromáždí adresu pro kliknutí na umístění. Slouží k definování obsahu a umístění automaticky otevíraného okna pomocí funkce [setOptions](/javascript/api/azure-maps-control/atlas.popup#setoptions-popupoptions-) místní třídy.

Změna kurzoru, překryvný objekt a událost Click jsou vytvořeny v [naslouchací službě událostí načtení](/javascript/api/azure-maps-control/atlas.map#events)mapy. Tato struktura kódu zajišťuje, že mapa bude plně načtena před načtením informací o souřadnicích.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Osvědčené postupy pro používání vyhledávací služby](how-to-use-best-practices-for-search.md)

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Mapa](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Překryvný](/javascript/api/azure-maps-control/atlas.popup)

Úplné příklady kódu najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Zobrazení pokynů pro trasu z A do B](./map-route.md)

> [!div class="nextstepaction"]
> [Zobrazení provozu](./map-show-traffic.md)
---
title: Zobrazit směr směrování na mapě | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak zobrazit směry mezi dvěma umístěními na mapě pomocí webové sady SDK Microsoft Azure Maps.
author: Philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 1cde1aaa7c9dba3e28407439a46b0e0a3326e4fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83123968"
---
# <a name="show-directions-from-a-to-b"></a>Zobrazení pokynů pro trasu z A do B

V tomto článku se dozvíte, jak vytvořit žádost o trasu a jak zobrazit trasu na mapě.

Existují dva způsoby, jak to provést. Prvním způsobem je dotazování [rozhraní API pro Azure Maps tras](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) prostřednictvím modulu služby. Druhým způsobem je použít [rozhraní Fetch API](https://fetch.spec.whatwg.org/) k vytvoření žádosti o vyhledávání na [rozhraní API pro Azure Maps tras](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Oba způsoby jsou popsány níže.

## <a name="query-the-route-via-service-module"></a>Dotazování trasy prostřednictvím modulu služby

<iframe height='500' scrolling='no' title='Zobrazit pokyny od A do B na mapě (modul služby)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Informace o tom, jak na CodePen () na, najdete na stránce s <a href='https://codepen.io/azuremaps/pen/RBZbep/'>pokyny od a do B na mapě (Service Module Azure Maps)</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'>CodePen</a>
</iframe>

Ve výše uvedeném kódu první blok vytvoří objekt mapy a nastaví mechanismus ověřování na použití přístupového tokenu. Pokyny najdete v tématu [vytvoření mapy](./map-create.md) .

Druhý blok kódu vytvoří `TokenCredential` pro ověření požadavků HTTP pro Azure Maps pomocí přístupového tokenu. Pak předá `TokenCredential` `atlas.service.MapsURL.newPipeline()` a vytvoří instanci [kanálu](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) . `routeURL`Představuje adresu URL pro Azure Maps operací [Směrování](https://docs.microsoft.com/rest/api/maps/route) .

Třetí blok kódu vytvoří a přidá objekt [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) do mapy.

Čtvrtý blok kódu vytvoří objekty počátečního a koncového [bodu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) a přidá je do objektu DataSource.

Čára je [funkcí](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) pro LineString. [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) vykreslí řádkové objekty zabalené ve [zdroji dat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako řádky na mapě. Čtvrtý blok kódu vytvoří a přidá na mapu řádkovou vrstvu. Viz Vlastnosti vrstvy čáry na [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

[Symbolová vrstva](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) používá texty nebo ikony pro vykreslení dat na základě bodu zabalených ve [zdroji dat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Texty nebo ikony se vykreslují jako symboly na mapě. Pátý blok kódu vytvoří a přidá na mapu vrstvu symbolů.

Šestý blok kódu se dotazuje směrovací služby Azure Maps, která je součástí [modulu služby](how-to-use-services-module.md). Metoda [CalculateRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest#methods) RouteUrl se používá k získání trasy mezi počátečním a koncovým bodem. Kolekce funkcí pro injson z odpovědi je pak extrahována pomocí `geojson.getFeatures()` metody a přidána do zdroje dat. Následně vykreslí odpověď jako trasu na mapě. Další informace o přidání čáry na mapu najdete v tématu [Přidání čáry na mapě](map-add-line-layer.md).

Poslední blok kódu nastaví meze mapy pomocí vlastnosti [SetCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) mapy.

Dotaz Route, zdroj dat, symbol, vrstvy čáry a hranice kamery jsou vytvořeny v rámci [naslouchacího procesu události](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Tato struktura kódu zajišťuje, že se výsledky zobrazí až po úplném načtení mapy.

## <a name="query-the-route-via-fetch-api"></a>Dotazování trasy prostřednictvím rozhraní API pro načtení

<iframe height='500' scrolling='no' title='Zobrazit směr od A do B na mapě' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na téma <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>zobrazení pokynů z a na B na mapě</a> pomocí Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt mapy a nastaví mechanismus ověřování pro použití přístupového tokenu. Pokyny najdete v tématu [vytvoření mapy](./map-create.md) .

Druhý blok kódu vytvoří a přidá objekt [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) do mapy.

Třetí blok kódu vytvoří počáteční a cílový bod pro trasu. Pak je přidá do zdroje dat. Pokyny k použití [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)najdete [v tématu Přidání kódu PIN na mapě](map-add-pin.md) .

[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) vykreslí řádkové objekty zabalené ve [zdroji dat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako řádky na mapě. Čtvrtý blok kódu vytvoří a přidá na mapu řádkovou vrstvu. Viz Vlastnosti vrstvy čáry na [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

[Symbolová vrstva](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) používá text nebo ikony pro vykreslení dat na základě bodu zabalených ve [zdroji dat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako symboly na mapě. Pátý blok kódu vytvoří a přidá na mapu vrstvu symbolů. Viz Vlastnosti vrstvy symbolů na [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest).

Další blok kódu vytvoří `SouthWest` `NorthEast` body od počátečního a cílového bodu a nastaví meze mapy pomocí vlastnosti [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) mapy.

Poslední blok kódu používá [rozhraní Fetch API](https://fetch.spec.whatwg.org/) k vytvoření žádosti o vyhledávání na [rozhraní API pro Azure Maps tras](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Odpověď se pak analyzuje. Pokud byla odpověď úspěšná, informace o zeměpisné šířce a délce se použijí k vytvoření řádku a propojení těchto bodů. Data řádku se pak přidají do zdroje dat, aby se vygenerovala trasa na mapě. Pokyny najdete [na stránce Přidání čáry na mapě](map-add-line-layer.md) .

Dotaz Route, zdroj dat, symbol, vrstvy čáry a hranice kamery jsou vytvořeny v rámci [naslouchacího procesu události](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Znovu chceme zajistit, aby se výsledky zobrazovaly po úplném načtení mapy.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Osvědčené postupy pro používání směrovací služby](how-to-use-best-practices-for-search.md)

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Úplné příklady kódu najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Zobrazit provoz na mapě](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interakce s událostmi myši na mapě](./map-events.md)

---
title: Zobrazit trasy na mapě | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak zobrazit trasy mezi dvěma umístěními na mapě pomocí sady Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: dde9264d0cb65726b624b918982cfa01985b63ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371413"
---
# <a name="show-directions-from-a-to-b"></a>Zobrazení pokynů pro trasu z A do B

V tomto článku se zobrazí, jak podat žádost o trasu a zobrazit trasu na mapě.

Existují dva způsoby, jak to udělat. Prvním způsobem je dotaz [na rozhraní API trasy Azure Maps route](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) prostřednictvím modulu služby. Druhým způsobem je použití [rozhraní API pro načtení](https://fetch.spec.whatwg.org/) k vytvoření požadavku na vyhledávání v rozhraní API trasy Azure Maps [Route](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Oba způsoby jsou popsány níže.

## <a name="query-the-route-via-service-module"></a>Dotaz na trasu pomocí servisního modulu

<iframe height='500' scrolling='no' title='Zobrazit trasy od A do B na mapě (Servisní modul)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/RBZbep/'>trasu pera Zobrazit od A do B na mapě (Service Module)</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok vytvoří objekt mapy a nastaví mechanismus ověřování pro použití přístupového tokenu. Můžete vidět [vytvořit mapu](./map-create.md) pro pokyny.

Druhý blok kódu vytvoří `TokenCredential` k ověření http požadavků na Azure Maps s přístupovým tokenem. Potom předá `TokenCredential` `atlas.service.MapsURL.newPipeline()` a vytvoří [pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) instance. Představuje `routeURL` adresu URL operací trasy Azure Maps [Route.](https://docs.microsoft.com/rest/api/maps/route)

Třetí blok kódu vytvoří a přidá objekt [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) do mapy.

Čtvrtý blok kódu vytvoří objekty počátečních a koncových [bodů](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) a přidá je do objektu dataSource.

Řádek je [funkce](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) pro LineString. A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) vykreslí čárové objekty zabalené v [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako čáry na mapě. Čtvrtý blok kódu vytvoří a přidá vrstvu čar do mapy. Viz vlastnosti vrstvy čáry na [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

[Vrstva symbolů](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) používá texty nebo ikony k vykreslení dat založených na bodu zabalených do [zdroje dat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Texty nebo ikony se vykreslují jako symboly na mapě. Pátý blok kódu vytvoří a přidá vrstvu symbolů do mapy.

Šestý blok kódu dotazuje služby směrování Azure Maps, která je součástí [modulu služby](how-to-use-services-module.md). [Metoda calculateRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest#methods) routeurl se používá k získání trasy mezi počátečním a koncovým bodem. Kolekce funkcí GeoJSON z odpovědi je pak `geojson.getFeatures()` extrahována pomocí metody a je přidána do zdroje dat. Potom vykreslí odpověď jako trasu na mapě. Další informace o přidání čáry do mapy naleznete [v tématu přidání čáry na mapě](map-add-line-layer.md).

Poslední blok kódu nastaví hranice mapy pomocí vlastnosti [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) mapy.

Dotaz trasy, zdroj dat, symbol, vrstvy čar a hranice kamery jsou vytvořeny uvnitř [posluchače události](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Tato struktura kódu zajišťuje, že výsledky jsou zobrazeny až po úplném načtení mapy.

## <a name="query-the-route-via-fetch-api"></a>Dotaz na trasu pomocí rozhraní API pro načtení

<iframe height='500' scrolling='no' title='Zobrazení trasy od A do B na mapě' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>trasu pera Zobrazit z A do B na mapě</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt mapy a nastaví mechanismus ověřování pro použití přístupového tokenu. Můžete vidět [vytvořit mapu](./map-create.md) pro pokyny.

Druhý blok kódu vytvoří a přidá objekt [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) do mapy.

Třetí blok kódu vytvoří počáteční a cílový bod trasy. Potom je přidá do zdroje dat. Na mapě se zobrazí [přidání špendlíku,](map-add-pin.md) kde najdete pokyny k používání [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest).

A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) vykreslí čárové objekty zabalené v [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako čáry na mapě. Čtvrtý blok kódu vytvoří a přidá vrstvu čar do mapy. Viz vlastnosti čárové vrstvy na [linelayeroptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

[Vrstva symbolů](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) používá text nebo ikony k vykreslení dat založených na bodu zabalených do [Zdroje dat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako symbolů na mapě. Pátý blok kódu vytvoří a přidá vrstvu symbolů do mapy. Viz vlastnosti vrstvy symbolu na [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest).

Další blok kódu `SouthWest` `NorthEast` vytvoří a body z počátečnía cílové body a nastaví hranice mapy pomocí mapové [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) vlastnost.

Poslední blok kódu používá [rozhraní API pro načtení](https://fetch.spec.whatwg.org/) k vytvoření požadavku na vyhledávání do rozhraní AZURE Maps Route [API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Odpověď je pak analyzována. Pokud byla odpověď úspěšná, informace o zeměpisné šířce a zeměpisné šířce se používají k vytvoření pole čáry propojením těchto bodů. Data řádku jsou pak přidána do zdroje dat, aby se trasa na mapě vykreslovala. Pokyny [namapě naleznete v přidání řádku.](map-add-line-layer.md)

Dotaz trasy, zdroj dat, symbol, vrstvy čar a hranice kamery jsou vytvořeny uvnitř [posluchače události](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Opět chceme zajistit, aby se výsledky zobrazovaly po úplném načtení mapy.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Doporučené postupy pro používání služby směrování](how-to-use-best-practices-for-search.md)

Další informace o třídách a metodách použitých v tomto článku:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Úplné příklady kódu naleznete v následujících článcích:

> [!div class="nextstepaction"]
> [Zobrazit provoz na mapě](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interakce s mapou - události myši](./map-events.md)

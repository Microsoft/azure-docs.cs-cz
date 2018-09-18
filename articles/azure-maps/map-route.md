---
title: Zobrazení pokynů pro trasu s Azure Maps | Dokumentace Microsoftu
description: Jak zobrazit směrech mezi dvěma umístěními na mapě jazyka Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 09/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: ed522779f5a86e38ee12a246cea9ac85d0379f9e
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45729034"
---
# <a name="show-directions-from-a-to-b"></a>Zobrazení pokynů pro trasu z A do B

Tento článek ukazuje, jak vytvořit žádost o trasa a trasy na mapě zobrazit.

Existují dva způsoby, jak to provést. První způsob je dotaz [trasy rozhraní API služby Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) prostřednictvím modulu service. Druhý způsob je, aby [XMLHttpRequest](https://xhr.spec.whatwg.org/) rozhraní API. Obě možnosti jsou popsány níže.

## <a name="query-the-route-via-service-module"></a>Dotaz směrování přes modul služby

<iframe height='500' scrolling='no' title='Zobrazení pokynů pro trasu z A na B na mapě (modulu Service)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/RBZbep/'>zobrazení pokynů pro trasu z A na B na mapě (modulu Service)</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

První blok kódu vytvoří objekt Map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

Řádek v druhé bloku kódu vytváří instanci služby klienta.

Inicializuje třetí blok kódu [řádek řetězec vrstvy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings) na mapě.

Čtvrtý bloku kódu vytvoří a přidá špendlíky na mapě představující počáteční a koncový bod trasy. Můžete zobrazit [přidání špendlíku na mapě](map-add-pin.md) pokyny ohledně použití [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins).

Další blok kódu používá [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) funkce třídy map nastavit ohraničující rámeček mapy podle počáteční a koncový bod trasy.

Šestý blok kódu vytvoří trasy dotazu.

Směrovací služba Azure Maps prostřednictvím dotazuje poslední blok kódu [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) metodu k získání trasy mezi počáteční a cíl bodu. Odpověď se pak Parsuje do pomocí formátu GeoJSON [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes) metody. Přidá všechny tyto řádky do mapy vykreslovat trasy. Další informace získáte v části o [přidání čáry na mapu](./map-add-shape.md#addALine).

## <a name="query-the-route-via-xmlhttprequest"></a>Dotaz směrování přes XMLHttpRequest

<iframe height='500' scrolling='no' title='Zobrazení pokynů pro trasu z A do B na mapě' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>zobrazení pokynů pro trasu z A do B na mapě</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

První blok kódu vytvoří objekt Map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

Druhý bloku kódu vytvoří a přidá špendlíky na mapě představující počáteční a koncový bod trasy. Můžete zobrazit [přidání špendlíku na mapě](map-add-pin.md) pokyny ohledně použití [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins).

Třetí blok kódu používá [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) funkce třídy Map nastavit ohraničující rámeček mapy podle počáteční a koncový bod trasy.

Odešle čtvrtý bloku kódu [XMLHttpRequest](https://xhr.spec.whatwg.org/) k [trasy rozhraní API služby Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).

Poslední blok kódu analyzuje příchozí odpovědi. Pro úspěšné odpovědi shromáždí informace o zeměpisné šířce a délce pro každý bod na trase. Každý bod na trase propojíte svůj další bod na trase vytvoří pole řádků. Přidá všechny tyto řádky do mapy vykreslovat trasy. Můžete zobrazit [přidejte řádek na mapě](./map-add-shape.md#addALine) pokyny.

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Naleznete v následujících článcích pro úplné příklady:

> [!div class="nextstepaction"]
> [Na mapě Zobrazit provoz](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interakce s mapou - události myši](./map-events.md)

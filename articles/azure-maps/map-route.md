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
ms.openlocfilehash: 37323bacf47613c0faf7769701808ecef2645115
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44157949"
---
# <a name="show-directions-from-a-to-b"></a>Zobrazení pokynů pro trasu z A do B 

Tento článek ukazuje, jak vytvořit žádost o trasa a trasy na mapě zobrazit.

Existují dva způsoby, jak to provést, je jedním ze způsobů, tak pomocí dotazu [trasy rozhraní API služby Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) prostřednictvím služby modulu a druhý je tím, že [XMLHttpRequest](https://xhr.spec.whatwg.org/) rozhraní API. Oba jsou popsány níže.

## <a name="use-service-module-to-query-for-a-route"></a>Použít modul služby k dotazování pro trasu

## <a name="understand-the-code"></a>Vysvětlení kódu

<iframe height='500' scrolling='no' title='Zobrazení pokynů pro trasu z A na B na mapě (modulu Service)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/RBZbep/'>zobrazení pokynů pro trasu z A na B na mapě (modulu Service)</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

Řádek v druhé bloku kódu vytváří instanci služby klienta.

Inicializuje třetí blok kódu [řádek řetězec vrstvy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings) na mapě.

Čtvrtý bloku kódu vytvoří a přidá špendlíky na mapě představující počáteční a koncový bod trasy. Můžete zobrazit [přidání špendlíku na mapě](map-add-pin.md) pokyny.

Další blok kódu používá [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) funkce třídy map nastavit ohraničující rámeček mapy podle počáteční a koncový bod trasy.

Šestý blok kódu vytvoří trasy dotazu.

Směrovací služba Azure Maps prostřednictvím dotazuje poslední blok kódu [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) metodu k získání trasy mezi počáteční a cíl bodu. Odpověď se pak Parsuje do pomocí formátu GeoJSON [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes) metody. Přidá všechny tyto řádky do mapy vykreslovat trasy. Můžete zobrazit [přidejte řádek na mapě](./map-add-shape.md#addALine) Další informace.

## <a name="use-xmlhttprequest-to-query-for-a-route"></a>Použití XMLHTTPRequest k dotazování služby pro trasu

## <a name="understand-the-code"></a>Vysvětlení kódu

<iframe height='500' scrolling='no' title='Zobrazení pokynů pro trasu z A do B na mapě' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>zobrazení pokynů pro trasu z A do B na mapě</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

Druhý bloku kódu vytvoří a přidá špendlíky na mapě představující počáteční a koncový bod trasy. Můžete zobrazit [přidání špendlíku na mapě](map-add-pin.md) pokyny.

Třetí blok kódu používá [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) funkce třídy map nastavit ohraničující rámeček mapy podle počáteční a koncový bod trasy.

Odešle čtvrtý bloku kódu [XMLHttpRequest](https://xhr.spec.whatwg.org/) k [trasy rozhraní API služby Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).

Poslední blok kódu analyzuje příchozí odpovědi. Pro úspěšné odpovědi shromáždí informace o zeměpisné šířce a délce pro každý bod na trase. Každý bod na trase propojíte svůj další bod na trase vytvoří pole řádků. Přidá všechny tyto řádky do mapy vykreslovat trasy. Můžete zobrazit [přidejte řádek na mapě](./map-add-shape.md#addALine) pokyny.

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku: 

* [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections)
* [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes)
* [Čárová vrstva řetězec](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings)
* [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds)
    * [addLinestrings](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)

Další příklady kódu pro přidání do vaše mapy najdete v následujících článcích: 
* [Na mapě Zobrazit provoz](./map-show-traffic.md)
* [Interakce s mapou - události myši](./map-events.md)

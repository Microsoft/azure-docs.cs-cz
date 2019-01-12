---
title: Zobrazení pokynů pro trasu s Azure Maps | Dokumentace Microsoftu
description: Jak zobrazit směrech mezi dvěma umístěními na mapě jazyka Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 11/15/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 8c337ba1770b9b618567e7a3ee5fdbadba63181b
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54231848"
---
# <a name="show-directions-from-a-to-b"></a>Zobrazení pokynů pro trasu z A do B

Tento článek ukazuje, jak vytvořit žádost o trasa a trasy na mapě zobrazit.

Existují dva způsoby, jak to provést. První způsob je dotaz [trasy rozhraní API služby Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) prostřednictvím modulu service. Druhý způsob je, aby [XMLHttpRequest](https://xhr.spec.whatwg.org/) rozhraní API. Obě možnosti jsou popsány níže.

## <a name="query-the-route-via-service-module"></a>Dotaz směrování přes modul služby

<iframe height='500' scrolling='no' title='Zobrazení pokynů pro trasu z A na B na mapě (modulu Service)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/RBZbep/'>zobrazení pokynů pro trasu z A na B na mapě (modulu Service)</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt Map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

Řádek v druhé bloku kódu vytváří instanci služby klienta.

Třetí vytvoří a přidá [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) objektu do mapy.

 Řádek je [funkce](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) z LineString. A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) vykresluje řádek objektů, který je obalen [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako řádky na mapě. Čtvrtý bloku kódu vytvoří a přidat čárovou vrstvu mapy. Zobrazit vlastnosti čárovou vrstvu na [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.deprecated.linestringlayeroptions?view=azure-iot-typescript-latest).

A [symbol vrstvy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) používá k vykreslení dat na základě bodu zabalené v textu nebo ikony [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako symboly na mapě. Pátý bloku kódu vytvoří a přidat vrstvu symbolů do mapy.

Šestý bloku kódu vytvoří počáteční a koncové [body](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) objektu a přidá je do objektu dataSource.

Sedmý bloku kódu nastaví mezí mapy pomocí mapy [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) vlastnost.

Poslední blok kódu dotazuje služba Směrování Azure Maps, která je součástí sady [modulu service](https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1). [GetRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) metoda se používá k získání trasy mezi počáteční a koncový bod. Odpověď se pak Parsuje do pomocí formátu GeoJSON [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes) metody. Pak vykreslí odpovědi jako trasy na mapě. Další informace o přidání řádku do mapy, naleznete v tématu [přidejte řádek na mapě](./map-add-shape.md#addALine).

Směrování dotazů, zdroj dat, symbolů a řádek vrstvy a hranice fotoaparátu vytvářejí se a nastavená na mapě [naslouchací proces událostí](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) zajistit, že výsledky se zobrazí po načtení mapy plně.

## <a name="query-the-route-via-xmlhttprequest"></a>Dotaz směrování přes XMLHttpRequest

<iframe height='500' scrolling='no' title='Zobrazení pokynů pro trasu z A do B na mapě' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>zobrazení pokynů pro trasu z A do B na mapě</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

První blok kódu vytvoří objekt Map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

Druhý bloku kódu vytvoří a přidá [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) objektu do mapy.

Třetí blok kódu vytváří body start a cíl pro tuto trasu a přidá je do zdroje dat. Můžete zobrazit [přidání špendlíku na mapě](map-add-pin.md) pokyny ohledně použití [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins).

 A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) vykresluje řádek objektů, který je obalen [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako řádky na mapě. Čtvrtý bloku kódu vytvoří a přidá čárovou vrstvu mapy. Zobrazit vlastnosti čárovou vrstvu na [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

A [symbol vrstvy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) používá k vykreslení dat na základě bodu zabalené v textu nebo ikony [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako symboly na mapě. Pátý bloku kódu vytvoří a přidat vrstvu symbolů do mapy. Zobrazit vlastnosti vrstvy symbolu v [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest).

Vytvoří další blok kódu `SouthWest` a `NorthEast` směřuje od spuštění a cílové body a nastaví mezí mapy pomocí mapy [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) vlastnost.

Odešle posledního bloku kódu [XMLHttpRequest](https://xhr.spec.whatwg.org/) k [trasy rozhraní API služby Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Pak analyzuje příchozí odpovědi. A úspěšné odpovědi, shromažďuje informace o zeměpisné šířce a délce pro každý bod trasy a vytvoří pole řádků propojením těchto bodů. Potom přidá tyto řádky do zdroje dat k vykreslení trasy na mapě. Můžete zobrazit [přidejte řádek na mapě](./map-add-shape.md#addALine) pokyny.

Směrování dotazů, zdroj dat, symbolů a řádek vrstvy a hranice fotoaparátu vytvářejí se a nastavená na mapě [naslouchací proces událostí](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) zajistit, že výsledky se zobrazí po načtení mapy plně.

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Naleznete v následujících článcích pro úplné příklady:

> [!div class="nextstepaction"]
> [Na mapě Zobrazit provoz](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interakce s mapou - události myši](./map-events.md)

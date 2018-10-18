---
title: Vyhledání trasy s využitím Azure Maps | Microsoft Docs
description: Trasa k bodu zájmu s využitím Azure Maps
author: dsk-2015
ms.author: dkshir
ms.date: 10/02/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 3bf1aa6d1b9bd65c28ef99ddbac71fb75daf99e7
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816714"
---
# <a name="route-to-a-point-of-interest-using-azure-maps"></a>Trasa k bodu zájmu s využitím Azure Maps

V tomto kurzu se dozvíte, jak pomocí účtu Azure Maps a sady SDK Route Service vyhledat trasu k bodu zájmu. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření nové webové stránky s použitím rozhraní API pro mapové ovládací prvky
> * Nastavit souřadnice pro adresu
> * Zadat dotaz na rozhraní API Route Service ohledně trasy k bodu zájmu

## <a name="prerequisites"></a>Požadavky

Než budete pokračovat, podle postupu v předchozím kurzu [vytvořte účet Azure Maps](./tutorial-search-location.md#createaccount) a [získejte pro účet klíč předplatného](./tutorial-search-location.md#getkey).

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>Vytvoření nové mapy

Následující kroky ukazují, jak vytvořit statickou stránku HTML s vloženým rozhraním API pro mapové ovládací prvky.

1. Na místním počítači vytvořte nový soubor s názvem **MapRoute.html**.
2. Přidejte do souboru následující komponenty HTML:

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Route</title>
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css"/>
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.min.js?api-version=1"></script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #map {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>

    <body>
        <div id="map"></div>
        <script>
            // Embed Map Control JavaScript code here
        </script>
    </body>

    </html>
    ```
    V hlavičce HTML jsou vložená umístění prostředků pro soubory CSS a JavaScript pro knihovnu Azure Maps. Segment *script* v těle souboru HTML bude obsahovat vložený kód JavaScriptu pro přístup k rozhraním Azure Maps API.

3. Do bloku *script* v souboru HTML přidejte následující kód JavaScriptu. Nahraďte řetězec **\<your account key\>** primárním klíčem, který jste zkopírovali ze svého účtu Maps.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var MapsAccountKey = "<your account key>";
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey
    });
    ```
    Objekt **atlas.Map** umožňuje ovládání vizuální a interaktivní webové mapy a je součástí rozhraní API pro mapové ovládací prvky prostředí Azure.

4. Uložte soubor a otevřete ho v prohlížeči. V tuto chvíli máte základní mapu, kterou můžete dále rozvíjet.

   ![Zobrazení základní mapy](./media/tutorial-route-location/basic-map.png)

## <a name="set-start-and-end-points"></a>Nastavení počátečního a koncového bodu

Pro účely tohoto kurzu nastavte jako počáteční bod Microsoft a jako cílový bod čerpací stanici v Seattlu.

1. Do stejného bloku *script* v souboru **MapRoute.html** přidejte následující kód JavaScriptu, který vytvoří počáteční a koncový bod trasy:

    ```JavaScript
    // Create the GeoJSON objects which represent the start and end point of the route
    var startPoint = new atlas.data.Point([-122.130137, 47.644702]);
    var startPin = new atlas.data.Feature(startPoint, {
        title: "Microsoft",
        icon: "pin-round-blue"
    });

    var destinationPoint = new atlas.data.Point([-122.3352, 47.61397]);
    var destinationPin = new atlas.data.Feature(destinationPoint, {
        title: "Contoso Oil & Gas",
        icon: "pin-blue"
    });
    ```
    Tento kód vytvoří dva [objekty GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) představující počáteční a koncový bod trasy.

2. Přidáním následujícího kódu jazyka JavaScript přidejte na mapu špendlíky pro počáteční a koncový bod:

    ```JavaScript
    // Fit the map window to the bounding box defined by the start and destination points
    var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    map.setCameraBounds({
        bounds: [swLon, swLat, neLon, neLat],
        padding: 50
    });

    map.addEventListener("load", function () { 
        // Add pins to the map for the start and end point of the route
        map.addPins([startPin, destinationPin], {
            name: "route-pins",
            textFont: "SegoeUi-Regular",
            textOffset: [0, -20]
        });
    });
    ```
    **map.setCameraBounds** upraví okno mapy podle souřadnic počátečního a koncového bodu. **map.addEventListener** zajišťuje načtení všech funkcí map přidaných do mapy po plném načtení mapy. Rozhraní API **map.addPins** v naslouchacím procesu událostí přidá do mapového ovládacího prvku body jako vizuální součásti.

3. Uložte soubor **MapRoute.html** a aktualizujte prohlížeč. Teď se ve středu mapy zobrazí Seattle a můžete si všimnou modrých špendlíků, které označují počáteční a koncový bod.

   ![Zobrazení mapy s označeným počátečním a koncovým bodem](./media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Získání pokynů

Tato část ukazuje způsob použití rozhraní API Maps Route Service k vyhledání trasy z daného počátečního bodu do cíle. Route Service poskytuje rozhraní API pro plánování *nejrychlejší*, *nejkratší*, *úsporné* nebo *vzrušující* trasy mezi dvěma místy. Umožňuje uživatelům také plánovat trasy v budoucnu s použitím rozsáhlé databáze Azure s historickými dopravními informacemi a předvídat dobu trvání trasy pro kterýkoli den a čas. Další informace najdete v tématu [Získání pokynů k trase](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Všechny následující funkce by se měly přidat **do modulu eventListener pro načtení mapy**, aby se zajistilo jejich načtení po plném načtení mapy.

1. Nejprve do mapy přidejte novou vrstvu, na které se zobrazí cesta trasy neboli *LineString*. Do bloku *script* přidejte následující kód jazyka JavaScript.

    ```JavaScript
    // Initialize the linestring layer for routes on the map
    var routeLinesLayerName = "routes";
    map.addLinestrings([], {
        name: routeLinesLayerName,
        color: "#2272B9",
        width: 5,
        cap: "round",
        join: "round",
        before: "labels"
    });
    ```

2. Přidáním následujícího kódu JavaScriptu do bloku skriptu vytvořte instanci klientské služby.
    ```JavaScript
    var client = new atlas.service.Client(MapsAccountKey);
    ```

3. Přidejte následující blok kódu a vytvořte řetězec dotazu trasy.
    ```JavaScript
    // Construct the route query string
    var routeQuery = startPoint.coordinates[1] +
        "," +
        startPoint.coordinates[0] +
        ":" +
        destinationPoint.coordinates[1] +
        "," +
        destinationPoint.coordinates[0];
    ```

4. Abyste získali trasu, přidejte do skriptu následující blok kódu. Dotazuje se směrovací služby Azure Maps prostřednictvím metody [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) a potom analyzuje odpověď do formátu GeoJSON pomocí [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes). Potom přidá všechny čáry odpovědi na mapu za účelem vykreslení mapy. Další informace získáte v části o [přidání čáry na mapu](./map-add-shape.md#addALine).

    ```JavaScript
    // Execute the query then add the route to the map once a response is received  
    client.route.getRouteDirections(routeQuery).then(response => {
         // Parse the response into GeoJSON
         var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

         // Get the first in the array of routes and add it to the map
         map.addLinestrings([geoJsonResponse.getGeoJsonRoutes().features[0]], {
             name: routeLinesLayerName
         });
    });
    ```

5. Uložte soubor **MapRoute.html** a aktualizujte webový prohlížeč. V případě úspěšného připojení s použitím rozhraní Maps API by se měla zobrazit mapa podobná následující.

    ![Ovládací prvek Mapa a Route Service v Azure](./media/tutorial-route-location/map-route.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření nové webové stránky s použitím rozhraní API pro mapové ovládací prvky
> * Nastavit souřadnice pro adresu
> * Zadat dotaz na službu Route Service ohledně trasy k bodu zájmu

Přístup k ukázce kódu pro tento kurz můžete získat tady:

> [Vyhledání trasy pomocí Azure Maps](https://github.com/Azure-Samples/azure-maps-samples/blob/master/src/route.html)

V dalším kurzu se dozvíte, jak vytvořit dotaz na trasu s omezeními, jako jsou režim dopravy nebo typ nákladu, a pak na stejné mapě zobrazit více tras.

> [!div class="nextstepaction"]
> [Vyhledání tras pro různé režimy dopravy](./tutorial-prioritized-routes.md)

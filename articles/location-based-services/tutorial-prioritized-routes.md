---
title: "Více tras v případě použití Azure Location Based Services | Dokumentace Microsoftu"
description: "S použitím Azure Location Based Services můžete hledat trasy pro různé režimy dopravy."
services: location-based-services
keywords: 
author: kgremban
ms.author: kgremban
ms.date: 11/28/2017
ms.topic: tutorial
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 986e8667ca421baa78647e77e43fef92d81a7982
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-location-based-services"></a>S použitím Azure Location Based Services můžete hledat trasy pro různé režimy dopravy.

Tento kurz demonstruje způsob použití účtu Azure Location Based Services a sady SDK Route Service k vyhledání trasy k bodu zájmu s určením priority podle režimu dopravy. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurovat dotaz na rozhraní API Route Service
> * Vykreslovat trasy s určenou prioritou podle režimu dopravy

## <a name="prerequisites"></a>Požadavky

Než budete pokračovat, nezapomeňte [vytvořit účet Azure Location Based Services](./tutorial-search-location.md#createaccount) a [získat z účtu klíč](./tutorial-search-location.md#getkey). Můžete také sledovat způsob použití rozhraní API pro mapové ovládací prvky a službu Search Service popsaný v kurzu [Hledání okolních bodů zájmu s použitím Azure Location Based Services](./tutorial-search-location.md) a seznámit se se základním používáním rozhraní API Route Service popsaným v kurzu [Trasa k bodu zájmu s použitím Azure Location Based Services](./tutorial-route-location.md).


<a id="queryroutes"></a>

## <a name="configure-your-route-service-query"></a>Konfigurování dotazu na rozhraní API Route Service

Provedením následujících kroků vytvořte statickou stránku HTML s vloženým rozhraním API pro mapové ovládací prvky služeb Location Based Services. 

1. Na místním počítači vytvořte nový soubor s názvem **MapTruckRoute.html**. 
2. Přidejte do souboru následující součásti HTML:

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Truck Route</title>
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
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
    Všimněte si, že jsou v hlavičce HTML vložena umístění prostředků pro soubory CSS a JavaScript pro knihovnu Azure Location Based Services. Všimněte si také bloku *script* přidaného do těla souboru HTML. Tento blok bude obsahovat vložený kód jazyka JavaScript pro přístup k rozhraní API pro mapové ovládací prvky v Azure.
3. Do bloku *script* v souboru HTML přidejte následující kód jazyka JavaScript. Nahraďte zástupný symbol *<insert-key>* primárním klíčem účtu Location Based Services.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var LBSAccountKey = "<_your account key_>";
    var map = new atlas.Map("map", {
        "subscription-key": LBSAccountKey
    });
    ```
    Objekt **atlas.Map** umožňuje ovládání vizuální a interaktivní webové mapy a je součástí rozhraní API pro mapové ovládací prvky prostředí Azure.

4. Přidáním následujícího kódu jazyka JavaScript do bloku *script* přidejte do mapy zobrazení toku provozu:

    ```JavaScript
    // Add Traffic Flow to the Map
    map.setTraffic({
        flow: "relative"
    });
    ```
    Tento kód nastaví tok provozu na hodnotu `relative`, která odpovídá relativní rychlosti silniční dopravy vzhledem k volnému toku. Můžete také nastavit hodnotu rychlosti silniční dopravy `absolute` nebo `relative-delay`, která zobrazuje relativní rychlost, pokud se liší od volného toku. 

5. Přidáním následujícího kódu jazyka JavaScript vytvořte špendlíky pro počáteční a koncový bod trasy:

    ```JavaScript
    // Create the GeoJSON objects which represent the start and end point of the route
    var startPoint = new atlas.data.Point([-122.356099, 47.580045]);
    var startPin = new atlas.data.Feature(startPoint, {
        title: "Fabrikam, Inc.",
        icon: "pin-round-blue"
    });

    var destinationPoint = new atlas.data.Point([-122.130137, 47.644702]);
    var destinationPin = new atlas.data.Feature(destinationPoint, {
        title: "Microsoft",
        icon: "pin-blue"
    });
    ```
    Tento kód vytvoří dva [objekty GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) představující počáteční a koncový bod trasy. 

6. Přidáním následujícího kódu jazyka JavaScript přidejte do mapového ovládacího prvku vrstvy *linestrings*, aby se zobrazovaly trasy podle režimu dopravy, například _car_ (osobní auto) nebo _truck_ (nákladní auto).

    ```JavaScript
    // Place route layers on the map
    var carRouteLayerName = "car-route";
    map.addLinestrings([], {
        name: carRouteLayerName,
        color: "#B76DAB",
        width: 5,
        cap: "round",
        join: "round",
        before: "labels"
    });

    var truckRouteLayerName = "truck-route";
    map.addLinestrings([], {
        name: truckRouteLayerName,
        color: "#2272B9",
        width: 9,
        cap: "round",
        join: "round",
        before: carRouteLayerName
    });
    ```

7. Přidáním následujícího kódu jazyka JavaScript přidejte na mapu počáteční a koncový bod:

    ```JavaScript
    // Fit the map window to the bounding box defined by the start and destination points
    var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    map.setCameraBounds({
        bounds: [swLon, swLat, neLon, neLat],
        padding: 100
    });

    // Add pins to the map for the start and end point of the route
    map.addPins([startPin, destinationPin], {
        name: "route-pins",
        textFont: "SegoeUi-Regular",
        textOffset: [0, -20]
    });
    ``` 
    Rozhraní API **map.setCameraBounds** upraví okno mapy podle souřadnic počátečního a koncového bodu. Rozhraní API **map.addPins** přidá do mapového ovládacího prvku body jako vizuální součásti.

8. Uložte si soubor **MapTruckRoute.html** do počítače. 

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Vykreslovat trasy s určenou prioritou podle režimu dopravy

Tato část ilustruje způsob použití rozhraní API Route Service služeb Azure Location Based Services k vyhledání více tras z daného počátečního bodu na cílové místo pro příslušný režim dopravy. Rozhraní API Route Service poskytuje rozhraní API pro plánování nejrychlejší, nejkratší nebo úsporné trasy mezi dvěma místy s přihlédnutím k okamžité dopravní situaci. Umožňuje uživatelům také plánovat trasy v budoucnu s použitím rozsáhlé databáze Azure s historickými dopravními informacemi a předvídat dobu trvání trasy pro kterýkoli den a čas. 

1. Otevřete soubor **MapTruckRoute.html**, který jste vytvořili v předchozí části, a přidejte do bloku *script* následující kód jazyka JavaScript, který s použitím rozhraní API Route Service zjistí trasu pro nákladní vozidlo.

    ```JavaScript
    // Perform a request to the route service and draw the resulting truck route on the map
    var xhttpTruck = new XMLHttpRequest();
    xhttpTruck.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(this.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                name: truckRouteLayerName
            });
        }
    };

    var truckRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
    truckRouteUrl += "&api-version=1.0";
    truckRouteUrl += "&subscription-key=" + LBSAccountKey;
    truckRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];
    truckRouteUrl += "&travelMode=truck";
    truckRouteUrl += "&vehicleWidth=2";
    truckRouteUrl += "&vehicleHeight=2";
    truckRouteUrl += "&vehicleLength=5";
    truckRouteUrl += "&vehicleLoadType=USHazmatClass2";

    xhttpTruck.open("GET", truckRouteUrl, true);
    xhttpTruck.send();
    ```
    Tento fragment kódu vytvoří požadavek [XMLHttpRequest](https://xhr.spec.whatwg.org/) a přidá obslužnou rutinu události pro parsování příchozí odpovědi. V případě úspěšné odpovědi vytvoří pole souřadnic pro vrácenou trasu a přidá ji do vrstvy `truckRouteLayerName` na mapě. 
    
    Tento fragment kódu rovněž odešle dotaz na rozhraní API Route Service s cílem zjistit trasu pro zadaný počáteční a koncový bod pro příslušný klíč účtu. Jako indikace trasy pro těžké nákladní vozidlo jsou použité následující volitelné parametry:
   - Parametr `travelMode=truck` určuje režim dopravy *truck* (nákladní vozidlo). Další podporované režimy dopravy jsou *taxi*, *bus* (autobus), *van* (dodávka), *motorcycle* (motorka) a výchozí *car* (osobní auto).
   - Parametry `vehicleWidth`, `vehicleHeight` a `vehicleLength` určují rozměry vozidla v metrech a přihlíží se k nim jen v případě, že je nastaven režim dopravy *truck*.
   - Parametr `vehicleLoadType` určuje, že se jedná o nebezpečný náklad, jehož doprava je na některých silnicích zakázána. Aktuálně se k němu přihlíží jen v případě režimu *truck*.

2. Přidáním následujícího kódu jazyka JavaScript zjistěte trasu pro osobní auto s použitím rozhraní API Route Service:

    ```JavaScript
    // Perform a request to the route service and draw the resulting car route on the map
    var xhttpCar = new XMLHttpRequest();
    xhttpCar.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(this.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                name: carRouteLayerName
            });
        }
    };

    var carRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
    carRouteUrl += "&api-version=1.0";
    carRouteUrl += "&subscription-key=" + LBSAccountKey;
    carRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttpCar.open("GET", carRouteUrl, true);
    xhttpCar.send();
    ```
    Tento fragment kódu vytvoří další požadavek [XMLHttpRequest](https://xhr.spec.whatwg.org/) a přidá obslužnou rutinu události pro parsování příchozí odpovědi. V případě úspěšné odpovědi vytvoří pole souřadnic pro vrácenou trasu a přidá ji do vrstvy `carRouteLayerName` na mapě. 
    
    Tento fragment kódu rovněž odešle dotaz na rozhraní API Route Service s cílem zjistit trasu pro zadaný počáteční a koncový bod pro příslušný klíč účtu. Vzhledem k tomu, že nejsou použity žádné další parametry, je vrácena trasa pro výchozí režim dopravy *car*. 

3. Uložte soubor **MapTruckRoute.html** na místní počítač, potom ho otevřete ve webovém prohlížeči podle svého výběru a podívejte se na výsledek. V případě úspěšného připojení s použitím rozhraní API služeb Location Based Services by se měla zobrazit mapa podobná následující. 

    ![Trasy s určenou prioritou s použitím rozhraní API Route Service v prostředí Azure](./media/tutorial-prioritized-routes/lbs-prioritized-routes.png)

    Všimněte si, že trasa pro nákladní auto je modrá, zatímco trasa pro osobní auto je fialová.

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Konfigurovat dotaz na rozhraní API Route Service
> * Vykreslovat trasy s určenou prioritou podle režimu dopravy

Pokud se chcete podrobněji seznámit se sadou SDK Azure Location Based Services, pokračujte články **Koncepty** a **Postupy**. 

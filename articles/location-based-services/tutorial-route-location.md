---
title: "Hledání trasy s použitím Azure Location Based Services | Dokumentace Microsoftu"
description: "Trasa k bodu zájmu s použitím Azure Location Based Services"
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
ms.openlocfilehash: b54d97afb4e762a7d252acf7cf53a5deac01d43f
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="route-to-a-point-of-interest-using-azure-location-based-services"></a>Trasa k bodu zájmu s použitím Azure Location Based Services

Tento kurz demonstruje způsob použití účtu Azure Location Based Services a sady SDK Route Service k vyhledání trasy k bodu zájmu. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Získat souřadnice pro adresu
> * Zadat dotaz na rozhraní API Route Service ohledně trasy k bodu zájmu

## <a name="prerequisites"></a>Požadavky

Než budete pokračovat, nezapomeňte [vytvořit účet Azure Location Based Services](./tutorial-search-location.md#createaccount) a [získat pro účet klíč předplatného](./tutorial-search-location.md#getkey). Můžete také sledovat způsob použití rozhraní API pro mapové ovládací prvky a službu Search Service popsaný v kurzu [Hledání okolních bodů zájmu s použitím Azure Location Based Services](./tutorial-search-location.md).


<a id="getcoordinates"></a>

## <a name="get-address-coordinates"></a>Získání souřadnic pro adresu

Provedením následujících kroků vytvořte statickou stránku HTML s vloženým rozhraním API pro mapové ovládací prvky služeb Location Based Services. 

1. Na místním počítači vytvořte nový soubor s názvem **MapRoute.html**. 
2. Přidejte do souboru následující součásti HTML:

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Route</title>
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
    Všimněte si, že jsou v hlavičce HTML vložena umístění prostředků pro soubory CSS a JavaScript pro knihovnu Azure Location Based Services. Všimněte si také bloku *script* v těle souboru HTML, který bude obsahovat vložený kód jazyka JavaScript pro přístup k rozhraním API služeb Azure Location Based Services.

3. Do bloku *script* v souboru HTML přidejte následující kód jazyka JavaScript. Ve skriptu použijte primární klíč svého účtu Location Based Services.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var LBSAccountKey = "<_your account key_>";
    var map = new atlas.Map("map", {
        "subscription-key": LBSAccountKey
    });
    ```
    Objekt **atlas.Map** umožňuje ovládání vizuální a interaktivní webové mapy a je součástí rozhraní API pro mapové ovládací prvky prostředí Azure.

4. Do bloku *script* přidejte následující kód jazyka JavaScript. Tímto způsobem se do mapového ovládacího prvku přidá vrstva *linestrings*, aby bylo možné zobrazit trasu:

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

5. Přidáním následujícího kódu jazyka JavaScript vytvořte počáteční a koncový bod trasy:

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
    Tento kód vytvoří dva [objekty GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) představující počáteční a koncový bod trasy. Koncový bod je kombinace zeměpisné šířky a délky pro jednu z *čerpacích stanic* vyhledávaných v předchozím kurzu [Hledání okolních bodů zájmu s použitím Azure Location Based Services](./tutorial-search-location.md).

6. Přidáním následujícího kódu jazyka JavaScript přidejte na mapu špendlíky pro počáteční a koncový bod:

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

    // Add pins to the map for the start and end point of the route
    map.addPins([startPin, destinationPin], {
        name: "route-pins",
        textFont: "SegoeUi-Regular",
        textOffset: [0, -20]
    });
    ``` 
    Rozhraní API **map.setCameraBounds** upraví okno mapy podle souřadnic počátečního a koncového bodu. Rozhraní API **map.addPins** přidá do mapového ovládacího prvku body jako vizuální součásti.

7. Uložte si soubor **MapRoute.html** do počítače. 

<a id="getroute"></a>

## <a name="query-route-service-for-directions-to-point-of-interest"></a>Zadání dotazu na rozhraní API Route Service ohledně trasy k bodu zájmu

Tato část ilustruje způsob použití rozhraní API Route Service služeb Azure Location Based Services k vyhledání trasy z daného počátečního bodu na cílové místo. Rozhraní API Route Service poskytuje rozhraní API pro plánování nejrychlejší, nejkratší nebo úsporné trasy mezi dvěma místy s přihlédnutím k okamžité dopravní situaci. Umožňuje uživatelům také plánovat trasy v budoucnu s použitím rozsáhlé databáze Azure s historickými dopravními informacemi a předvídat dobu trvání trasy pro kterýkoli den a čas. 

1. Otevřete soubor **MapRoute.html**, který jste vytvořili v předchozí části, a přidejte do bloku *script* následující kód jazyka JavaScript, který ilustruje použití rozhraní API Route Service.

    ```JavaScript
    // Perform a request to the route service and draw the resulting route on the map
    var xhttp = new XMLHttpRequest();
    xhttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(xhttp.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], { name: routeLinesLayerName });
        }
    };
    ```
    Tento fragment kódu vytvoří požadavek [XMLHttpRequest](https://xhr.spec.whatwg.org/) a přidá obslužnou rutinu události pro parsování příchozí odpovědi. V případě úspěšné odpovědi se vytvoří pole souřadnic pro úseky první vrácené trasy. Potom se tato sada souřadnic pro příslušnou trasu přidá do vrstvy mapy *linestrings*.

2. Přidejte do bloku *script* následující kód, který odešle požadavek XMLHttpRequest do rozhraní API Route Service v rámci služeb Azure Location Based Services:

    ```JavaScript
    var url = "https://atlas.microsoft.com/route/directions/json?";
    url += "&api-version=1.0";
    url += "&subscription-key=" + LBSAccountKey;
    url += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttp.open("GET", url, true);
    xhttp.send();
    ```
    Výše uvedený požadavek demonstruje požadované parametry, kterými jsou klíč účtu a souřadnice počátečního a koncového bodu (v uvedeném pořadí). 

3. Uložte soubor **MapRoute.html** na místní počítač, potom ho otevřete ve webovém prohlížeči podle svého výběru a podívejte se na výsledek. V případě úspěšného připojení s použitím rozhraní API služeb Location Based Services by se měla zobrazit mapa podobná následující. 

    ![Ovládací prvek Mapa a Route Service v Azure](./media/tutorial-route-location/lbs-map-route.png)


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Získat souřadnice pro adresu
> * Zadat dotaz na rozhraní API Route Service ohledně trasy k bodu zájmu

Pokračujte kurzem [Hledání tras pro různé režimy dopravy s použitím Azure Location Based Services](./tutorial-prioritized-routes.md), v němž se naučíte používat Azure Location Based Services k určení priority tras k požadovanému bodu zájmu podle režimu dopravy. 

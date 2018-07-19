---
title: Více tras v případě použití Azure Maps | Microsoft Docs
description: Vyhledání tras pro různé režimy dopravy s využitím Azure Maps
author: dsk-2015
ms.author: dkshir
ms.date: 05/07/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 83ca46ecb8f0cce2ff8c749016eb3ad1ac7df7cf
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988965"
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-maps"></a>Vyhledání tras pro různé režimy dopravy s využitím Azure Maps

Tento kurz demonstruje způsob použití účtu Azure Maps a služby Route Service k vyhledání trasy k bodu zájmu s určením priority podle režimu dopravy. Na mapě zobrazíte dvě různé trasy, jednu pro auta a jednu pro nákladní vozy, pro které můžou platit omezení trasy kvůli výšce, váze nebo nebezpečnému nákladu. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření nové webové stránky s použitím rozhraní API pro mapové ovládací prvky
> * Vizualizace aktuálního provozu na mapě
> * Vytvoření dotazů na trasy s deklarací režimu dopravy
> * Zobrazení více tras na mapě

## <a name="prerequisites"></a>Požadavky

Než budete pokračovat, podle postupu v prvním kurzu [vytvořte účet Azure Maps](./tutorial-search-location.md#createaccount) a [získejte pro účet klíč předplatného](./tutorial-search-location.md#getkey). 


## <a name="create-a-new-map"></a>Vytvoření nové mapy 
Následující kroky ukazují, jak vytvořit statickou stránku HTML s vloženým rozhraním API pro mapové ovládací prvky. 

1. Na místním počítači vytvořte nový soubor s názvem **MapTruckRoute.html**. 
2. Přidejte do souboru následující komponenty HTML:

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
    V hlavičce HTML jsou vložená umístění prostředků pro soubory CSS a JavaScript pro knihovnu Azure Maps. Segment *script* v těle HTML bude obsahovat vložený kód JavaScriptu pro mapu.
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

   ![Zobrazení základní mapy](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Vizualizace aktuálního provozu

1. Pokud mapě neurčíte, kam se má zaměřit, zobrazí se na ní celý svět. Abyste mohli zobrazit data o provozu, nastavte pro mapu středový bod a úroveň přiblížení. Nahraďte kód deklarující `new atlas.Map` následujícím kódem JavaScriptu: 
    
    ```JavaScript
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey,
        center: [-118.2437,34.0522],
        zoom: 12
    });
    ```

    Tento kód pro mapu nastaví středový bod a deklaruje úroveň přiblížení, abyste se ve výchozím nastavení mohli zaměřit na konkrétní oblast. 

1. Přidejte do mapy zobrazení aktuálního provozu:

    ```JavaScript
    // Add Traffic Flow to the Map
    map.setTraffic({
        flow: "relative"
    });
    ```
    Tento kód nastaví tok provozu na hodnotu `relative`, která odpovídá relativní rychlosti silniční dopravy vzhledem k volnému toku. Můžete také nastavit hodnotu rychlosti silniční dopravy `absolute` nebo `relative-delay`, která zobrazuje relativní rychlost, pokud se liší od volného toku. 

2. Uložte soubor **MapTruckRoute.html** a aktualizujte stránku v prohlížeči. Měly by se zobrazit ulice Los Angeles s daty o aktuálním provozu.

   ![Zobrazení mapy provozu](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="set-start-and-end-points"></a>Nastavení počátečního a koncového bodu

Pro účely tohoto kurzu jako počáteční bod nastavte fiktivní společnost Fabrikam v Seattlu a jako cílový bod nastavte pobočku Microsoftu. 

1. Přidáním následujícího kódu jazyka JavaScript vytvořte špendlíky pro počáteční a koncový bod trasy:

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

2. Přidáním následujícího kódu jazyka JavaScript přidejte na mapu počáteční a koncový bod:

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
    Volání **map.setCameraBounds** upraví okno mapy podle souřadnic počátečního a koncového bodu. Rozhraní API **map.addPins** přidá do mapového ovládacího prvku body jako vizuální součásti.

3. Uložte soubor a aktualizujte prohlížeč. Na mapě se zobrazí špendlíky. Přestože jste pro mapu deklarovali středový bod v Los Angeles, volání **map.setCameraBounds** mapu posunulo tak, aby se zobrazil počáteční a koncový bod. 

   ![Zobrazení mapy s počátečním a koncovým bodem](./media/tutorial-prioritized-routes/pins-map.png)


<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Vykreslovat trasy s určenou prioritou podle režimu dopravy

Tato část ukazuje způsob použití rozhraní API Maps Route Service k vyhledání více tras z daného počátečního bodu do cíle v závislosti na režimu dopravy. Route Service poskytuje rozhraní API pro plánování *nejrychlejší*, *nejkratší*, *úsporné* nebo *vzrušující* trasy mezi dvěma místy s přihlédnutím k aktuální dopravní situaci. Umožňuje uživatelům také plánovat trasy v budoucnu s použitím rozsáhlé databáze Azure s historickými dopravními informacemi a předvídat dobu trvání trasy pro kterýkoli den a čas. Další informace najdete v tématu [Získání pokynů k trase](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).


1. Nejprve do mapy přidejte novou vrstvu, na které se zobrazí cesta trasy neboli *LineString*. V tomto kurzu existují dvě různé trasy **car-route** a **truck-route** a obě trasy mají vlastní styl. Do bloku *script* přidejte následující kód JavaScriptu:

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

2. Do bloku *script* přidejte následující kód JavaScriptu, který si vyžádá trasu pro nákladní vůz a zobrazí výsledky na mapě:

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
    truckRouteUrl += "&subscription-key=" + MapsAccountKey;
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
    
    Tento fragment kódu také vytvoří dotaz pro Maps Route Service s použitím vašeho klíče účtu. Dotaz zahrnuje souřadnice počátečního a koncového bodu a volitelné parametry, které značí, že je trasa určená pro těžký nákladní vůz.

2. Přidejte následující kód JavaScriptu, který si vyžádá trasu pro auto a zobrazí výsledky:

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
    carRouteUrl += "&subscription-key=" + MapsAccountKey;
    carRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttpCar.open("GET", carRouteUrl, true);
    xhttpCar.send();
    ```
    Tento fragment kódu vytvoří další požadavek [XMLHttpRequest](https://xhr.spec.whatwg.org/) a přidá obslužnou rutinu události pro parsování příchozí odpovědi. V případě úspěšné odpovědi vytvoří pole souřadnic pro vrácenou trasu a přidá ji do vrstvy `carRouteLayerName` na mapě. 
    
    Tento fragment kódu také vytvoří dotaz pro Maps Route Service s použitím vašeho klíče účtu. Dotaz zahrnuje souřadnice počátečního a koncového bodu. Vzhledem k tomu, že nejsou zadané žádné další parametry, služba Route Service jako režim dopravy použije výchozí hodnotu *car*. 

3. Uložte soubor **MapTruckRoute.html**, aktualizujte prohlížeč a prohlédněte si výsledek. V případě úspěšného připojení s použitím rozhraní Maps API by se měla zobrazit mapa podobná následující. 

    ![Trasy s určenou prioritou s použitím rozhraní API Route Service v prostředí Azure](./media/tutorial-prioritized-routes/prioritized-routes.png)

    Trasa pro nákladní vůz je modrá a tlustší, zatímco trasa pro auto je fialová a tenčí. Trasa pro auto prochází přes Lake Washington po silnici I-90, která vede tunely pod obytnými oblastmi, a proto je na ní zakázaný náklad nebezpečného odpadu. Trasa pro nákladní vůz s určeným typem nákladu USHazmatClass2 je správně přesměrovaná přes jinou silnici. 

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření nové webové stránky s použitím rozhraní API pro mapové ovládací prvky
> * Vizualizace aktuálního provozu na mapě
> * Vytvoření dotazů na trasy s deklarací režimu dopravy
> * Zobrazení více tras na mapě

Další informace o pokrytí a možnostech Azure Maps najdete v tématu [Úrovně přiblížení a mřížka dlaždic](zoom-levels-and-tile-grid.md) a dalších článcích věnovaných konceptům. 

Další příklady kódu a prostředí pro interaktivní psaní kódu najdete v tématu [Jak používat mapový ovládací prvek](how-to-use-map-control.md) a dalších příručkách s návody. 

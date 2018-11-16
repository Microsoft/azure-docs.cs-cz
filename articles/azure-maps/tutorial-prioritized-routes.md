---
title: Více tras v případě použití Azure Maps | Microsoft Docs
description: Vyhledání tras pro různé režimy dopravy s využitím Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 11/14/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 0a278eb1612ec9573c4d12611ccce2d1b5b971bc
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51705273"
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
    <html>
    <head>
        <title>Multiple routes by mode of travel</title>
        
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1"></script>

        <script>
            var map, datasource, client;

            function GetMap() {
                //Add Map Control JavaScript code here.
            }
        </script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #myMap {
                position: relative;
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```
    
    Všimněte si, že hlavička HTML zahrnuje soubory prostředků šablon stylů CSS a JavaScriptu hostované knihovnou Ovládací prvek Mapa v Azure. V těle stránky si všimněte události `onload`, která po načtení těla stránky zavolá funkci `GetMap`. Tato funkce bude obsahovat vložený kód JavaScriptu pro přístup k rozhraním Azure Maps API.

3. Do funkce `GetMap` přidejte následující kód JavaScriptu. Nahraďte řetězec **\<Your Azure Maps Key\>** primárním klíčem, který jste zkopírovali ze svého účtu Maps.

    ```JavaScript
    //Add your Azure Maps subscription key to the map SDK. 
    atlas.setSubscriptionKey('<Your Azure Maps Key>');

    //Initialize a map instance.
    map = new atlas.Map('myMap');
    ```

    Objekt **atlas.Map** umožňuje ovládání vizuální a interaktivní webové mapy a je součástí rozhraní API pro mapové ovládací prvky prostředí Azure.

4. Uložte soubor a otevřete ho v prohlížeči. V tuto chvíli máte základní mapu, kterou můžete dále rozvíjet.

   ![Zobrazení základní mapy](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Vizualizace aktuálního provozu

1. Přidejte do mapy zobrazení aktuálního provozu. `map.events.add` zajišťuje načtení všech funkcí map přidaných do mapy po plném načtení mapy.

    ```JavaScript
    map.events.add("load", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```
    
     Do mapy se přidá událost load, která se aktivuje po plném načtení prostředků mapy. V obslužné rutině události načtení mapy se nastavení toku provozu na mapě nastaví na hodnotu `relative`, která odpovídá relativní rychlosti silniční dopravy vzhledem k volnému toku. Můžete také nastavit hodnotu rychlosti silniční dopravy `absolute` nebo `relative-delay`, která zobrazuje relativní rychlost, pokud se liší od volného toku.

2. Uložte soubor **MapTruckRoute.html** a aktualizujte stránku v prohlížeči. Měly by se zobrazit ulice Los Angeles s daty o aktuálním provozu.

   ![Zobrazení mapy provozu](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Definice způsobu vykreslení trasy

V tomto kurzu se vypočítají dvě trasy, které se vykreslí na mapě. Jedna trasa využívá silnice pro automobilovou dopravu a druhá využívá silnice pro nákladní dopravu. Při vykreslení zobrazíme ikonu symbolu pro začátek a konec trasy a pro každou cestu trasy použijeme čáru jiné barvy.

1. Do funkce GetMap za inicializaci mapy přidejte následující kód JavaScriptu.

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('load', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: ['get', 'strokeColor'],
            strokeWidth: ['get', 'strokeWidth'],
            lineJoin: 'round',
            lineCap: 'round',
            filter: ['==', '$type', 'LineString']
        }), 'labels');

        //Add a layer for rendering point data.
        map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: ['get', 'icon'],
                allowOverlap: true
            },
            textOptions: {
                textField: ['get', 'title'],
                offset: [0, 1.2]
            },
            filter: ['==', '$type', 'Point']
        }));
    });
    ```
    
    Do mapy se přidá událost load, která se aktivuje po plném načtení prostředků mapy. V obslužné rutině události načtení mapy se vytvoří zdroj dat, do kterého se uloží čáry tras a také počáteční a koncové body tras. Vytvoří se vrstva čar, která se připojí ke zdroji dat a která definuje, jak se vykreslí čára trasy. Ve funkci čáry trasy se tloušťka a barva čáry načte z vlastností pomocí výrazů. Přidá se filtr, který zajistí, že se na této vrstvě vykreslí pouze data typu GeoJSON LineString. Při přidávání vrstvy do mapy se předá druhý parametr s hodnotou `'labels'`, který určuje, že se má tato vrstva vykreslit pod popisky mapy. Tím se zajistí, že čára trasy nepřekryje popisky silnic. Vytvoří se vrstva symbolů, která se připojí ke zdroji dat. Tato vrstva určuje, jak se vykreslí počáteční a koncový bod. V tomto případě se do ní přidaly výrazy pro načtení informací o obrázku ikony a textovém popisku z vlastností objektů jednotlivých bodů. 
    
2. Pro účely tohoto kurzu jako počáteční bod nastavte fiktivní společnost Fabrikam v Seattlu a jako cílový bod nastavte pobočku Microsoftu. Do obslužné rutiny události načtení mapy přidejte následující kód.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-round-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-blue'
    });
    ```
    
    Tento kód vytvoří dva [objekty GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) představující počáteční a koncový bod trasy. Ke každému bodu se přidají vlastnosti `title` a `icon`.

3. Dále přidáním následujícího kódu JavaScriptu přidejte na mapu špendlíky pro počáteční a koncový bod:

    ```JavaScript
    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });
    ```
    
    Počáteční a koncový bod se přidají ke zdroji dat. Ohraničující rámeček pro počáteční a koncový bod se vypočítá pomocí funkce `atlas.data.BoundingBox.fromData`. Tento ohraničující rámeček se používá k nastavení zobrazení počátečního a koncového bodu pro kameru mapy pomocí funkce `map.setCamera`. Přidá se odsazení, které kompenzuje rozměry ikon symbolů v pixelech.

4. Uložte soubor a aktualizujte prohlížeč. Na mapě se zobrazí špendlíky. Teď se ve středu mapy zobrazí Seattle a můžete si všimnou modrých špendlíků, které označují počáteční a koncový bod.

   ![Zobrazení mapy s počátečním a koncovým bodem](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Vykreslovat trasy s určenou prioritou podle režimu dopravy

Tato část ukazuje způsob použití rozhraní API Maps Route Service k vyhledání více tras z daného počátečního bodu do cíle v závislosti na režimu dopravy. Route Service poskytuje rozhraní API pro plánování *nejrychlejší*, *nejkratší*, *úsporné* nebo *vzrušující* trasy mezi dvěma místy s přihlédnutím k aktuální dopravní situaci. Umožňuje uživatelům také plánovat trasy v budoucnu s použitím rozsáhlé databáze Azure s historickými dopravními informacemi a předvídat dobu trvání trasy pro kterýkoli den a čas. Další informace najdete v tématu [Získání pokynů k trase](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Všechny následující bloky kódu by se měly přidat **do modulu eventListener pro načtení mapy**, aby se zajistilo jejich načtení po úplném načtení mapy.

1. Přidáním následujícího kódu JavaScriptu do obslužné rutiny události načtení mapy vytvořte instanci klienta služby.

    ```JavaScript
    //If the service client hasn't been created, create an instance of it.
    if (!client) {
        client = new atlas.service.Client(atlas.getSubscriptionKey());
    }
    ```
    
2. Přidejte následující blok kódu a vytvořte řetězec dotazu trasy.

    ```JavaScript
    //Create the route request with the query being the start and end point in the format 'startLongitude,startLatitude:endLongitude,endLatitude'.
    var routeQuery = startPoint.geometry.coordinates[1] +
        ',' +
        startPoint.geometry.coordinates[0] +
        ':' +
        endPoint.geometry.coordinates[1] +
        ',' +
        endPoint.geometry.coordinates[0];
    ```

3. Přidejte následující kód JavaScriptu, který si vyžádá trasu pro nákladní vůz s nákladem třídy USHazmatClass2 a zobrazí výsledky:

    ```JavaScript
    //Execute the truck route query then add the route to the map.
    client.route.getRouteDirections(routeQuery, {
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then(function (response) {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

        //Get the route line and add some style properties to it.
        var routeLine = geoJsonResponse.getGeoJsonRoutes().features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```
    Výše uvedený fragment kódu se dotazuje směrovací služby Azure Maps prostřednictvím metody [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) a pak pomocí metody [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest) parsuje odpověď do formátu GeoJSON. Pak pro vrácenou trasu vytvoří pole souřadnic a přidá ho ke zdroji dat. Přidá však také index 0, aby se zajistilo, že se vykreslí před všemi ostatními čárami ve zdroji dat. To se provádí proto, že výpočet trasy pro nákladní vůz bude často pomalejší než výpočet trasy pro auto, a kdyby se čára trasy pro nákladní vůz přidala ke zdroji dat až po čáře trasy pro auto, vykreslila by se nad ní. K čáře trasy pro nákladní vůz se přidají dvě vlastnosti – barva čáry v pěkném odstínu modré a tloušťka čáry 9 pixelů. 

4. Přidejte následující kód JavaScriptu, který si vyžádá trasu pro auto a zobrazí výsledky:

    ```JavaScript
    //Execute the car route query then add the route to the map.
    client.route.getRouteDirections(routeQuery).then(function (response) {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

        //Get the route line and add some style properties to it.
        var routeLine = geoJsonResponse.getGeoJsonRoutes().features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source.
        datasource.add(routeLine);
    });
    ```
    Tento fragment kódu používá stejný dotaz na trasu pro nákladní auto i pro auto. Dotazuje se směrovací služby Azure Maps prostřednictvím metody [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) a pak pomocí metody [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest) parsuje odpověď do formátu GeoJSON. Pak pro vrácenou trasu vytvoří pole souřadnic a přidá ho ke zdroji dat. K čáře trasy pro auto se přidají dvě vlastnosti – barva čáry v odstínu šedé a tloušťka čáry 5 pixelů. 

5. Uložte soubor **MapTruckRoute.html**, aktualizujte prohlížeč a prohlédněte si výsledek. V případě úspěšného připojení s použitím rozhraní Maps API by se měla zobrazit mapa podobná následující.

    ![Trasy s určenou prioritou s použitím rozhraní API Route Service v prostředí Azure](./media/tutorial-prioritized-routes/prioritized-routes.png)

    Trasa pro nákladní vůz je modrá a tlustší, zatímco trasa pro auto je fialová a tenčí. Trasa pro auto prochází přes Lake Washington po silnici I-90, která vede tunely pod obytnými oblastmi, a proto je na ní zakázaný náklad nebezpečného odpadu. Trasa pro nákladní vůz s určeným typem nákladu USHazmatClass2 je správně přesměrovaná přes jinou silnici.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření nové webové stránky s použitím rozhraní API pro mapové ovládací prvky
> * Vizualizace aktuálního provozu na mapě
> * Vytvoření dotazů na trasy s deklarací režimu dopravy
> * Zobrazení více tras na mapě

Přístup k vzorovému kódu pro tento kurz můžete získat tady:

> [Více tras v případě použití Azure Maps](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

[Podívejte se na živou ukázku](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

V dalším kurzu ukazuje proces vytvoření lokátoru jednoduché úložiště pomocí Azure Maps.

> [!div class="nextstepaction"]
> [Vytvořit úložiště pomocí Azure Maps](./tutorial-create-store-locator.md)
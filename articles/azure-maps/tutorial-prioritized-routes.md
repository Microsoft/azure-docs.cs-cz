---
title: Více tras v případě použití Azure Maps | Microsoft Docs
description: Vyhledání tras pro různé režimy dopravy s využitím Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 3e33eef430db627a82bb82e8c22ddc861d08982b
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2019
ms.locfileid: "59546261"
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
        <title>Map Route</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js"></script>

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

3. Do funkce `GetMap` přidejte následující kód JavaScriptu. Nahraďte řetězec `<Your Azure Maps Key>` s primární klíč, který jste zkopírovali ze svého účtu mapy.

    ```JavaScript
    //Instantiate a map object
    var map = new atlas.Map("myMap", {
        //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
        authOptions: {
            authType: 'subscriptionKey',
            subscriptionKey: '<Your Azure Maps Key>'
        }
    });
    ```

    `atlas.Map` Třída umožňuje ovládání vizuální a interaktivní webové mapy a je součástí služby API pro mapové ovládací Azure.

4. Uložte soubor a otevřete ho v prohlížeči. V tuto chvíli máte základní mapu, kterou můžete dále rozvíjet.

   ![Zobrazení základní mapy](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Vizualizace aktuálního provozu

1. Přidejte do mapy zobrazení aktuálního provozu. Mapování `ready` události čeká na mapování prostředků jsou načtené a připravené k bezpečně pracovat s ním.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    Na mapě `ready` obslužná rutina události, nastavení toku provozu na mapě nastavené na `relative`, která odpovídá relativní rychlosti silniční dopravy vzhledem k volnému toku. Můžete také nastavit hodnotu rychlosti silniční dopravy `absolute` nebo `relative-delay`, která zobrazuje relativní rychlost, pokud se liší od volného toku.

2. Uložte soubor **MapTruckRoute.html** a aktualizujte stránku v prohlížeči. Je-li pracovat s mapy a přiblížení Los Angeles, měli byste vidět ulice s aktuálními údaji provoz.

   ![Zobrazení mapy provozu](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Definice způsobu vykreslení trasy

V tomto kurzu se vypočítají dvě trasy, které se vykreslí na mapě. Jedna trasa využívá silnice pro automobilovou dopravu a druhá využívá silnice pro nákladní dopravu. Při vykreslení zobrazíme ikonu symbolu pro začátek a konec trasy a pro každou cestu trasy použijeme čáru jiné barvy.

1. Po inicializaci mapy, přidejte následující kód jazyka JavaScript v mapách `ready` obslužné rutiny události.

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('ready', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: ['get', 'strokeColor'],
            strokeWidth: ['get', 'strokeWidth'],
            lineJoin: 'round',
            lineCap: 'round'
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
            filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']] //Only render Point or MultiPoints in this layer.
        }));
    });
    ```
    
    V mapách `ready` obslužná rutina události, zdroj dat je vytvořili pro uložení postupu řádky, jakož i počáteční a koncové body. Vytvoří se vrstva čar, která se připojí ke zdroji dat a která definuje, jak se vykreslí čára trasy. Ve funkci čáry trasy se tloušťka a barva čáry načte z vlastností pomocí výrazů. Při přidávání vrstvy do mapy se předá druhý parametr s hodnotou `'labels'`, který určuje, že se má tato vrstva vykreslit pod popisky mapy. Tím se zajistí, že čára trasy nepřekryje popisky silnic. Vytvoří se vrstva symbolů, která se připojí ke zdroji dat. Tato vrstva určuje, jak se vykreslí počáteční a koncový bod. V tomto případě se do ní přidaly výrazy pro načtení informací o obrázku ikony a textovém popisku z vlastností objektů jednotlivých bodů. 
    
2. Pro účely tohoto kurzu jako počáteční bod nastavte fiktivní společnost Fabrikam v Seattlu a jako cílový bod nastavte pobočku Microsoftu. V mapách `ready` obslužná rutina události, přidejte následující kód.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-round-blue'
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

    Počáteční a koncový bod se přidají ke zdroji dat. Ohraničující rámeček pro počáteční a koncový bod se vypočítá pomocí funkce `atlas.data.BoundingBox.fromData`. Tento ohraničovací rámeček slouží k nastavení zobrazení mapy kamery oproti použití celého postupu `map.setCamera` funkce. Přidá se odsazení, které kompenzuje rozměry ikon symbolů v pixelech.

4. Uložte soubor a aktualizujte prohlížeč. Na mapě se zobrazí špendlíky. Teď se ve středu mapy zobrazí Seattle a můžete si všimnou modrých špendlíků, které označují počáteční a koncový bod.

   ![Zobrazení mapy s počátečním a koncovým bodem](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Vykreslovat trasy s určenou prioritou podle režimu dopravy

Tato část ukazuje, jak použít rozhraní API pro mapy route service k vyhledání více tras z daného počátečního bodu na koncový bod příslušný režim dopravy. Route Service poskytuje rozhraní API pro plánování *nejrychlejší*, *nejkratší*, *úsporné* nebo *vzrušující* trasy mezi dvěma místy s přihlédnutím k aktuální dopravní situaci. Umožňuje uživatelům také plánovat trasy v budoucnu s použitím rozsáhlé databáze Azure s historickými dopravními informacemi a předvídat dobu trvání trasy pro kterýkoli den a čas. Další informace najdete v tématu [Získání pokynů k trase](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Všechny následující bloky kódu by se měly přidat **do modulu eventListener pro načtení mapy**, aby se zajistilo jejich načtení po úplném načtení mapy.

1. Ve funkci GetMap přidejte následující kód jazyka Javascript.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential` Vytvoří `SubscriptionKeyCredentialPolicy` k ověření požadavků HTTP ve službě Azure Maps se klíč předplatného. `atlas.service.MapsURL.newPipeline()` Přijímá `SubscriptionKeyCredential` zásady a vytvoří [kanálu](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest) instance. `routeURL` Představuje adresu URL ke službě Azure Maps [trasy](https://docs.microsoft.com/rest/api/maps/route) operace.

2. Po nastavení přihlašovacích údajů a adresu URL, přidejte následující JavaScript kód k vytvoření trasy od začátku na koncový bod pro nákladní vozidlo provádění USHazmatClass2 třídy nákladu a zobrazit výsledky.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request for a truck vehicle type
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates,{
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```

    Tento fragment kódu výše uvedené dotazy směrování služby Azure Maps pomocí [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-iot-typescript-latest) metody. Řádku postupu pak extrahují z kolekce funkcí GeoJSON z odpovědi, které je extrahována pomocí `geojson.getFeatures()` metody. Ke zdroji dat se pak přidá trasu řádku. Přidá také indexu 0 k zajištění, že je vykreslen před dalších řádků ve zdroji dat. To se provádí proto, že výpočet trasy pro nákladní vůz bude často pomalejší než výpočet trasy pro auto, a kdyby se čára trasy pro nákladní vůz přidala ke zdroji dat až po čáře trasy pro auto, vykreslila by se nad ní. Dvě vlastnosti se přidají do řádku postupu truck stroke barva, která se odlišuje od modré a šířku tahu devět pixelů.

3. Přidejte následující kód jazyka JavaScript sestavit trasu pro automobilu a zobrazit výsledky.

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.  
        datasource.add(routeLine);
    });
    ```

    Tento fragment kódu výše uvedené dotazy směrování služby Azure Maps pomocí [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-iot-typescript-latest) metody. Řádku postupu pak extrahují z kolekce funkcí GeoJSON z odpovědi, které je extrahována pomocí `geojson.getFeatures()` metody. Ke zdroji dat se pak přidá trasu řádku. Dvě vlastnosti se přidají do řádku postupu car stroke barva, která je odstínu nachová a šířku tahu 5 pixelů.  

4. Uložte soubor **MapTruckRoute.html**, aktualizujte prohlížeč a prohlédněte si výsledek. V případě úspěšného připojení s použitím rozhraní Maps API by se měla zobrazit mapa podobná následující.

    ![Trasy s určenou prioritou s použitím rozhraní API Route Service v prostředí Azure](./media/tutorial-prioritized-routes/prioritized-routes.png)

    Trasa pro nákladní vůz je modrá a tlustší, zatímco trasa pro auto je fialová a tenčí. Trasa pro auto prochází přes Lake Washington po silnici I-90, která vede tunely pod obytnými oblastmi, a proto je na ní zakázaný náklad nebezpečného odpadu. Trasa pro nákladní vůz s určeným typem nákladu USHazmatClass2 je správně přesměrovaná přes jinou silnici.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření nové webové stránky s použitím rozhraní API pro mapové ovládací prvky
> * Vizualizace aktuálního provozu na mapě
> * Vytvoření dotazů na trasy s deklarací režimu dopravy
> * Zobrazení více tras na mapě

> [!div class="nextstepaction"]
> [Zobrazit úplný zdrojový kód](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

> [!div class="nextstepaction"]
> [Živé ukázkové zobrazení](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

V dalším kurzu ukazuje proces vytvoření lokátoru jednoduché úložiště pomocí Azure Maps.

> [!div class="nextstepaction"]
> [Vytvořit úložiště pomocí Azure Maps](./tutorial-create-store-locator.md)

> [!div class="nextstepaction"]
> [Použití výrazů s daty styl](data-driven-style-expressions-web-sdk.md)
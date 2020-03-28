---
title: 'Kurz: Najít více tras podle způsobu cestování | Mapy Microsoft Azure'
description: V tomto kurzu se dozvíte, jak najít trasy pro různé způsoby cestování pomocí Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: c7ed2421f468dfbb64c635683a7909b517105bc7
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333790"
---
# <a name="tutorial-find-routes-for-different-modes-of-travel-using-azure-maps"></a>Kurz: Hledání tras pro různé způsoby cestování pomocí Azure Maps

Tento kurz ukazuje, jak používat účet Azure Maps a službu směrování. Služba trasy může najít trasu k vašemu bodu zájmu, upřednostňována podle způsobu vaší cesty. Na mapě můžete zobrazit dvě různé trasy, jednu pro osobní automobily a jednu pro nákladní automobily. Služba směrování bere v úvahu omezení z důvodu výšky a hmotnosti vozidla nebo z důvodu přepravy nebezpečného nákladu. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit novou webovou stránku s použitím rozhraní API pro mapové ovládací prvky
> * Vizualizace aktuálního provozu na mapě
> * Vytvoření dotazů na trasy s deklarací režimu dopravy
> * Zobrazení více tras na mapě

## <a name="prerequisites"></a>Požadavky
Než budete pokračovat, postupujte podle pokynů v části [Vytvoření účtu](quick-demo-map-app.md#create-an-account-with-azure-maps) a vyberte cenovou úroveň S1. Postupujte podle pokynů v [získání primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account) a získejte primární klíč pro svůj účet. Další informace o ověřování v Azure Maps najdete v [tématu správa ověřování v Azure Maps](how-to-manage-authentication.md).

## <a name="create-a-new-map"></a>Vytvoření nové mapy

Následující kroky ukazují, jak vytvořit statickou stránku HTML s vloženým rozhraním API pro mapové ovládací prvky.

1. Na místním počítači vytvořte nový soubor s názvem **MapTruckRoute.html**.
2. Přidejte do souboru následující součásti HTML:

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
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

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

3. Do funkce `GetMap` přidejte následující kód JavaScriptu. Nahraďte `<Your Azure Maps Key>` řetězec primárním klíčem, který jste zkopírovali ze svého účtu Mapy.

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

    Třída `atlas.Map` poskytuje ovládací prvek pro vizuální a interaktivní webové mapy a je součástí rozhraní API řízení map Azure.

4. Uložte soubor a otevřete ho v prohlížeči. V tuto chvíli máte základní mapu, kterou můžete dále rozvíjet.

   ![Zobrazení základní mapy](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Vizualizace aktuálního provozu

1. Přidejte do mapy zobrazení aktuálního provozu. Událost `ready` mapy čeká, dokud nejsou načteny prostředky mapy a připraveny k bezpečné interakci s ním.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    V obslužné rutině události mapy `ready` je `relative`nastavení toku provozu na mapě nastaveno na , což je rychlost vozovky vzhledem k volnému toku. Můžete také nastavit hodnotu rychlosti silniční dopravy `absolute` nebo `relative-delay`, která zobrazuje relativní rychlost, pokud se liší od volného toku.

2. Uložte soubor **MapTruckRoute.html** a aktualizujte stránku v prohlížeči. Pokud pracujete s mapou a přibližujete se k Los Angeles, měli byste vidět ulice s aktuálními dopravními údaji.

   ![Zobrazení provozu na mapě](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Definice způsobu vykreslení trasy

V tomto kurzu se vypočítají dvě trasy, které se vykreslí na mapě. Jedna trasa využívá silnice pro automobilovou dopravu a druhá využívá silnice pro nákladní dopravu. Při vykreslení zobrazíme ikonu symbolu pro začátek a konec trasy a různé barevné čáry pro každou trasu.

1. Po inicializaci mapy přidejte do obslužné rutiny události mapy `ready` následující kód JavaScriptu.

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
    
    V obslužné rutině události maps `ready` je vytvořen zdroj dat pro uložení čar trasy a počátečních a koncových bodů. Vytvoří se vrstva čar, která se připojí ke zdroji dat a která definuje, jak se vykreslí čára trasy. Ve funkci čáry trasy se tloušťka a barva čáry načte z vlastností pomocí výrazů. Při přidávání vrstvy do mapy se předá druhý parametr s hodnotou `'labels'`, který určuje, že se má tato vrstva vykreslit pod popisky mapy. Tím je zajištěno, že čára trasy nepokrývá popisky silnic. Vytvoří se vrstva symbolů, která se připojí ke zdroji dat. Tato vrstva určuje způsob vykreslení počátečního a koncového bodu. V tomto případě byly přidány výrazy pro načtení informací o obrázku ikony a textovém popisku z vlastností každého objektu bodu. 
    
2. Pro účely tohoto kurzu jako počáteční bod nastavte fiktivní společnost Fabrikam v Seattlu a jako cílový bod nastavte pobočku Microsoftu. Do obslužné rutiny události maps `ready` přidejte následující kód.

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

    Počáteční a koncový bod se přidají ke zdroji dat. Ohraničující rámeček pro počáteční a koncový bod se vypočítá pomocí funkce `atlas.data.BoundingBox.fromData`. Tento ohraničovací rámeček slouží k nastavení zobrazení `map.setCamera` mapových kamer po celé trase pomocí funkce. Přidá se odsazení, které kompenzuje rozměry ikon symbolů v pixelech.

4. Uložte soubor a aktualizujte prohlížeč. Na mapě se zobrazí špendlíky. Nyní je mapa soustředěna nad Seattlem. Můžete vidět kulatý modrý kolík označující počáteční bod a modrý kolík označující cílový bod.

   ![Zobrazení mapy s počátečním a koncovým bodem](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Vykreslovat trasy s určenou prioritou podle režimu dopravy

V této části se zobrazí způsob použití rozhraní API služby směrování Map. Rozhraní API trasy se používá k vyhledání více tras z daného počátečního bodu do koncového bodu na základě způsobu přenosu. Služba trasy poskytuje api pro plánování *nejrychlejších*, *nejkratších*, *ekologických*nebo *vzrušujících* tras. Nejen, že api plánují trasy mezi dvěma místy, ale také berou v úvahu aktuální dopravní podmínky. 

Rozhraní API trasy umožňuje uživatelům plánovat trasy v budoucnu pomocí rozsáhlé databáze historických přenosů Azure. Rozhraní API může předpovědět dobu trvání trasy pro daný den a čas. Další informace najdete v tématu [Získání pokynů k trase](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). 

Všechny následující bloky kódu by měly být přidány **v rámci mapy zatížení eventListener** zajistit, že načíst po načtení mapy.

1. Ve funkci GetMap přidejte do kódu Javascriptu následující.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   Vytvoří `SubscriptionKeyCredential` `SubscriptionKeyCredentialPolicy` k ověření http požadavky na Azure Maps s klíčem předplatného. Bere `atlas.service.MapsURL.newPipeline()` v `SubscriptionKeyCredential` zásadě a vytvoří [pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) instance. Představuje `routeURL` adresu URL operací trasy Azure Maps [Route.](https://docs.microsoft.com/rest/api/maps/route)

2. Po nastavení přihlašovacích údajů a adresy URL přidejte následující kód Jazyka JavaScript, abyste vytvořili trasu od začátku do konce pro nákladní vozidlo přepravující náklad třídy USHazmatClass2 a zobrazte výsledky.

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

    Tento fragment kódu výše dotazuje služby směrování Azure Maps prostřednictvím metody [getRouteDirections.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) Řádek trasy je pak extrahován z kolekce funkcí GeoJSON z `geojson.getFeatures()` odpovědi, která je extrahována pomocí metody. Řádek trasy je pak přidán do zdroje dat. Index 0 zajišťuje, že je vykreslen před všechny ostatní řádky ve zdroji dat. To se provádí, protože výpočet trasy nákladního vozidla bude často pomalejší než výpočet trasy vozidla. Pokud je trasa nákladního vozidla přidána do zdroje dat po trase vozidla, vykreslí se nad ní. K čáře trasy nákladního vozidla se přidají dvě vlastnosti, barva tahu, která je pěkným odstínem modré, a šířka tahu devět pixelů.

3. Přidejte následující kód JavaScriptu, abyste vytvořili trasu pro auto a zobrazili výsledky.

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

    Tento fragment kódu výše dotazuje služby směrování Azure Maps prostřednictvím metody [getRouteDirections.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) Řádek trasy je pak extrahován z kolekce funkcí GeoJSON z `geojson.getFeatures()` odpovědi, která je extrahována pomocí metody. Řádek trasy je pak přidán do zdroje dat. K čáře trasy vozu se přidají dvě vlastnosti, barva tahu, která je odstínem fialové, a šířka tahu pěti obrazových bodů.  

4. Uložte soubor **MapTruckRoute.html**, aktualizujte prohlížeč a prohlédněte si výsledek. V případě úspěšného připojení s použitím rozhraní Maps API by se měla zobrazit mapa podobná následující.

    ![Trasy s určenou prioritou s použitím rozhraní API Route Service v prostředí Azure](./media/tutorial-prioritized-routes/prioritized-routes.png)

    Trasa kamionu je tlustá modrá a trasa auta je tenká fialová. Trasa auta vede přes jezero Washington přes I-90, které prochází tunely pod obytnými oblastmi. Vzhledem k tomu, že tunely jsou v blízkosti obytných oblastí, náklad nebezpečného odpadu je omezen. Trasa nákladního vozidla, která určuje typ nákladu USHazmatClass2, je nasměrována k použití jiné dálnice.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit novou webovou stránku s použitím rozhraní API pro mapové ovládací prvky
> * Vizualizace aktuálního provozu na mapě
> * Vytvoření dotazů na trasy s deklarací režimu dopravy
> * Zobrazení více tras na mapě

> [!div class="nextstepaction"]
> [Zobrazit úplný zdrojový kód](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

> [!div class="nextstepaction"]
> [Zobrazit živý vzorek](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

Další kurz ukazuje proces vytváření jednoduchého lokátoru úložiště pomocí Azure Maps.

> [!div class="nextstepaction"]
> [Vytvoření lokátoru obchodu pomocí Azure Maps](./tutorial-create-store-locator.md)

> [!div class="nextstepaction"]
> [Použití výrazů pro styly založené na datech](data-driven-style-expressions-web-sdk.md)
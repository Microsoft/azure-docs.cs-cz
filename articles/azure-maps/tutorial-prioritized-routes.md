---
title: 'Kurz: vyhledání více tras podle režimu cestování | Mapy Microsoft Azure'
description: V tomto kurzu se naučíte, jak najít trasy pro různé režimy cestování pomocí Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: c7ed2421f468dfbb64c635683a7909b517105bc7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80333790"
---
# <a name="tutorial-find-routes-for-different-modes-of-travel-using-azure-maps"></a>Kurz: Vyhledání tras pro různé režimy cestování pomocí Azure Maps

V tomto kurzu se dozvíte, jak používat účet Azure Maps a službu Směrování. Služba směrování může trasu najít do svého bodu zájmu a nastavit prioritu podle vašeho způsobu cestování. Na mapě můžete zobrazit dvě různé trasy, jednu pro automobily a jednu pro nákladní automobily. Směrovací služba vezme v úvahu omezení z důvodu výšky a váhy vozidla nebo v případě, že vozidlo uskutečňuje nebezpečný náklad. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit novou webovou stránku s použitím rozhraní API pro mapové ovládací prvky
> * Vizualizace aktuálního provozu na mapě
> * Vytvoření dotazů na trasy s deklarací režimu dopravy
> * Zobrazení více tras na mapě

## <a name="prerequisites"></a>Požadavky
Než budete pokračovat, postupujte podle pokynů v části [Vytvoření účtu](quick-demo-map-app.md#create-an-account-with-azure-maps) a vyberte cenovou úroveň S1. Použijte k získání primárního klíče pro váš účet postup uvedený v části [získání primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account) . Další informace o ověřování v Azure Maps najdete v tématu [Správa ověřování v Azure Maps](how-to-manage-authentication.md).

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

3. Do funkce `GetMap` přidejte následující kód JavaScriptu. Nahraďte řetězec `<Your Azure Maps Key>` primárním klíčem, který jste zkopírovali z účtu Maps.

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

    `atlas.Map` Třída poskytuje ovládací prvek pro vizuální a interaktivní webovou mapu a je součástí rozhraní API služby Azure ovládací prvek mapa.

4. Uložte soubor a otevřete ho v prohlížeči. V tuto chvíli máte základní mapu, kterou můžete dále rozvíjet.

   ![Zobrazení základní mapy](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Vizualizace aktuálního provozu

1. Přidejte do mapy zobrazení aktuálního provozu. Událost Maps `ready` počká, dokud se prostředky map nenačte a neumožní bezpečně pracovat s ní.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    V obslužné rutině události mapy `ready` je nastavení toku přenosu na mapě nastaveno na `relative`, což je rychlost provozu vzhledem k volnému toku. Můžete také nastavit hodnotu rychlosti silniční dopravy `absolute` nebo `relative-delay`, která zobrazuje relativní rychlost, pokud se liší od volného toku.

2. Uložte soubor **MapTruckRoute.html** a aktualizujte stránku v prohlížeči. Pokud s mapou pracujete a přiblížíte se k Los Angeles, měla by se zobrazit ulice s aktuálními daty o provozu.

   ![Zobrazení provozu na mapě](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Definice způsobu vykreslení trasy

V tomto kurzu se vypočítají dvě trasy, které se vykreslí na mapě. Jedna trasa využívá silnice pro automobilovou dopravu a druhá využívá silnice pro nákladní dopravu. Když se vykreslí, zobrazí se ikona pro začátek a konec trasy a pro každou cestu cesty se vytvoří různé barevné čáry.

1. Po inicializaci mapy přidejte do obslužné rutiny události Maps `ready` následující kód jazyka JavaScript.

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
    
    V obslužné rutině události Maps `ready` se vytvoří zdroj dat, který bude ukládat řádky trasy a počáteční a koncové body. Vytvoří se vrstva čar, která se připojí ke zdroji dat a která definuje, jak se vykreslí čára trasy. Ve funkci čáry trasy se tloušťka a barva čáry načte z vlastností pomocí výrazů. Při přidávání vrstvy do mapy se předá druhý parametr s hodnotou `'labels'`, který určuje, že se má tato vrstva vykreslit pod popisky mapy. Tím se zajistí, že řádek trasy nepokrývá popisky cest. Vytvoří se vrstva symbolů, která se připojí ke zdroji dat. Tato vrstva určuje, jak budou vykresleny počáteční a koncové body. V tomto případě byly přidány výrazy pro načtení obrázku ikony a informace o popisku textu z vlastností u jednotlivých objektů objektu Point. 
    
2. Pro účely tohoto kurzu jako počáteční bod nastavte fiktivní společnost Fabrikam v Seattlu a jako cílový bod nastavte pobočku Microsoftu. V obslužné rutině události Maps `ready` přidejte následující kód.

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

    Počáteční a koncový bod se přidají ke zdroji dat. Ohraničující rámeček pro počáteční a koncový bod se vypočítá pomocí funkce `atlas.data.BoundingBox.fromData`. Toto ohraničovací pole slouží k nastavení zobrazení mapy kamer přes celou trasu pomocí `map.setCamera` funkce. Přidá se odsazení, které kompenzuje rozměry ikon symbolů v pixelech.

4. Uložte soubor a aktualizujte prohlížeč. Na mapě se zobrazí špendlíky. Mapa se teď nacentruje na střed Seattle. Můžete zobrazit kulatě modrý kód PIN, který označuje počáteční bod a modrý kód PIN označující bod dokončení.

   ![Zobrazení mapy s počátečním a koncovým bodem](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Vykreslovat trasy s určenou prioritou podle režimu dopravy

V této části se dozvíte, jak používat rozhraní API služby mapy tras. Rozhraní API trasy se používá k vyhledání několika tras z daného počátečního bodu až po koncový bod na základě vašeho režimu přenosu. Služba Směrování poskytuje rozhraní API pro plánování *nejrychlejší*, *nejkratší*, *ekonomické*nebo *Thrilling* trasy. Nejen naplánují trasy rozhraní API mezi dvěma umístěními, ale také zvažte aktuální podmínky provozu. 

Rozhraní API pro směrování umožňuje uživatelům plánovat trasy v budoucnu pomocí rozsáhlé historické databáze přenosů Azure. Rozhraní API může předpovědět dobu trvání tras pro daný den a čas. Další informace najdete v tématu [Získání pokynů k trase](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). 

Všechny následující bloky kódu by měly být přidány **v rámci naslouchacího procesu událostí zatížení mapy** , aby bylo zajištěno, že budou načteny po úplném načtení mapy.

1. Do funkce GetMap přidejte následující kód JavaScript Code.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential` Vytvoří `SubscriptionKeyCredentialPolicy` a ověří požadavky HTTP, které se mají Azure Maps pomocí klíče předplatného. `SubscriptionKeyCredential` Zásada `atlas.service.MapsURL.newPipeline()` převezme a vytvoří instanci [kanálu](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) . `routeURL` Představuje adresu URL pro Azure Maps operací [Směrování](https://docs.microsoft.com/rest/api/maps/route) .

2. Po nastavení přihlašovacích údajů a adresy URL přidejte následující kód JavaScriptu, který vytvoří trasu od začátku do koncového bodu pro nákladní automobil, který vede USHazmatClass2 náklad na vydanou třídu a zobrazí výsledky.

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

    Tento fragment kódu výše se dotazuje směrovací služby Azure Maps pomocí metody [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) . Z odpovědi, která je extrahována pomocí `geojson.getFeatures()` metody, je následně extrahována z kolekce funkcí injson. Řádek trasy se pak přidá do zdroje dat. Index 0 zajistí, že se vykreslí před všemi ostatními řádky ve zdroji dat. To se provádí, protože výpočet trasy vozíku bude často pomalejší než výpočet trasy auta. Pokud je čára postupu nákladní jednotky přidána do zdroje dat po trase auta, vykreslí se nad ní. Do řádku trasy vozíku se přidají dvě vlastnosti, Barva tahu, která je dobrým barevným trendem modrou, a tloušťka čáry devět pixelů.

3. Přidáním následujícího kódu jazyka JavaScript vytvořte trasu pro automobil a zobrazte výsledky.

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

    Tento fragment kódu výše se dotazuje směrovací služby Azure Maps pomocí metody [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) . Z odpovědi, která je extrahována pomocí `geojson.getFeatures()` metody, je následně extrahována z kolekce funkcí injson. Řádek trasy se pak přidá do zdroje dat. Do řádku trasy automobilu jsou přidány dvě vlastnosti, Barva tahu, která je vybarvení fialová a šířka tahu je 5 pixelů.  

4. Uložte soubor **MapTruckRoute.html**, aktualizujte prohlížeč a prohlédněte si výsledek. V případě úspěšného připojení s použitím rozhraní Maps API by se měla zobrazit mapa podobná následující.

    ![Trasy s určenou prioritou s použitím rozhraní API Route Service v prostředí Azure](./media/tutorial-prioritized-routes/prioritized-routes.png)

    Směr nákladní automobil je silný modrou a trasa automobilu je tenká fialová. Trasa automobilu projde přes Lake Washington prostřednictvím I-90, který prochází tunely v rámci domácích oblastí. Vzhledem k tomu, že tunely jsou blízko domácích oblastí, je zakázaný nebezpečný odpadní náklad. Směr nákladní lodě, který určuje typ nákladu USHazmatClass2, je směrován na použití jiné dálnice.

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
> [Zobrazit ukázku živého vysílání](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

V dalším kurzu se dozvíte, jak vytvořit jednoduchý Lokátor úložiště pomocí Azure Maps.

> [!div class="nextstepaction"]
> [Vytvoření lokátoru úložiště pomocí Azure Maps](./tutorial-create-store-locator.md)

> [!div class="nextstepaction"]
> [Použití výrazů pro styly založené na datech](data-driven-style-expressions-web-sdk.md)
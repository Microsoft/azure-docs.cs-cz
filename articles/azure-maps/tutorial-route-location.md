---
title: 'Kurz: Najít trasu k místu | Mapy Microsoft Azure'
description: Tento kurz ukazuje, jak vykreslit trasu do umístění (bod zájmu) na mapě pomocí služby Microsoft Azure Maps Routing Service.
author: philmea
ms.author: philmea
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 98c36176ecd2996e5f735c52017162a076ef4bde
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333761"
---
# <a name="tutorial-route-to-a-point-of-interest-using-azure-maps"></a>Kurz: Cesta k bodu zájmu pomocí Azure Maps

V tomto kurzu se dozvíte, jak pomocí účtu Azure Maps a sady SDK Route Service vyhledat trasu k bodu zájmu. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit novou webovou stránku s použitím rozhraní API pro mapové ovládací prvky
> * Nastavit souřadnice pro adresu
> * Zadat dotaz na rozhraní API Route Service ohledně trasy k bodu zájmu

## <a name="prerequisites"></a>Požadavky

Než budete pokračovat, postupujte podle pokynů v [aplikaci Vytvořit účet](quick-demo-map-app.md#create-an-account-with-azure-maps), potřebujete předplatné s cenovou úrovní S1. Postupujte podle pokynů v [získání primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account) a získejte primární klíč pro svůj účet. Další informace o ověřování v Azure Maps najdete v [tématu správa ověřování v Azure Maps](how-to-manage-authentication.md).

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>Vytvoření nové mapy

Následující kroky ukazují, jak vytvořit statickou stránku HTML s vloženým rozhraním API pro mapové ovládací prvky.

1. Na místním počítači vytvořte nový soubor s názvem **MapRoute.html**.
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

    Objekt `atlas.Map` umožňuje ovládání vizuální a interaktivní webové mapy a je součástí rozhraní API pro mapové ovládací prvky v Azure.

4. Uložte soubor a otevřete ho v prohlížeči. V tuto chvíli máte základní mapu, kterou můžete dále rozvíjet.

   ![Zobrazení základní mapy](media/tutorial-route-location/basic-map.png)

## <a name="define-how-the-route-will-be-rendered"></a>Definice způsobu vykreslení trasy

V tomto kurzu se vykreslí jednoduchá trasa. Pro začátek a konec trasy se použije ikona symbolu a pro cestu trasy se použije čára.

1. Po inicializaci mapy přidejte následující kód JavaScriptu.

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
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
    
    V obslužné rutině události maps `ready` je vytvořen zdroj dat pro uložení řádku trasy a počátečního a koncového bodu. Vytvoří se vrstva čar, která se připojí ke zdroji dat a která definuje, jak se vykreslí čára trasy. Trasa bude vykreslena jako pěkný odstín modré. Bude mít šířku pěti pixelů, zaoblená spojení čar a zakončení. Při přidávání vrstvy do mapy se předá druhý parametr s hodnotou `'labels'`, který určuje, že se má tato vrstva vykreslit pod popisky mapy. Tím se zajistí, že čára trasy nepřekryje popisky silnic. Vytvoří se vrstva symbolů, která se připojí ke zdroji dat. Tato vrstva určuje způsob vykreslení počátečního a koncového bodu. V tomto případě byly přidány výrazy pro načtení informací o obrázku ikony a textovém popisku z vlastností každého objektu bodu. 
    
2. Pro účely tohoto kurzu nastavte jako počáteční bod Microsoft a jako koncový bod čerpací stanici v Seattlu. Do obslužné rutiny události maps `ready` přidejte následující kód.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end points of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.130137, 47.644702]), {
        title: "Redmond",
        icon: "pin-blue"
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.3352, 47.61397]), {
        title: "Seattle",
        icon: "pin-round-blue"
    });

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 80
    });
    ```

    Tento kód vytvoří dva [objekty GeoJSON Point,](https://en.wikipedia.org/wiki/GeoJSON) které představují počáteční a koncový bod trasy, a přidá body do zdroje dat. Ke každému bodu se přidají vlastnosti `title` a `icon`. Poslední blok nastaví pohled kamery pomocí zeměpisné šířky a délky počátečního a koncového bodu pomocí vlastnosti [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) mapy.

3. Uložte soubor **MapRoute.html** a aktualizujte prohlížeč. Nyní je mapa vycentrována nad Seattlem a můžete vidět modrý kolík označující počáteční bod a kulatý modrý kolík označující cílový bod.

   ![Zobrazení počátečního a koncového bodu tras na mapě](media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Získání pokynů

Tato část ukazuje, jak používat rozhraní API služby směrování Azure Maps. Rozhraní API služby trasy vyhledá trasu z daného počátečního bodu do koncového bodu. V rámci této služby existují api pro plánování *nejrychlejších*, *nejkratších*, *ekologických*nebo *vzrušujících* tras mezi dvěma místy. Tato služba také umožňuje uživatelům plánovat trasy v budoucnu pomocí rozsáhlé databáze historických provozu Azure. Uživatelé mohou zobrazit předpověď doby trvání trasy v libovolný den a čas. Další informace najdete v tématu [Získání pokynů k trase](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Všechny následující funkce by měly být přidány **v rámci mapy ready eventListener** zajistit, že se načtou po mapování prostředky jsou připraveny k přístupu.

1. Ve funkci GetMap přidejte do kódu JavaScriptu následující.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   Vytvoří `SubscriptionKeyCredential` `SubscriptionKeyCredentialPolicy` k ověření http požadavky na Azure Maps s klíčem předplatného. Bere `atlas.service.MapsURL.newPipeline()` v `SubscriptionKeyCredential` zásadě a vytvoří [pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) instance. Představuje `routeURL` adresu URL operací trasy Azure Maps [Route.](https://docs.microsoft.com/rest/api/maps/route)

2. Po nastavení přihlašovacích údajů a adresy URL přidejte následující kód Jazyka JavaScript, abyste trasu vytvořili od začátku do konce. Požadavky `routeURL` služby směrování Azure Maps k výpočtu trasy trasy. Kolekce funkcí GeoJSON z odpovědi je pak `geojson.getFeatures()` extrahována pomocí metody a přidána do zdroje dat.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();
        datasource.add(data);
    });
    ```

3. Uložte soubor **MapRoute.html** a aktualizujte webový prohlížeč. V případě úspěšného připojení s použitím rozhraní Maps API by se měla zobrazit mapa podobná následující.

    ![Ovládací prvek Mapa a Route Service v Azure](./media/tutorial-route-location/map-route.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit novou webovou stránku s použitím rozhraní API pro mapové ovládací prvky
> * Nastavit souřadnice pro adresu
> * Zadat dotaz na službu Route Service ohledně trasy k bodu zájmu

> [!div class="nextstepaction"]
> [Zobrazit úplný zdrojový kód](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)

> [!div class="nextstepaction"]
> [Zobrazit živý vzorek](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

V dalším kurzu se dozvíte, jak vytvořit dotaz na trasu s omezeními, jako jsou režim dopravy nebo typ nákladu, a pak na stejné mapě zobrazit více tras.

> [!div class="nextstepaction"]
> [Vyhledání tras pro různé režimy dopravy](./tutorial-prioritized-routes.md)

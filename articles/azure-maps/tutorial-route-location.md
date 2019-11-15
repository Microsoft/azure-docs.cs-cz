---
title: 'Kurz: Vyhledání trasy pomocí Azure Maps'
description: 'Kurz: směrování do bodu zájmu pomocí Azure Maps'
author: walsehgal
ms.author: v-musehg
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: c9e407b271fd77397723ee49f530339847096749
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74106616"
---
# <a name="tutorial-route-to-a-point-of-interest-using-azure-maps"></a>Kurz: směrování do bodu zájmu pomocí Azure Maps

V tomto kurzu se dozvíte, jak pomocí účtu Azure Maps a sady SDK Route Service vyhledat trasu k bodu zájmu. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit novou webovou stránku s použitím rozhraní API pro mapové ovládací prvky
> * Nastavit souřadnice pro adresu
> * Zadání dotazu na rozhraní API Route Service ohledně trasy k bodu zájmu

## <a name="prerequisites"></a>Požadavky

Než budete pokračovat, postupujte podle pokynů v části [Správa účtu](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) a vytvořte předplatné účtu Azure Maps s cenovou úrovní S1 a podle kroků v části [získání primárního klíče](./tutorial-search-location.md#getkey) Získejte primární klíč předplatného pro váš účet.

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

    Objekt `atlas.Map` umožňuje ovládání vizuální a interaktivní webové mapy a je součástí rozhraní API pro mapové ovládací prvky v Azure.

4. Uložte soubor a otevřete ho v prohlížeči. V tuto chvíli máte základní mapu, kterou můžete dále rozvíjet.

   ![Zobrazení základní mapy](media/tutorial-route-location/basic-map.png)

## <a name="define-how-the-route-will-be-rendered"></a>Definice způsobu vykreslení trasy

V tomto kurzu se vykreslí jednoduchá trasa. Pro začátek a konec trasy se použije ikona symbolu a pro cestu trasy se použije čára.

1. Po inicializaci mapy přidejte následující kód jazyka JavaScript.

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
    
    V obslužné rutině události Maps `ready` se vytvoří zdroj dat pro uložení řádku trasy i počátečního a koncového bodu. Vytvoří se vrstva čar, která se připojí ke zdroji dat a která definuje, jak se vykreslí čára trasy. Čára trasy se vykreslí se vykreslí v pěkném odstínu modré s tloušťkou 5 pixelů a zaoblenými spoji a zakončeními. Při přidávání vrstvy do mapy se předá druhý parametr s hodnotou `'labels'`, který určuje, že se má tato vrstva vykreslit pod popisky mapy. Tím se zajistí, že čára trasy nepřekryje popisky silnic. Vytvoří se vrstva symbolů, která se připojí ke zdroji dat. Tato vrstva určuje, jak se vykreslí počáteční a koncový bod. V tomto případě se do ní přidaly výrazy pro načtení informací o obrázku ikony a textovém popisku z vlastností objektů jednotlivých bodů. 
    
2. Pro účely tohoto kurzu nastavte jako počáteční bod Microsoft a jako koncový bod čerpací stanici v Seattlu. V obslužné rutině události Maps `ready` přidejte následující kód.

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

    Tento kód vytvoří dva [objekty Inčárkového bodu JSON](https://en.wikipedia.org/wiki/GeoJSON) pro reprezentaci počátečního a koncového bodu trasy a přidá body do objektu DataSource. Ke každému bodu se přidají vlastnosti `title` a `icon`. Poslední blok nastaví zobrazení kamery s použitím informací o zeměpisné šířce a délce počátečního a koncového bodu pomocí vlastnosti [SetCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) mapy.

3. Uložte soubor **MapRoute.html** a aktualizujte prohlížeč. Mapa se teď nacentruje na střed Seattle a vidíte modrý kód PIN, který označuje počáteční bod a kulatě modrý kód PIN označení bodu dokončení.

   ![Zobrazení mapy s označeným počátečním a koncovým bodem](media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Získání pokynů

V této části se dozvíte, jak pomocí rozhraní API služby Azure Maps Route vyhledat trasu z daného počátečního bodu do koncového bodu. Route Service poskytuje rozhraní API pro plánování *nejrychlejší*, *nejkratší*, *úsporné* nebo *vzrušující* trasy mezi dvěma místy. Umožňuje uživatelům také plánovat trasy v budoucnu s použitím rozsáhlé databáze Azure s historickými dopravními informacemi a předvídat dobu trvání trasy pro kterýkoli den a čas. Další informace najdete v tématu [Získání pokynů k trase](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Všechny následující funkce by měly být přidány **v rámci mapy připravené naslouchacího procesu událostí** , aby bylo zajištěno, že budou načteny po přípravě prostředků mapy k dispozici.

1. Do funkce GetMap přidejte následující kód JavaScript Code.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential` vytvoří `SubscriptionKeyCredentialPolicy` k ověření požadavků HTTP na Azure Maps pomocí klíče předplatného. `atlas.service.MapsURL.newPipeline()` přebírá v zásadách `SubscriptionKeyCredential` a vytvoří instanci [kanálu](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) . `routeURL` představuje adresu URL pro Azure Maps operace [Směrování](https://docs.microsoft.com/rest/api/maps/route) .

2. Po nastavení přihlašovacích údajů a adresy URL přidejte následující kód JavaScriptu, který vytvoří trasu od začátku do koncového bodu. `routeURL` žádá službu Směrování Azure Maps, aby vypočítala trasy směrování. Kolekce funkcí pro injson z odpovědi je pak extrahována pomocí metody `geojson.getFeatures()` a přidána do zdroje dat.

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
> [Zobrazit ukázku živého vysílání](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

V dalším kurzu se dozvíte, jak vytvořit dotaz na trasu s omezeními, jako jsou režim dopravy nebo typ nákladu, a pak na stejné mapě zobrazit více tras.

> [!div class="nextstepaction"]
> [Vyhledání tras pro různé režimy dopravy](./tutorial-prioritized-routes.md)

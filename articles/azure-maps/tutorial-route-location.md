---
title: Vyhledání trasy s využitím Azure Maps | Microsoft Docs
description: Trasa k bodu zájmu s využitím Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 11/14/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 7fb382c6c57972484eff80c0d7355319a1f9178a
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55893547"
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
    <html>
    <head>
        <title>Map Route</title>
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

            #map {
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

    Objekt `atlas.Map` umožňuje ovládání vizuální a interaktivní webové mapy a je součástí rozhraní API pro mapové ovládací prvky v Azure.

4. Uložte soubor a otevřete ho v prohlížeči. V tuto chvíli máte základní mapu, kterou můžete dále rozvíjet.

   ![Zobrazení základní mapy](./media/tutorial-route-location/basic-map.png)

## <a name="define-how-the-route-will-be-rendered"></a>Definice způsobu vykreslení trasy

V tomto kurzu se vykreslí jednoduchá trasa. Pro začátek a konec trasy se použije ikona symbolu a pro cestu trasy se použije čára.

1. Do funkce GetMap za inicializaci mapy přidejte následující kód JavaScriptu.

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('load', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
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
    
    Do mapy se přidá událost load, která se aktivuje po plném načtení prostředků mapy. V obslužné rutině události načtení mapy se vytvoří zdroj dat, do kterého se uloží čára trasy a také počáteční a koncový bod trasy. Vytvoří se vrstva čar, která se připojí ke zdroji dat a která definuje, jak se vykreslí čára trasy. Čára trasy se vykreslí se vykreslí v pěkném odstínu modré s tloušťkou 5 pixelů a zaoblenými spoji a zakončeními. Přidá se filtr, který zajistí, že se na této vrstvě vykreslí pouze data typu GeoJSON LineString. Při přidávání vrstvy do mapy se předá druhý parametr s hodnotou `'labels'`, který určuje, že se má tato vrstva vykreslit pod popisky mapy. Tím se zajistí, že čára trasy nepřekryje popisky silnic. Vytvoří se vrstva symbolů, která se připojí ke zdroji dat. Tato vrstva určuje, jak se vykreslí počáteční a koncový bod. V tomto případě se do ní přidaly výrazy pro načtení informací o obrázku ikony a textovém popisku z vlastností objektů jednotlivých bodů. 
    
2. Pro účely tohoto kurzu nastavte jako počáteční bod Microsoft a jako koncový bod čerpací stanici v Seattlu. Do obslužné rutiny události načtení mapy přidejte následující kód.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.130137, 47.644702]), {
        title: 'Microsoft',
        icon: 'pin-round-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.3352, 47.61397]), {
        title: 'Contoso Oil & Gas',
        icon: 'pin-blue'
    });    
    ```

    Tento kód vytvoří dva [objekty GeoJSON Point](https://en.wikipedia.org/wiki/GeoJSON) představující počáteční a koncový bod trasy. Ke každému bodu se přidají vlastnosti `title` a `icon`.
    
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
    
    Počáteční a koncový bod se přidají ke zdroji dat. Ohraničující rámeček pro počáteční a koncový bod se vypočítá pomocí funkce `atlas.data.BoundingBox.fromData`. Tento ohraničující rámeček se používá k nastavení zobrazení počátečního a koncového bodu pro kameru mapy pomocí funkce **map.setCamera**. Přidá se odsazení, které kompenzuje rozměry ikon symbolů v pixelech.

3. Uložte soubor **MapRoute.html** a aktualizujte prohlížeč. Teď se ve středu mapy zobrazí Seattle a můžete si všimnou modrých špendlíků, které označují počáteční a koncový bod.

   ![Zobrazení mapy s označeným počátečním a koncovým bodem](./media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Získání pokynů

Tato část ukazuje způsob použití rozhraní API Maps Route Service k vyhledání trasy z daného počátečního bodu do cíle. Route Service poskytuje rozhraní API pro plánování *nejrychlejší*, *nejkratší*, *úsporné* nebo *vzrušující* trasy mezi dvěma místy. Umožňuje uživatelům také plánovat trasy v budoucnu s použitím rozsáhlé databáze Azure s historickými dopravními informacemi a předvídat dobu trvání trasy pro kterýkoli den a čas. Další informace najdete v tématu [Získání pokynů k trase](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Všechny následující funkce by se měly přidat **do modulu eventListener pro načtení mapy**, aby se zajistilo jejich načtení po plném načtení mapy.

1. Přidáním následujícího kódu JavaScriptu do obslužné rutiny události načtení mapy vytvořte instanci klienta služby.

    ```JavaScript
    //If the service client hasn't already been created, create an instance.
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

3. Abyste získali trasu, přidejte do skriptu následující blok kódu. Dotazuje se směrovací služby Azure Maps prostřednictvím metody [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest) a potom analyzuje odpověď do formátu GeoJSON pomocí [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest). Potom přidá čáru trasy v odpovědi ke zdroji dat, který ji automaticky vykreslí na mapě.

    ```JavaScript
    //Execute the car route query then add the route to the map once a response is received.
    client.route.getRouteDirections(routeQuery).then(function (response) {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

        //Add the route line to the data source.
        datasource.add(geoJsonResponse.getGeoJsonRoutes().features[0]);
    });
    ```

5. Uložte soubor **MapRoute.html** a aktualizujte webový prohlížeč. V případě úspěšného připojení s použitím rozhraní Maps API by se měla zobrazit mapa podobná následující.

    ![Ovládací prvek Mapa a Route Service v Azure](./media/tutorial-route-location/map-route.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření nové webové stránky s použitím rozhraní API pro mapové ovládací prvky
> * Nastavit souřadnice pro adresu
> * Zadat dotaz na službu Route Service ohledně trasy k bodu zájmu

Přístup k vzorovému kódu pro tento kurz můžete získat tady:

> [Vyhledání trasy pomocí Azure Maps](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)

[Podívejte se na živou ukázku](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

V dalším kurzu se dozvíte, jak vytvořit dotaz na trasu s omezeními, jako jsou režim dopravy nebo typ nákladu, a pak na stejné mapě zobrazit více tras.

> [!div class="nextstepaction"]
> [Vyhledání tras pro různé režimy dopravy](./tutorial-prioritized-routes.md)

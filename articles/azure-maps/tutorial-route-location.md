---
title: 'Kurz: Vyhledání trasy do umístění | Mapy Microsoft Azure'
description: Kurz o tom, jak najít trasu k bodu zájmu. Přečtěte si, jak nastavit souřadnice adres a dotazovat se na službu Azure Maps Route, kde najdete pokyny k bodu.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: d8aaf093a7e2a0ad1470d72d815e919f5b4e2d05
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624979"
---
# <a name="tutorial-how-to-display-route-directions-using-azure-maps-route-service-and-map-control"></a>Kurz: jak zobrazit směry tras pomocí služby Azure Maps Route Service a mapového ovládacího prvku

V tomto kurzu se dozvíte, jak pomocí [rozhraní Azure Maps Route Service API](/rest/api/maps/route) a [mapového ovládacího prvku](./how-to-use-map-control.md) zobrazit směr směrování z počátečního na koncový bod. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření a zobrazení mapového ovládacího prvku na webové stránce. 
> * Definujte vykreslení zobrazení trasy definováním [vrstev symbolů](map-add-pin.md) a [vrstev čáry](map-add-line-layer.md).
> * Vytvořte a přidejte objekty injson do mapy tak, aby představovaly počáteční a koncové body.
> * Získejte směr směrování z počátečního a koncového bodu pomocí [rozhraní API pro získání tras](/rest/api/maps/route/getroutedirections).

Úplný zdrojový kód pro ukázku můžete získat [zde](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html). Ukázku najdete [tady](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination).

## <a name="prerequisites"></a>Požadavky

1. [Vytvořit účet Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Získejte primární klíč předplatného](quick-demo-map-app.md#get-the-primary-key-for-your-account), označovaný také jako primární klíč nebo klíč předplatného.

<a id="getcoordinates"></a>

## <a name="create-and-display-the-map-control"></a>Vytvoření a zobrazení mapového ovládacího prvku

Následující kroky ukazují, jak vytvořit a zobrazit mapový ovládací prvek na webové stránce.

1. Na místním počítači vytvořte nový soubor s názvem **MapRoute.html**.
2. Zkopírujte nebo vložte následující kód HTML do souboru.

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

    Hlavička HTML obsahuje soubory prostředků CSS a JavaScript hostované knihovnou Azure Ovládací prvek Mapa. `onload`Událost těla volá `GetMap` funkci. V dalším kroku přidáme inicializační kód mapového ovládacího prvku.

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

4. Uložte soubor a otevřete ho v prohlížeči. Zobrazí se jednoduchá.

     :::image type="content" source="./media/tutorial-route-location/basic-map.png" alt-text="Základní vykreslování mapy mapového ovládacího prvku":::

## <a name="define-route-display-rendering"></a>Definovat vykreslování zobrazení tras

V tomto kurzu vykreslíme trasu pomocí čárové vrstvy. Počáteční a koncové body budou vykresleny pomocí vrstvy symbolů. Další informace o přidávání vrstev čar naleznete v tématu [Přidání čáry čáry do mapy](map-add-line-layer.md). Další informace o vrstvách symbolů naleznete v tématu [Přidání vrstvy symbolů na mapu](map-add-pin.md).

1. Do funkce přidejte následující kód JavaScriptu `GetMap` . Tento kód implementuje `ready` obslužnou rutinu události mapového ovládacího prvku. Zbytek kódu v tomto kurzu bude umístěn uvnitř `ready` obslužné rutiny události.

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

    V `ready` obslužné rutině události mapového ovládacího prvku je vytvořen zdroj dat pro uložení trasy ze začátku do koncového bodu. Chcete-li definovat způsob, jakým se má řádek trasy vykreslovat, vytvoří se čára a připojí se ke zdroji dat.  Aby se zajistilo, že řádek trasy nepokrývá popisky cest, předali jsme druhý parametr s hodnotou `'labels'` .

    Dále je vytvořena vrstva symbolu a připojena ke zdroji dat. Tato vrstva určuje způsob vykreslování počátečních a koncových bodů. Přidaly se výrazy pro načtení obrázku ikony a informací o popisku textu z vlastností u každého objektu Point. Další informace o výrazech naleznete v tématu [výrazy stylu na základě dat](data-driven-style-expressions-web-sdk.md).

2. Nastavte počáteční bod jako Microsoft a koncový bod jako čerpací stanice v Seattlu.  V `ready` obslužné rutině události mapového ovládacího prvku přidejte následující kód.

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

    Tento kód vytvoří dva [objekty typu bodový kód](https://en.wikipedia.org/wiki/GeoJSON) pro zobrazení dat, které představují počáteční a koncové body, které jsou poté přidány do zdroje dat. 

    Poslední blok kódu nastaví zobrazení kamery pomocí zeměpisné šířky a délky počátečního a koncového bodu. Počáteční a koncový bod se přidají ke zdroji dat. Ohraničující rámeček pro počáteční a koncový bod se vypočítá pomocí funkce `atlas.data.BoundingBox.fromData`. Toto ohraničovací pole slouží k nastavení zobrazení mapy kamer přes celou trasu pomocí `map.setCamera` funkce. Je přidáno odsazení, které umožňuje kompenzovat rozměry ikon symbolů v pixelech. Další informace o vlastnosti setCamera mapového ovládacího prvku naleznete v tématu [setCamera (CameraOptions | Vlastnost CameraBoundsOptions & AnimationOptions)](/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-&preserve-view=false) .

3. Uložte **MapRoute.html** a aktualizujte prohlížeč. Mapa se teď nacentruje na střed Seattle. Modrý kód pro 15kolíkový označí počáteční bod. Kulatě modrý kód PIN označuje koncový bod.

    :::image type="content" source="./media/tutorial-route-location/map-pins.png" alt-text="Zobrazení počátečního a koncového bodu na mapě":::

<a id="getroute"></a>

## <a name="get-route-directions"></a>Získat směr směrování

V této části se dozvíte, jak používat rozhraní API pro Azure Maps trasy k získání směrných tras a odhadované doby doručení od jednoho bodu k druhému.

>[!TIP]
>Služba Azure Maps Route Services nabízí rozhraní API k plánování tras na základě různých typů tras, jako jsou *nejrychlejší*, nejkratší *, kandidát* nebo *Thrilling* trasy založené na vzdálenosti, přenosných podmínkách a způsobu využití přenosu.  Tato služba také umožňuje uživatelům plánovat budoucí trasy na základě historických přenosových podmínek. Uživatelé mohou zobrazit předpovědi trvání tras pro určitou dobu. Další informace najdete v tématu [získání rozhraní API pro směr směrování](/rest/api/maps/route/getroutedirections).

1. Do `GetMap` funkce uvnitř `ready` obslužné rutiny události ovládacího prvku přidejte následující kód jazyka JavaScript.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential`Vytvoří a `SubscriptionKeyCredentialPolicy` OVĚŘÍ požadavky HTTP, které se mají Azure Maps pomocí klíče předplatného. `atlas.service.MapsURL.newPipeline()`Zásada převezme `SubscriptionKeyCredential` a vytvoří instanci [kanálu](/javascript/api/azure-maps-rest/atlas.service.pipeline) . `routeURL`Představuje adresu URL pro Azure Maps operací [Směrování](/rest/api/maps/route) .

2. Po nastavení přihlašovacích údajů a adresy URL přidejte následující kód do `ready` obslužné rutiny události ovládacího prvku. Tento kód vytvoří trasu od počátečního bodu do koncového bodu. `routeURL`Vyžádá rozhraní API služby Azure Maps Route k výpočtu směrů tras. Kolekce funkcí pro injson z odpovědi je pak extrahována pomocí `geojson.getFeatures()` metody a přidána do zdroje dat.

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

3. Uložte soubor **MapRoute.html** a aktualizujte webový prohlížeč. Mapa by nyní měla zobrazovat trasu od začátku do koncového bodu.

     :::image type="content" source="./media/tutorial-route-location/map-route.png" alt-text="Služba Směrování a řízení mapy Azure":::

Úplný zdrojový kód pro ukázku můžete získat [zde](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html). Ukázku najdete [tady](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nejsou k dispozici žádné prostředky, které vyžadují vyčištění.

## <a name="next-steps"></a>Další kroky

V dalším kurzu se dozvíte, jak vytvořit dotaz směrování s omezeními, jako je například režim dopravy nebo typ nákladu. Potom můžete zobrazit více tras na stejné mapě.

> [!div class="nextstepaction"]
> [Vyhledání tras pro různé režimy dopravy](./tutorial-prioritized-routes.md)
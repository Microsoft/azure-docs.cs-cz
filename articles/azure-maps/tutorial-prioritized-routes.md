---
title: 'Kurz: vyhledání více tras podle režimu cestování | Mapy Microsoft Azure'
description: Kurz týkající se použití Azure Maps k nalezení tras pro konkrétní cestovní režimy až po body zájmu. Podívejte se, jak zobrazit více tras na mapách.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 0d57e86088ee472c63b433bde14a0e4316cc20a5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91321743"
---
# <a name="tutorial-find-and-display-routes-for-different-modes-of-travel-using-azure-maps"></a>Kurz: vyhledání a zobrazení tras pro různé režimy cestování pomocí Azure Maps

V tomto kurzu se dozvíte, jak pomocí [služby Azure Maps Route Service](https://docs.microsoft.com/rest/api/maps/route) a [mapového ovládacího prvku](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control) zobrazit směr směrování jak u privátních vozidel, tak z komerčních vozidel (nákladní automobily) `USHazmatClass2` typu nákladu. Kromě toho vás provedeme tak, jak vizualizovat data o přenosech dat v reálném čase na mapě. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření a zobrazení mapového ovládacího prvku na webové stránce
> * Vykreslování dat o přenosech dat v reálném čase na mapě
> * Vyžádat a zobrazit trasy privátních a komerčních vozidel na mapě

## <a name="prerequisites"></a>Požadavky

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. [Vytvořte účet Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account).

3. [Získejte primární klíč předplatného](quick-demo-map-app.md#get-the-primary-key-for-your-account), označovaný také jako primární klíč nebo klíč předplatného. Další informace o ověřování v Azure Maps najdete v tématu [Správa ověřování v Azure Maps](how-to-manage-authentication.md).

Úplný zdrojový kód pro ukázku můžete získat [zde](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html). Ukázku najdete [tady](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel).

## <a name="create-a-new-web-page-using-the-map-control-api"></a>Vytvořit novou webovou stránku s použitím rozhraní API pro mapové ovládací prvky

Následující kroky ukazují, jak vytvořit a zobrazit mapový ovládací prvek na webové stránce.

1. Na místním počítači vytvořte nový soubor s názvem **MapTruckRoute.html**.
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

    :::image type="content" source="./media/tutorial-prioritized-routes/basic-map.png" alt-text="Základní vykreslování mapy mapového ovládacího prvku":::

## <a name="render-real-time-traffic-data-on-a-map"></a>Vykreslování dat o přenosech dat v reálném čase na mapě

1. Do funkce přidejte následující kód JavaScriptu `GetMap` . Tento kód implementuje `ready` obslužnou rutinu události mapového ovládacího prvku. Zbytek kódu v tomto kurzu bude umístěn uvnitř `ready` obslužné rutiny události.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    V `ready` obslužné rutině události mapy je nastavení toku přenosu na mapě nastaveno na `relative` , což je rychlost provozu vzhledem k volnému toku. Další možnosti přenosů naleznete v tématu [TrafficOptions Interface](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions?view=azure-maps-typescript-latest&preserve-view=false).

2. Uložte soubor **MapTruckRoute.html** a aktualizujte stránku v prohlížeči. Pokud se přiblížíte k libovolnému městu, třeba k Los Angeles, uvidíte, že se ulic zobrazuje s aktuálními daty toku provozu.

    :::image type="content" source="./media/tutorial-prioritized-routes/traffic-map.png" alt-text="Zobrazení provozu na mapě":::

<a id="queryroutes"></a>

## <a name="define-route-display-rendering"></a>Definovat vykreslování zobrazení tras

V tomto kurzu se vypočítají dvě trasy, které se vykreslí na mapě. První trasa se vypočítá pro privátní vozidlo (automobil). Druhá trasa se vypočítá pro komerční vozidlo (nákladní vůz), aby se zobrazil rozdíl mezi výsledky. Při vykreslení bude mapa zobrazovat ikonu symbolu pro počáteční a koncový bod trasy a spojnici geometrií čáry s různými barvami pro každou cestu trasy. Další informace o přidávání vrstev čar naleznete v tématu [Přidání čáry čáry do mapy](map-add-line-layer.md). Další informace o vrstvách symbolů naleznete v tématu [Přidání vrstvy symbolů na mapu](map-add-pin.md).

1. V `ready` obslužné rutině události mapového ovládacího prvku přidejte následující kód.

    ```JavaScript

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

    ```


    V `ready` obslužné rutině události mapového ovládacího prvku je vytvořen zdroj dat pro uložení trasy od začátku do konce. [Výrazy](data-driven-style-expressions-web-sdk.md) se používají k načtení šířky čáry a barvy z vlastností funkce řádek trasy. Aby se zajistilo, že řádek trasy nepokrývá popisky cest, předali jsme druhý parametr s hodnotou `'labels'` .

    Dále je vytvořena vrstva symbolu a připojena ke zdroji dat. Tato vrstva určuje způsob vykreslování počátečních a koncových bodů. Přidaly se výrazy pro načtení obrázku ikony a informací o popisku textu z vlastností u každého objektu Point. Další informace o výrazech naleznete v tématu [výrazy stylu na základě dat](data-driven-style-expressions-web-sdk.md).

2. Nastavte počáteční bod jako fiktivní společnost v Seattlu označované jako Fabrikam a koncový bod jako Microsoft Office.  V `ready` obslužné rutině události mapového ovládacího prvku přidejte následující kód.


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

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });

    ```

    Tento kód vytvoří dva [objekty typu bodový kód](https://en.wikipedia.org/wiki/GeoJSON) pro zobrazení dat, které představují počáteční a koncové body, které jsou poté přidány do zdroje dat.

    Poslední blok kódu nastaví zobrazení kamery pomocí zeměpisné šířky a délky počátečního a koncového bodu. Počáteční a koncový bod se přidají ke zdroji dat. Ohraničující rámeček pro počáteční a koncový bod se vypočítá pomocí funkce `atlas.data.BoundingBox.fromData`. Toto ohraničovací pole slouží k nastavení zobrazení mapy kamer přes celou trasu pomocí `map.setCamera` funkce. Je přidáno odsazení, které umožňuje kompenzovat rozměry ikon symbolů v pixelech. Další informace o vlastnosti setCamera mapového ovládacího prvku naleznete v tématu [setCamera (CameraOptions | Vlastnost CameraBoundsOptions & AnimationOptions)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-&preserve-view=false) .

3. Uložte **TruckRoute.html** a aktualizujte prohlížeč. Mapa se teď nacentruje na střed Seattle. Modrý kód pro 15kolíkový označí počáteční bod. Kulatě modrý kód PIN označuje koncový bod.

   :::image type="content" source="./media/tutorial-prioritized-routes/pins-map.png" alt-text="Zobrazení mapy s počátečním a koncovým bodem":::

<a id="multipleroutes"></a>

## <a name="request-and-display-private-and-commercial-vehicle-routes-on-a-map"></a>Vyžádat a zobrazit trasy privátních a komerčních vozidel na mapě

V této části se dozvíte, jak používat službu Azure Maps Route k získání pokynů od jednoho bodu k druhému v závislosti na vašem režimu přenosu. Budeme používat dva způsoby přenosu: nákladní auto a auto.

>[!TIP]
>Služba Směrování poskytuje rozhraní API k plánování *nejrychlejší*, *nejkratší*, *ekonomické*nebo *Thrilling* trasy na základě vzdálenosti, přenosových podmínek a použitého přenosu. Tato služba také umožňuje uživatelům plánovat budoucí trasy na základě historických přenosových podmínek. Uživatelé mohou zobrazit předpovědi trvání tras pro určitou dobu. Další informace najdete v tématu [získání rozhraní API pro směr směrování](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).

1. Do `GetMap` funkce uvnitř `ready` obslužné rutiny události ovládacího prvku přidejte následující kód jazyka JavaScript.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential`Vytvoří a `SubscriptionKeyCredentialPolicy` OVĚŘÍ požadavky HTTP, které se mají Azure Maps pomocí klíče předplatného. `atlas.service.MapsURL.newPipeline()`Zásada převezme `SubscriptionKeyCredential` a vytvoří instanci [kanálu](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline) . `routeURL`Představuje adresu URL pro Azure Maps operací [Směrování](https://docs.microsoft.com/rest/api/maps/route) .

2. Po nastavení přihlašovacích údajů a adresy URL přidejte následující kód JavaScriptu, který vytvoří trasu trasy vozíku od začátku do koncového bodu. Tato trasa se vytvoří a zobrazí pro nákladní automobil, který přepravuje náklad na vydanou `USHazmatClass2` třídu.

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

    Výše uvedený kód se dotazuje služby směrování Azure Maps prostřednictvím [rozhraní API pro Azure Maps trasy tras](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl#calculateroutedirections-aborter--geojson-position----calculateroutedirectionsoptions-). Z odpovědi, která je extrahována pomocí metody, je následně extrahována z kolekce funkcí injson `geojson.getFeatures()` . Nakonec se přidá řádek trasy do zdroje dat. Přidáváme ho do indexu 0, aby se zajistilo, že se trasa na nákladní vozík vykreslí před všemi ostatními řádky ve zdroji dat, protože výpočet trasy vozíku bude často pomalejší než výpočet trasy auta. Pokud je čára postupu nákladní jednotky přidána do zdroje dat po trase auta, vykreslí se nad ní. Do řádku trasy vozíku se přidají dvě vlastnosti: modrou barvu tahu a šířku tahu devět pixelů.

    >[!TIP]
    > Pokud chcete zobrazit všechny možné možnosti a hodnoty rozhraní API pro Azure Maps tras, přečtěte si téma [parametry identifikátoru URI pro směr směrování post](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#uri-parameters).

3. Nyní k vytvoření trasy pro automobil přidejte následující kód JavaScriptu.

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. This will add the car route after the truck route.  
        datasource.add(routeLine);
    });
    ```

    Výše uvedený kód provede dotazování směrovací služby Azure Maps pomocí metody  [rozhraní API pro Azure Maps trasy](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl#calculateroutedirections-aborter--geojson-position----calculateroutedirectionsoptions-) . Z odpovědi, která je extrahována pomocí metody, je následně extrahována z kolekce funkcí injson `geojson.getFeatures()` . Nakonec se přidá řádek trasy do zdroje dat. Do řádku trasy vozíku jsou přidány dvě vlastnosti: fialová barva tahu a šířka tahu na pět pixelů.

4. Uložte soubor **TruckRoute.html** a aktualizujte webový prohlížeč. Mapa by nyní měla zobrazovat trasy nákladní automobil a auto.

    :::image type="content" source="./media/tutorial-prioritized-routes/prioritized-routes.png" alt-text="Směrování privátních a komerčních vozidel na mapě s využitím Azure Route Service":::

    Trasa za nákladní vůz se zobrazuje pomocí tlusté modré čáry. Trasa auta se zobrazí s použitím tenké fialové čáry. Trasa automobilu prochází přes Lake Washington prostřednictvím I-90 a prochází tunely pod místními oblastmi. Vzhledem k tomu, že tunely jsou blízko domácích oblastí, je zakázaný nebezpečný odpadní náklad. Směr nákladní lodě, který určuje `USHazmatClass2` typ nákladu, je směrován na použití jiné dálnice.

Úplný zdrojový kód pro ukázku můžete získat [zde](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html). Ukázku najdete [tady](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel).

Můžete také [použít výrazy stylu řízené daty](data-driven-style-expressions-web-sdk.md) .



## <a name="next-steps"></a>Další kroky

V dalším kurzu se dozvíte, jak vytvořit jednoduchý Lokátor úložiště pomocí Azure Maps.

> [!div class="nextstepaction"]
> [Vytvoření lokátoru úložiště pomocí Azure Maps](./tutorial-create-store-locator.md)
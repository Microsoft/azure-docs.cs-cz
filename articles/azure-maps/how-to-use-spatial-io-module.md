---
title: Jak používat modul Azure Maps prostorového vstupu/výstupu | Mapy Microsoft Azure
description: Naučte se používat modul pro prostorové vstupně-výstupní operace poskytovaný Azure Maps Web SDK. Tento modul poskytuje robustní funkce usnadňující vývojářům integrovat prostorová data pomocí Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/28/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: cd64c80acceb1542c080fc45efbce59f287d448a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97680682"
---
# <a name="how-to-use-the-azure-maps-spatial-io-module"></a>Jak používat modul Azure Maps prostorového vstupu/výstupu

Sada Azure Maps Web SDK poskytuje **modul pro prostorové vstupně-výstupní operace**, který integruje prostorová data do Azure Maps Web SDK pomocí JavaScriptu nebo TypeScript. Robustní funkce v tomto modulu umožňují vývojářům:

- [Čtení a zápis běžných souborů prostorových dat](spatial-io-read-write-spatial-data.md). Mezi podporované formáty souborů patří: KML, KMZ, GPX, GeoRSS, GML, soubory. JSON a CSV, které obsahují sloupce s prostorovými informacemi. Podporuje také Well-Known text (Well).
- [Připojte se ke službám Open Geospatial Consortium (OGC) a integrujte je se sadou Azure Maps Web SDK. Překrývá služby webového mapování (WMS) a WMTS (Web Map) jako vrstvy na mapě](spatial-io-add-ogc-map-layer.md).
- [Dotazování dat ve službě webové funkce (WFS)](spatial-io-connect-wfs-service.md).
- [Překrytí složitých datových sad, které obsahují informace o stylu a jejich automatické vykreslování pomocí minimálního kódu](spatial-io-add-simple-data-layer.md).
- [Využijte vysokorychlostní XML a oddělených tříd pro čtení a zápis souborů](spatial-io-core-operations.md).

V této příručce se dozvíte, jak integrovat a používat modul pro prostorové vstupně-výstupní operace ve webové aplikaci.

Toto video poskytuje přehled modulu pro prostorové vstupně-výstupní operace v sadě Azure Maps Web SDK.

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Easily-integrate-spatial-data-into-the-Azure-Maps/player?format=ny]

> [!WARNING]
> Používejte jenom data a služby, které jsou ze zdroje, kterému důvěřujete, zejména v případě, že se na něj odkazuje z jiné domény. Modul pro prostorové vstupně-výstupní operace provede kroky pro minimalizaci rizika, ale nejbezpečnější přístup je příliš, takže danagerous data do vaší aplikace neumožňují začít. 

## <a name="prerequisites"></a>Předpoklady

Než budete moct použít modul pro vytváření prostorových dat, budete muset [vytvořit účet Azure Maps](./quick-demo-map-app.md#create-an-azure-maps-account) a [získat pro svůj účet primární klíč předplatného](./quick-demo-map-app.md#get-the-primary-key-for-your-account).

## <a name="installing-the-spatial-io-module"></a>Instalace modulu pro prostorové vstupně-výstupní operace

Modul Azure Maps prostorového vstupu a výstupu můžete načíst pomocí jedné ze dvou možností:

* Globálně hostovaný Azure CDN pro modul Azure Maps prostorového vstupu/výstupu. Pro tuto možnost přidáte odkaz na JavaScript v `<head>` prvku souboru HTML.

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

* Zdrojový kód pro [Azure-Maps-](https://www.npmjs.com/package/azure-maps-spatial-io) v místě – v/v lze načíst místně a pak hostovat s vaší aplikací. Tento balíček obsahuje také definice TypeScript. Pro tuto možnost použijte následující příkaz k instalaci balíčku:

    ```sh
    npm install azure-maps-spatial-io
    ```

    Pak přidejte odkaz na JavaScript v `<head>` prvku dokumentu HTML:

    ```html
    <script src="node_modules/azure-maps-spatial-io/dist/atlas-spatial.min.js"></script>
    ```

## <a name="using-the-spatial-io-module"></a>Použití modulu pro prostorové vstupně-výstupní operace

1. Vytvořte nový soubor HTML.

2. Načtěte Azure Maps Web SDK a inicializujte mapový ovládací prvek. Podrobnosti najdete v příručce k [ovládacímu prvku mapa Azure Maps](./how-to-use-map-control.md) . Až budete s tímto krokem hotovi, váš soubor HTML by měl vypadat takto:

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <script type='text/javascript'>

            var map;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    // Write your code here to make sure it runs once the map resources are ready

                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

2. Načtěte modul Azure Maps prostorového vstupu. Pro toto cvičení použijte CDN pro modul Azure Maps prostor v/v. Níže přidejte odkaz na `<head>` element souboru HTML:

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

3. Inicializujte `datasource` a přidejte zdroj dat do mapy. Inicializujte `layer` a přidejte zdroj dat do vrstvy mapy. Potom vykreslete zdroj dat i vrstvu. Než se posuňte dolů k zobrazení úplného kódu v dalším kroku, zamyslete se nad nejlepšími místy pro vložení zdrojů dat a fragmentů kódu vrstvy. Navrácení: před tím, než programově spolupracujeme s mapou, doporučujeme počkat, až bude prostředek mapy připravený.

    ```javascript
    var datasource, layer;
    ```

    a

    ```javascript
    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);
    
    //Add a simple data layer for rendering the data.
    layer = new atlas.layer.SimpleDataLayer(datasource);
    map.layers.add(layer);
    ```

4. Pokaždé dohromady, váš kód HTML by měl vypadat jako následující kód. Tato ukázka demonstruje, jak číst soubor XML z adresy URL. Pak načtěte a zobrazte data funkce souboru na mapě. 

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title>Spatial IO Module Example</title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <!-- Add reference to the Azure Maps Spatial IO module. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

        <script type='text/javascript'>
            var map, datasource, layer;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Add a simple data layer for rendering the data.
                    layer = new atlas.layer.SimpleDataLayer(datasource);
                    map.layers.add(layer);

                    //Read an XML file from a URL or pass in a raw XML string.
                    atlas.io.read('superCoolKmlFile.xml').then(r => {
                        if (r) {
                            //Add the feature data to the data source.
                            datasource.add(r);

                            //If bounding box information is known for data, set the map view to it.
                            if (r.bbox) {
                                map.setCamera({
                                    bounds: r.bbox,
                                    padding: 50
                                });
                            }
                        }
                    });
                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

5. Nezapomeňte nahradit `<Your Azure Maps Key>` primární klíč. Otevřete soubor HTML a zobrazí se podobné výsledky jako na následujícím obrázku:

    <center>

    ![Příklad prostorových dat](./media/how-to-use-spatial-io-module/spatial-data-example.png)

    </center>

## <a name="next-steps"></a>Další kroky

Funkce, kterou jsme tady ukázali, je jenom jedna z mnoha funkcí dostupných v modulu pro prostorové vstupně-výstupní operace. Přečtěte si následující příručky, kde se dozvíte, jak používat další funkce v modulu prostorového vstupu/výstupu:

> [!div class="nextstepaction"]
> [Přidání jednoduché datové vrstvy](spatial-io-add-simple-data-layer.md)

> [!div class="nextstepaction"]
> [Čtení a zápis prostorových dat](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy mapy OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Připojení ke službě WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Využití základních operací](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Podrobnosti o podporovaných formátech dat](spatial-io-supported-data-format-details.md)

Přečtěte si dokumentaci Azure Maps prostor v/v:

> [!div class="nextstepaction"]
> [Balení Azure Maps prostorového vstupu/výstupu](/javascript/api/azure-maps-spatial-io/)
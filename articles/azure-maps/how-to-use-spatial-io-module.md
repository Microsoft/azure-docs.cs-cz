---
title: Jak používat prostorový io modul Azure Maps | Mapy Microsoft Azure
description: Přečtěte si, jak používat modul Prostorové IO poskytované webovou sadou Azure Maps SDK. Tento modul poskytuje robustní funkce, které vývojářům usnadňují integraci prostorových dat s webovou sadou Azure Maps.
author: philmea
ms.author: philmea
ms.date: 02/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 1de9dd9721700418b1aa9ba661fc070db1dbedcc
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804635"
---
# <a name="how-to-use-the-azure-maps-spatial-io-module"></a>Jak používat modul Prostorové IO Azure Maps

Webová sada Azure Maps Web SDK poskytuje **modul Prostorové IO**, který integruje prostorová data s webovou sadou Azure Maps s sadou JavaScript nebo TypeScript. Robustní funkce v tomto modulu umožňují vývojářům:

- [Čtení a zápis běžných souborů prostorových dat](spatial-io-read-write-spatial-data.md). Mezi podporované formáty souborů patří: KML, KMZ, GPX, GeoRSS, GML, GeoJSON a CSV soubory obsahující sloupce s prostorovými informacemi. Podporuje také známý text (WKT).
- [Připojte se ke službám Open Geospatial Consortium (OGC) a integrujte je s webovou sadou Azure Maps SDK. Překrytí služby webové mapy (WMS) a webové mapy dlaždice služby (WMTS) jako vrstvy na mapě](spatial-io-add-ogc-map-layer.md).
- [Dotaz na data ve službě webových funkcí (WFS)](spatial-io-connect-wfs-service.md).
- [Překrytí složitých datových sad, které obsahují informace o stylu a mají je vykreslování automaticky pomocí minimálníkód](spatial-io-add-simple-data-layer.md).
- [Využijte vysokorychlostní třídy XML a oddělovače souborů a zapisovače](spatial-io-core-operations.md).

V této příručce se dozvíme, jak integrovat a používat modul Prostorové vive ve webové aplikaci.

Toto video poskytuje přehled prostorového io modulu v Azure Maps Web SDK.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Easily-integrate-spatial-data-into-the-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0" title="Snadno integrujte prostorová data do Azure Maps – Microsoft Channel 9 Video"></iframe>


> [!WARNING]
> Používejte pouze data a služby, které pocházejí ze zdroje, kterému důvěřujete, zejména pokud na ně odkazujete z jiné domény. Prostorový vstupně-tomodul podnikne kroky k minimalizaci rizika, ale nejbezpečnější přístup je příliš neumožňuje žádné danagerous data do aplikace začít. 

## <a name="prerequisites"></a>Požadavky

Než budete moci použít modul Prostorové IO, budete muset [vytvořit účet Azure Maps](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#create-an-account-with-azure-maps) a získat primární klíč předplatného pro svůj [účet](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#get-the-primary-key-for-your-account).

## <a name="installing-the-spatial-io-module"></a>Instalace modulu Prostorové IO

Prostorový iO modul Azure Maps můžete načíst pomocí jedné ze dvou možností:

* Globálně hostovaný Azure CDN pro prostorový IO modul Azure Maps. Pro tuto volbu přidáte odkaz na JavaScript v elementu `<head>` souboru HTML.

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

* Zdrojový kód pro [azure-maps-spatial-io](https://www.npmjs.com/package/azure-maps-spatial-io) lze načíst místně a pak hostované s vaší aplikací. Tento balíček také obsahuje definice jazyka TypeScript. Pro tuto možnost nainstalujte balíček pomocí následujícího příkazu:

    ```sh
    npm install azure-maps-spatial-io
    ```

    Potom přidejte odkaz na JavaScript `<head>` v elementu dokumentu HTML:

    ```html
    <script src="node_modules/azure-maps-spatial-io/dist/atlas-spatial.min.js"></script>
    ```

## <a name="using-the-spatial-io-module"></a>Použití modulu Prostorové IO

1. Vytvořte nový soubor HTML.

2. Načtěte webovou sadku Azure Maps SDK a inicializujte ovládací prvek mapy. Podrobnosti najdete v průvodci [ovládacím prvkem map Azure Maps.](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control) Po dokončení tohoto kroku by měl soubor HTML vypadat takto:

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

2. Načtěte prostorový io modul Azure Maps. Pro toto cvičení použijte CDN pro modul prostorových IO Azure Maps. Přidejte níže uvedený `<head>` odkaz na prvek souboru HTML:

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

3. Inicializovat `datasource`a a přidat zdroj dat do mapy. Inicializovat `layer`a a přidat zdroj dat do vrstvy mapy. Potom vykreslete zdroj dat i vrstvu. Než se posunete dolů a zobrazíte úplný kód v dalším kroku, zamyslete se nad nejlepšími místy, kam umístit fragmenty kódu zdroje dat a vrstvy. Připomeňme si, že než budeme programově manipulovat s mapou, měli bychom počkat, až bude prostředek mapy připraven.

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

4. Když to všechno dáme dohromady, váš kód HTML by měl vypadat jako následující kód. Tato ukázka ukazuje, jak číst soubor XML z adresy URL. Poté načtěte a zobrazte data funkcí souboru na mapě. 

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

5. Nezapomeňte jej `<Your Azure Maps Key>` nahradit primárním klíčem. Otevřete soubor HTML a uvidíte výsledky podobné následujícímu obrázku:

    <center>

    ![Příklad prostorových dat](./media/how-to-use-spatial-io-module/spatial-data-example.png)

    </center>

## <a name="next-steps"></a>Další kroky

Funkce, kterou jsme zde předvedli, je pouze jednou z mnoha funkcí dostupných v modulu Prostorové iO. Přečtěte si níže uvedené příručky, kde se dozvíte, jak používat další funkce v modulu Prostorové vi:

> [!div class="nextstepaction"]
> [Přidání jednoduché datové vrstvy](spatial-io-add-simple-data-layer.md)

> [!div class="nextstepaction"]
> [Čtení a zápis prostorových dat](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Přidání mapové vrstvy OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Připojení ke službě WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Využití základních operací](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Podrobnosti o podporovaném formátu dat](spatial-io-supported-data-format-details.md)

Podívejte se na dokumentaci prostorových io Azure Maps:

> [!div class="nextstepaction"]
> [Balíček Prostorových IO Azure Maps](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/)

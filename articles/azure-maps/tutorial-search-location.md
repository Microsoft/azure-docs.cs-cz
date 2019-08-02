---
title: Vyhledávání s využitím Azure Maps | Microsoft Docs
description: Vyhledání okolního bodu zájmu s využitím Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: a75f3f606129d370457816507537f2cb4491adf8
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478833"
---
# <a name="search-nearby-points-of-interest-using-azure-maps"></a>Hledání okolních bodů zájmu s využitím Azure Maps

V tomto kurzu se dozvíte, jak nastavit účet s Azure Maps a pak pomocí rozhraní Maps API vyhledat bod zájmu. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření účtu Azure Maps
> * Získání primárního klíče pro váš účet Maps
> * Vytvoření nové webové stránky s použitím rozhraní API pro mapové ovládací prvky
> * Vyhledání okolního bodu zájmu pomocí vyhledávací služby Maps

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Vytvoření účtu s Azure Maps

Pomocí následujících kroků vytvořte nový účet Maps:

1. V levém horním rohu webu [Azure Portal](https://portal.azure.com) klikněte na **Vytvořit prostředek**.
2. Do pole *Hledat na Marketplace* zadejte **Maps**.
3. Ve *výsledcích* vyberte **Maps**. Klikněte na tlačítko **Vytvořit**, které se zobrazí pod mapou.
4. Na stránce **Vytvořit účet Maps** zadejte následující hodnoty:
    * *Předplatné*, které chcete pro tento účet použít.
    * Název *skupiny prostředků* pro tento účet. Můžete zvolit možnost *Vytvořit novou* nebo *Použít existující* skupinu prostředků.
    * *Název* vašeho nového účtu.
    * *Cenová úroveň* pro tento účet.
    * Přečtěte si *Licenční podmínky* a *Prohlášení o zásadách ochrany osobních údajů* a zaškrtnutím příslušného políčka podmínky přijměte.
    * Klikněte na tlačítko **Vytvořit**.

![Vytvoření účtu Maps na portálu](./media/tutorial-search-location/create-account.png)

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Získání primárního klíče pro váš účet

Po úspěšném vytvoření účtu Maps načtěte klíč, který vám umožní dotazovat rozhraní Maps API.

1. Otevřete svůj účet Maps na portálu.
2. V části nastavení vyberte **ověřování**.
3. Zkopírujte do schránky **Primární klíč**. Místně ho uložte, abyste ho mohli použít později v tomto kurzu.

![Získání primárního klíče na portálu](./media/tutorial-search-location/get-key.png)

<a id="createmap"></a>

## <a name="create-a-new-map"></a>Vytvoření nové mapy

Rozhraní API pro mapové ovládací prvky je praktická klientská knihovna, která umožňuje snadnou integraci Maps do webové aplikace. Skrývá složitost prostých volání služby REST a zvyšuje produktivitu díky přizpůsobitelným komponentám, které umožňují změnu stylu. Následující kroky ukazují, jak vytvořit statickou stránku HTML s vloženým rozhraním API pro mapové ovládací prvky.

1. Na místním počítači vytvořte nový soubor s názvem **MapSearch.html**.
2. Přidejte do souboru následující komponenty HTML:

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Search</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
        function GetMap(){
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

   Všimněte si, že hlavička HTML zahrnuje soubory prostředků šablon stylů CSS a JavaScriptu hostované knihovnou Ovládací prvek Mapa v Azure. V těle stránky si všimněte události `onload`, která po načtení těla stránky zavolá funkci `GetMap`. `GetMap` Funkce bude obsahovat vložený kód jazyka JavaScript pro přístup k rozhraním API Azure Maps.

3. Do funkce `GetMap` v souboru HTML přidejte následující kód JavaScriptu. Nahraďte řetězec `<Your Azure Maps Key>` primárním klíčem, který jste zkopírovali z účtu Maps.

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

   Tento segment inicializuje rozhraní API pro mapové ovládací prvky pro váš klíč účtu Azure Maps. `atlas`je obor názvů, který obsahuje rozhraní API a související vizuální komponenty. `atlas.Map`poskytuje ovládací prvek pro vizuální a interaktivní webové mapování.

4. Uložte provedené změny do souboru a otevřete stránku HTML v prohlížeči. Toto je nejzákladnější mapa, kterou můžete provést voláním `atlas.Map` pomocí klíče účtu.

   ![Zobrazení mapy](./media/tutorial-search-location/basic-map.png)

5. Do funkce `GetMap` za inicializaci mapy přidejte následující kód JavaScriptu.

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering point data.
        var resultLayer = new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: 'pin-round-darkblue',
                anchor: 'center',
                allowOverlap: true
            },
            textOptions: {
                anchor: "top"
            }
        });

        map.layers.add(resultLayer);
    });
    ```

   V tomto segmentu `ready` kódu se událost přidá do mapy, která se aktivuje, když se načtou prostředky mapy a že je k ní přistupovaná mapa připravena. V obslužné rutině události mapy `ready` je vytvořen zdroj dat pro ukládání výsledných dat. Vytvoří se vrstva symbolů, která se připojí ke zdroji dat. Tato vrstva určuje, jak se mají vykreslit výsledná data ve zdroji dat, v tomto případě jako ikona tmavě modrého kulatého špendlíku ve středu souřadnic výsledku, který umožňuje překrytí jinými ikonami. Výsledná vrstva je přidána do vrstev mapy.

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Přidání možností vyhledávání

V této části se dozvíte, jak pomocí [rozhraní API pro vyhledávání](https://docs.microsoft.com/rest/api/maps/search) map najít na mapě bod zájmu. Jedná se o rozhraní RESTful API navržené pro vývojáře, kterým umožňuje hledat adresy, body zájmu a další geografické informace. Služba Search přiřazuje k zadané adrese informace o zeměpisné délce a šířce. **Modul služeb** popsaný dál slouží k vyhledání polohy pomocí rozhraní API Maps Search.

### <a name="service-module"></a>Modul služeb

1. V obslužné rutině události mapy `ready` vytvořte adresu URL vyhledávací služby přidáním následujícího kódu jazyka JavaScript.

    ```JavaScript
   // Use SubscriptionKeyCredential with a subscription key
   var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

   // Use subscriptionKeyCredential to create a pipeline
   var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

   // Construct the SearchURL object
   var searchURL = new atlas.service.SearchURL(pipeline); 
   ```

   `SubscriptionKeyCredential` VytvoříaověřípožadavkyHTTP,kterésemajíAzureMapspomocí`SubscriptionKeyCredentialPolicy` klíče předplatného. Zásada převezme a vytvoří instanci kanálu. [](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) `atlas.service.MapsURL.newPipeline()` `SubscriptionKeyCredential` Představuje adresu URL pro Azure Maps vyhledávacích operací. [](https://docs.microsoft.com/rest/api/maps/search) `searchURL`

2. Dále přidejte následující blok skriptu pro sestavení vyhledávacího dotazu. Používá službu Fuzzy Search, což je základní rozhraní API služby Search Service. Služba Fuzzy Search zpracovává většinu přibližných vstupů, jako jsou adresy, místa a body zájmu. Tento kód vyhledá okolí benzinových stanic v rámci zadaného poloměru poskytnuté zeměpisné šířky a délky. Kolekce funkcí pro injson z odpovědi je pak extrahována pomocí `geojson.getFeatures()` metody a přidána do zdroje dat, což automaticky vede k vykreslování dat na mapě prostřednictvím vrstvy symbolů. Poslední část skriptu pomocí vlastnosti mapy [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) nastaví zobrazení kamery mapy s použitím ohraničujícího rámečku výsledků.

    ```JavaScript
    var query =  'gasoline-station';
    var radius = 9000;
    var lat = 47.64452336193245;
    var lon = -122.13687658309935;

    searchURL.searchPOI(atlas.service.Aborter.timeout(10000), query, {
        limit: 10,
        lat: lat,
        lon: lon,
        radius: radius
    }).then((results) => {

        // Extract GeoJSON feature collection from the response and add it to the datasource
        var data = results.geojson.getFeatures();
        datasource.add(data);

        // set camera to bounds to show the results
        map.setCamera({
            bounds: data.bbox,
            zoom: 10
        });
    });
    ```

3. Uložte soubor **MapSearch.html** a aktualizujte prohlížeč. Nyní byste měli vidět, že mapa je vytvořená na střed Seattle s kulatými a modrými kolíky označujícími umístění benzinových stanic v oblasti.

   ![Zobrazení mapy s výsledky hledání](./media/tutorial-search-location/pins-map.png)

4. Můžete zobrazit nezpracovaná data, která mapa vykresluje, zadáním následujícího požadavku HTTP v prohlížeči. Nahraďte \<Your Azure Maps Key\> svým primárním klíčem.

   ```http
   https://atlas.microsoft.com/search/poi/json?api-version=2&query=gasoline%20station&subscription-key=<subscription-key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

V tomto okamžiku může stránka MapSearch zobrazit umístění bodů zájmu, které se vrátí z dotazu na vyhledání přibližných shod. Teď přidáme několik interaktivních funkcí a další informace o umístěních.

## <a name="add-interactive-data"></a>Přidání interaktivních dat

Mapa, kterou jsme vytvořili, zatím z výsledků hledání používá pouze data o zeměpisné šířce a délce. Pokud se však podíváte na nezpracovaný JSON, který vrací vyhledávací služba Maps, uvidíte, že obsahuje další informace o jednotlivých čerpacích stanicích, včetně názvu a adresy. Tato data můžete zahrnout do mapy s použitím interaktivních, automaticky otevíraných oken.

1. Přidejte následující řádky kódu do obslužné rutiny události `ready` mapy po kódu pro dotazování na službu přibližné vyhledávání. Tím se vytvoří instance metody Popup a do vrstvy symbolů se přidá událost mouseover.

    ```JavaScript
   //Create a popup but leave it closed so we can update it and display it later.
    popup = new atlas.Popup();

    //Add a mouse over event to the result layer and display a popup when this event fires.
    map.events.add('mouseover', resultLayer, showPopup);
    ```

    Rozhraní API `*atlas.Popup` poskytuje okno informace ukotvené na požadované pozici na mapě. 

2. Do této `GetMap` funkce přidejte následující kód, chcete-li v překryvném okně zobrazit ukazatel myši nad výslednou informací.

    ```JavaScript
    function showPopup(e) {
        //Get the properties and coordinates of the first shape that the event occured on.

        var p = e.shapes[0].getProperties();
        var position = e.shapes[0].getCoordinates();

        //Create HTML from properties of the selected result.
        var html = ['<div style="padding:5px"><div><b>', p.poi.name,
            '</b></div><div>', p.address.freeformAddress,
            '</div><div>', position[1], ', ', position[0], '</div></div>'];

        //Update the content and position of the popup.
        popup.setPopupOptions({
            content: html.join(''),
            position: position
        });

        //Open the popup.
        popup.open(map);
    }
    ```

3. Uložte soubor a aktualizujte prohlížeč. Teď mapa v prohlížeči při najetí myší na některý ze špendlíků vyhledávání ukazuje automaticky otevíraná okna s informacemi.

    ![Ovládací prvek Mapa a Search Service v Azure](./media/tutorial-search-location/popup-map.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření účtu s Azure Maps
> * Získání primárního klíče pro váš účet
> * Vytvoření nové webové stránky s použitím rozhraní API pro mapové ovládací prvky
> * Vyhledání okolního bodu zájmu pomocí služby Search Service

> [!div class="nextstepaction"]
> [Zobrazit úplný zdrojový kód](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/search.html)

> [!div class="nextstepaction"]
> [Zobrazit ukázku živého vysílání](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20for%20points%20of%20interest)

V dalším kurzu se dozvíte, jak zobrazit trasu mezi dvěma umístěními.

> [!div class="nextstepaction"]
> [Trasa do cíle](./tutorial-route-location.md)

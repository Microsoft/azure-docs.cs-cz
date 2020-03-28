---
title: 'Výuka: Hledání blízkých míst na mapě | Mapy Microsoft Azure'
description: V tomto kurzu se dozvíte, jak vyhledávat body zájmu na mapě pomocí Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 0b0cb92cd6b4918e28e143178a5cdbbbb19ac9af
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333633"
---
# <a name="tutorial-search-nearby-points-of-interest-using-azure-maps"></a>Kurz: Hledání blízkých bodů zájmu pomocí Azure Maps

V tomto kurzu se dozvíte, jak nastavit účet s Azure Maps a pak pomocí rozhraní Maps API vyhledat bod zájmu. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření účtu Azure Maps
> * Získání primárního klíče pro váš účet Maps
> * Vytvořit novou webovou stránku s použitím rozhraní API pro mapové ovládací prvky
> * Vyhledání okolního bodu zájmu pomocí vyhledávací služby Maps

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure](https://portal.azure.com).

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

![Vytvoření účtu Azure Maps na webu Azure Portal](./media/tutorial-search-location/create-account.png)

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Získání primárního klíče pro váš účet

Po úspěšném vytvoření účtu Maps načtěte klíč, který vám umožní dotazovat rozhraní Maps API. Doporučujeme používat primární klíč vašeho účtu jako klíč předplatného při volání služeb Azure Maps.

1. Otevřete svůj účet Maps na portálu.
2. V části nastavení vyberte **Možnost Ověřování**.
3. Zkopírujte do schránky **Primární klíč**. Místně ho uložte, abyste ho mohli použít později v tomto kurzu.

![Získání primárního klíče na webu Azure Portal](./media/tutorial-search-location/get-key.png)

Další informace o ověřování v Azure Maps najdete v [tématu správa ověřování v Azure Maps](how-to-manage-authentication.md).

<a id="createmap"></a>

## <a name="create-a-new-map"></a>Vytvoření nové mapy

Rozhraní API pro řízení mapy je praktická klientská knihovna. Toto ROZHRANÍ API umožňuje snadnointegrovat Mapy do webové aplikace. Skrývá složitost holých volání služby REST a zvyšuje vaši produktivitu pomocí přizpůsobitelných komponent. Následující kroky ukazují, jak vytvořit statickou stránku HTML s vloženým rozhraním API pro mapové ovládací prvky.

1. Na místním počítači vytvořte nový soubor s názvem **MapSearch.html**.
2. Přidejte do souboru následující součásti HTML:

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

   Všimněte si, že hlavička HTML zahrnuje soubory prostředků šablon stylů CSS a JavaScriptu hostované knihovnou Ovládací prvek Mapa v Azure. V těle stránky si všimněte události `onload`, která po načtení těla stránky zavolá funkci `GetMap`. Funkce `GetMap` bude obsahovat vřádík JavaScript kód pro přístup k rozhraní API Azure Maps.

3. Do funkce `GetMap` v souboru HTML přidejte následující kód JavaScriptu. Nahraďte `<Your Azure Maps Key>` řetězec primárním klíčem, který jste zkopírovali ze svého účtu Mapy.

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

   Tento segment inicializuje rozhraní API pro mapové ovládací prvky pro váš klíč účtu Azure Maps. `atlas`je obor názvů, který obsahuje rozhraní API a související vizuální součásti. `atlas.Map`poskytuje ovládací prvek pro vizuální a interaktivní webovou mapu.

4. Uložte provedené změny do souboru a otevřete stránku HTML v prohlížeči. Zobrazená mapa je nejzákladnější mapou, `atlas.Map` kterou můžete vytvořit voláním pomocí klíče účtu.

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

   V tomto segmentu `ready` kódu je do mapy přidána událost, která se spálí, když jsou načteny prostředky mapy a mapa je připravena k přístupu. V obslužné rutině události mapování `ready` je vytvořen zdroj dat pro ukládání dat výsledků. Vytvoří se vrstva symbolů, která se připojí ke zdroji dat. Tato vrstva určuje, jak mají být vykreslena výsledná data ve zdroji dat. V tomto případě je výsledek vykreslen tmavě modrou ikonou kulatého kolíku, vystředěnou nad souřadnici výsledků a umožňuje, aby se ostatní ikony překrývaly. Výsledná vrstva se přidá do vrstev mapy.

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Přidání možností vyhledávání

Tato část ukazuje, jak pomocí [rozhraní MAP Search API](https://docs.microsoft.com/rest/api/maps/search) najít na mapě bod zájmu. Jedná se o rozhraní RESTful API navržené pro vývojáře, kterým umožňuje hledat adresy, body zájmu a další geografické informace. Služba Search přiřazuje k zadané adrese informace o zeměpisné délce a šířce. **Modul služeb** popsaný dál slouží k vyhledání polohy pomocí rozhraní API Maps Search.

### <a name="service-module"></a>Modul služeb

1. V obslužné rutině události mapy `ready` vytvořte adresu URL vyhledávací služby přidáním následujícího kódu Javascriptu.

    ```JavaScript
   // Use SubscriptionKeyCredential with a subscription key
   var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

   // Use subscriptionKeyCredential to create a pipeline
   var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

   // Construct the SearchURL object
   var searchURL = new atlas.service.SearchURL(pipeline); 
   ```

   Vytvoří `SubscriptionKeyCredential` `SubscriptionKeyCredentialPolicy` k ověření http požadavky na Azure Maps s klíčem předplatného. Bere `atlas.service.MapsURL.newPipeline()` v `SubscriptionKeyCredential` zásadě a vytvoří [pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) instance. Představuje `searchURL` adresu URL pro operace Azure Maps [Search.](https://docs.microsoft.com/rest/api/maps/search)

2. Dále přidejte následující blok skriptu pro sestavení vyhledávacího dotazu. Používá službu Fuzzy Search, což je základní rozhraní API služby Search Service. Služba Fuzzy Search zpracovává většinu přibližných vstupů, jako jsou adresy, místa a body zájmu. Tento kód vyhledá v blízkosti Benzinové stanice v určeném poloměru stanovené zeměpisné šířky a délky. Kolekce funkcí GeoJSON z odpovědi je pak `geojson.getFeatures()` extrahována pomocí metody a přidána do zdroje dat, což automaticky vede k vykreslení dat na mapě prostřednictvím vrstvy symbolů. Poslední část skriptu pomocí vlastnosti mapy [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) nastaví zobrazení kamery mapy s použitím ohraničujícího rámečku výsledků.

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

3. Uložte soubor **MapSearch.html** a aktualizujte prohlížeč. Měli byste vidět mapu soustředěnou na Seattle s kulatými modrými kolíky pro umístění benzínových stanic v této oblasti.

   ![Zobrazení mapy s výsledky hledání](./media/tutorial-search-location/pins-map.png)

4. Můžete zobrazit nezpracovaná data, která mapa vykresluje, zadáním následujícího požadavku HTTP v prohlížeči. Nahraďte \<Your Azure Maps Key\> svým primárním klíčem.

   ```http
   https://atlas.microsoft.com/search/poi/json?api-version=1.0&query=gasoline%20station&subscription-key=<subscription-key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

V tomto okamžiku může stránka MapSearch zobrazit umístění bodů zájmu, které se vrátí z dotazu na vyhledání přibližných shod. Teď přidáme několik interaktivních funkcí a další informace o umístěních.

## <a name="add-interactive-data"></a>Přidání interaktivních dat

Mapa, kterou jsme vytvořili, zatím z výsledků hledání používá pouze data o zeměpisné šířce a délce. Nezpracovaný json, který služba Vyhledávání map vrací, však obsahuje další informace o každé čerpací stanici. Včetně jména a adresy. Tato data můžete zahrnout do mapy s použitím interaktivních, automaticky otevíraných oken.

1. Přidejte následující řádky kódu `ready` do obslužné rutiny události mapy za kód pro dotaz na službu přibližného vyhledávání. Tento kód vytvoří instanci místního oznámení a přidá událost myši do vrstvy symbolů.

    ```JavaScript
   //Create a popup but leave it closed so we can update it and display it later.
    popup = new atlas.Popup();

    //Add a mouse over event to the result layer and display a popup when this event fires.
    map.events.add('mouseover', resultLayer, showPopup);
    ```

    Rozhraní `*atlas.Popup` API poskytuje informační okno ukotvené na požadované pozici na mapě. 

2. Přidejte následující kód `GetMap` v rámci funkce, chcete-li zobrazit najeté myší informace o výsledku v popup.

    ```JavaScript
    function showPopup(e) {
        //Get the properties and coordinates of the first shape that the event occurred on.

        var p = e.shapes[0].getProperties();
        var position = e.shapes[0].getCoordinates();

        //Create HTML from properties of the selected result.
        var html = `
          <div style="padding:5px">
            <div><b>${p.poi.name}</b></div>
            <div>${p.address.freeformAddress}</div>
            <div>${position[1]}, ${position[0]}</div>
          </div>`;

        //Update the content and position of the popup.
        popup.setPopupOptions({
            content: html,
            position: position
        });

        //Open the popup.
        popup.open(map);
    }
    ```

3. Uložte soubor a aktualizujte prohlížeč. Teď mapa v prohlížeči při najetí myší na některý ze špendlíků vyhledávání ukazuje automaticky otevíraná okna s informacemi.

    ![Ovládací prvek Mapa a Search Service v Azure](./media/tutorial-search-location/popup-map.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření účtu s Azure Maps
> * Získání primárního klíče pro váš účet
> * Vytvoření nové webové stránky s použitím rozhraní API pro mapové ovládací prvky
> * Vyhledání okolního bodu zájmu pomocí služby Search Service

> [!div class="nextstepaction"]
> [Zobrazit úplný zdrojový kód](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/search.html)

> [!div class="nextstepaction"]
> [Zobrazit živý vzorek](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20for%20points%20of%20interest)

V dalším kurzu se dozvíte, jak zobrazit trasu mezi dvěma umístěními.

> [!div class="nextstepaction"]
> [Trasa do cíle](./tutorial-route-location.md)

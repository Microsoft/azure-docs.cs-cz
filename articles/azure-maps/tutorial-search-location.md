---
title: Vyhledávání s využitím Azure Maps | Microsoft Docs
description: Vyhledání okolního bodu zájmu s využitím Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 12/14/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 1396260ef703ce22f8e0309bd2c8df691d0af86e
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54120457"
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
    * *Název* vašeho nového účtu.
    * *Předplatné*, které chcete pro tento účet použít.
    * Název *skupiny prostředků* pro tento účet. Můžete zvolit možnost *Vytvořit novou* nebo *Použít existující* skupinu prostředků.
    * Vyberte *Umístění skupiny prostředků*.
    * Přečtěte si *Licenční podmínky* a *Prohlášení o zásadách ochrany osobních údajů* a zaškrtnutím příslušného políčka podmínky přijměte.
    * Klikněte na tlačítko **Vytvořit**.

    ![Vytvoření účtu Maps na portálu](./media/tutorial-search-location/create-account.png)

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Získání primárního klíče pro váš účet

Po úspěšném vytvoření účtu Maps načtěte klíč, který vám umožní dotazovat rozhraní Maps API.

1. Otevřete svůj účet Maps na portálu.
2. V části Nastavení vyberte **Klíče**.
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
      <meta charset="utf-8" />
      <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

      <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
      <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>

      <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
      <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1"></script>

      <script>      
         var map, datasource, client, popup;

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

   Všimněte si, že hlavička HTML zahrnuje soubory prostředků šablon stylů CSS a JavaScriptu hostované knihovnou Ovládací prvek Mapa v Azure. V těle stránky si všimněte události `onload`, která po načtení těla stránky zavolá funkci `GetMap`. Tato funkce bude obsahovat vložený kód JavaScriptu pro přístup k rozhraním Azure Maps API.

3. Do funkce `GetMap` v souboru HTML přidejte následující kód JavaScriptu. Nahraďte řetězec **\<Your Azure Maps Key\>** primárním klíčem, který jste zkopírovali ze svého účtu Maps.

   ```JavaScript
   //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
   atlas.setSubscriptionKey('<Your Azure Maps Key>');

   //Initialize a map instance.
   map = new atlas.Map('myMap');
   ```

   Tento segment inicializuje rozhraní API pro mapové ovládací prvky pro váš klíč účtu Azure Maps. **atlas** je obor názvů, který obsahuje rozhraní API a související vizuální komponenty. **atlas.Map** zajišťuje ovládací prvek pro vizuální a interaktivní webovou mapu. 

4. Uložte provedené změny do souboru a otevřete stránku HTML v prohlížeči. Toto je nejzákladnější mapa, kterou můžete vytvořit zavoláním **atlas.map** s použitím klíče účtu.

   ![Zobrazení mapy](./media/tutorial-search-location/basic-map.png)

5. Do funkce `GetMap` za inicializaci mapy přidejte následující kód JavaScriptu. 

   ```JavaScript
   //Wait until the map resources have fully loaded.
   map.events.add('load', function () {

      //Create a data source and add it to the map.
      datasource = new atlas.source.DataSource();
      map.sources.add(datasource);

      //Add a layer for rendering point data.
      var resultLayer = new atlas.layer.SymbolLayer(datasource, null, {
         iconOptions: {
            iconImage: 'pin-round-darkblue',
            anchor: 'center',
            allowOverlap: true
         }
      });
      map.layers.add(resultLayer);

      //Create a popup but leave it closed so we can update it and display it later.
      popup = new atlas.Popup();

      //Add a mouse over event to the result layer and display a popup when this event fires.
      map.events.add('mouseover', resultLayer, showPopup);
   });
   ```

   Do mapy se přidá událost load, která se aktivuje po plném načtení prostředků mapy. V obslužné rutině události načtení mapy se vytvoří zdroj dat, do kterého se uloží výsledná data. Vytvoří se vrstva symbolů, která se připojí ke zdroji dat. Tato vrstva určuje, jak se mají vykreslit výsledná data ve zdroji dat, v tomto případě jako ikona tmavě modrého kulatého špendlíku ve středu souřadnic výsledku, který umožňuje překrytí jinými ikonami. 

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Přidání možností vyhledávání

Tato část ukazuje, jak pomocí rozhraní API vyhledávací služby Maps vyhledat na mapě bod zájmu. Jedná se o rozhraní RESTful API navržené pro vývojáře, kterým umožňuje hledat adresy, body zájmu a další geografické informace. Služba Search přiřazuje k zadané adrese informace o zeměpisné délce a šířce. **Modul služeb** popsaný dál slouží k vyhledání polohy pomocí rozhraní API Maps Search.

### <a name="service-module"></a>Modul služeb

1. Přidáním následujícího kódu JavaScriptu do obslužné rutiny události načtení mapy vytvořte instanci klientské služby.

    ```JavaScript
    //Create an instance of the services client.
     client = new atlas.service.Client(atlas.getSubscriptionKey());
    ```

2. Dále přidejte následující blok skriptu pro sestavení vyhledávacího dotazu. Používá službu Fuzzy Search, což je základní rozhraní API služby Search Service. Služba Fuzzy Search zpracovává většinu přibližných vstupů, jako jsou adresy, místa a body zájmu. Tento kód vyhledá nejbližší čerpací stanice v zadaném okruhu. Odpověď se pak parsuje do formátu GeoJSON a přidá se ke zdroji dat, což má automaticky za následek vykreslení dat na mapě ve vrstvě symbolů. Poslední část skriptu pomocí vlastnosti mapy [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) nastaví zobrazení kamery mapy s použitím ohraničujícího rámečku výsledků. Protože se ohraničující rámeček počítá na základě souřadnic, přidá se odsazení, které kompenzuje rozměry ikon symbolů v pixelech. 
 
   ```JavaScript
   //Execute a POI search query then add the results to the map.
    client.search.getSearchPOI('gasoline station', {
        lat: 47.6292,
        lon: -122.2337,
        radius: 100000
    }).then(response => {
        //Parse the response into GeoJSON so that the map can understand.
        var geojsonResponse = new atlas.service.geojson.GeoJsonSearchResponse(response);
        var results = geojsonResponse.getGeoJsonResults();

        //Add the results to the data source so they can be rendered. 
        datasource.add(results);

        // Set the camera bounds
        map.setCamera({
            bounds: results.bbox,
            padding: 50
        });
    });
   ```
 
3. Uložte soubor **MapSearch.html** a aktualizujte prohlížeč. Teď by se měla zobrazit mapa, v jejímž středu je Seattle, s modrými špendlíky, které označují umístění čerpacích stanic v příslušné oblasti.

   ![Zobrazení mapy s výsledky hledání](./media/tutorial-search-location/pins-map.png)

4. Můžete zobrazit nezpracovaná data, která mapa vykresluje, zadáním následujícího požadavku HTTP v prohlížeči. Nahraďte \<Your Azure Maps Key\> svým primárním klíčem.

   ```http
   https://atlas.microsoft.com/search/fuzzy/json?api-version=1.0&query=gasoline%20station&subscription-key=<Your Azure Maps Key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

V tomto okamžiku může stránka MapSearch zobrazit umístění bodů zájmu, které se vrátí z dotazu na vyhledání přibližných shod. Teď přidáme několik interaktivních funkcí a další informace o umístěních.

## <a name="add-interactive-data"></a>Přidání interaktivních dat

Mapa, kterou jsme vytvořili, zatím z výsledků hledání používá pouze data o zeměpisné šířce a délce. Pokud se však podíváte na nezpracovaný JSON, který vrací vyhledávací služba Maps, uvidíte, že obsahuje další informace o jednotlivých čerpacích stanicích, včetně názvu a adresy. Tato data můžete zahrnout do mapy s použitím interaktivních, automaticky otevíraných oken.

1. Do obslužné rutiny události načtení mapy za kód pro dotazování služby Fuzzy Search přidejte následující řádky kódu. Tím se vytvoří instance metody Popup a do vrstvy symbolů se přidá událost mouseover.

    ```JavaScript
   //Create a popup but leave it closed so we can update it and display it later.
    popup = new atlas.Popup();

    //Add a mouse over event to the result layer and display a popup when this event fires.
    map.events.add('mouseover', resultLayer, showPopup);
    ```
    
    **atlas.Popup** v rozhraní API poskytuje okno s informacemi ukotvené na požadované pozici na mapě. 
      
2. Do značky *script* za funkci `GetMap` přidejte následující kód, který po najetí myši zobrazí v automaticky otevíraném okně informace o výsledku. 

   ```JavaScript
   function showPopup(e) {
        //Get the properties and coordinates of the first shape that the event occurred on.
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

2. Uložte soubor a aktualizujte prohlížeč. Teď mapa v prohlížeči při najetí myší na některý ze špendlíků vyhledávání ukazuje automaticky otevíraná okna s informacemi.

    ![Ovládací prvek Mapa a Search Service v Azure](./media/tutorial-search-location/popup-map.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření účtu s Azure Maps
> * Získání primárního klíče pro váš účet
> * Vytvoření nové webové stránky s použitím rozhraní API pro mapové ovládací prvky
> * Vyhledání okolního bodu zájmu pomocí služby Search Service

Přístup k vzorovému kódu pro tento kurz můžete získat tady:

> [Vyhledávání polohy pomocí Azure Maps](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/search.html)

[Podívejte se na živou ukázku](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20for%20points%20of%20interest)

V dalším kurzu se dozvíte, jak zobrazit trasu mezi dvěma umístěními.

> [!div class="nextstepaction"]
> [Trasa do cíle](./tutorial-route-location.md)

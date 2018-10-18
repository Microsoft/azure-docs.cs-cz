---
title: Vyhledávání s využitím Azure Maps | Microsoft Docs
description: Vyhledání okolního bodu zájmu s využitím Azure Maps
author: dsk-2015
ms.author: dkshir
ms.date: 10/02/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 761674c5839f0513532355116db07604f9e9d9dc
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816816"
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
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Search</title>

        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.min.js?api-version=1"></script>

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

    <body>
        <div id="map"></div>
        <script>
            // Embed Map Control JavaScript code here
        </script>
    </body>

    </html>
    ```
    Všimněte si, že hlavička HTML zahrnuje soubory prostředků šablon stylů CSS a JavaScriptu hostované knihovnou Ovládací prvek Mapa v Azure. Všimněte si segmentu *script* přidaného do části *body* souboru HTML. Tento segment bude obsahovat vložený kód JavaScriptu pro přístup k rozhraním Azure Maps API.

3. Do bloku *script* v souboru HTML přidejte následující kód JavaScriptu. Nahraďte řetězec **\<your account key\>** primárním klíčem, který jste zkopírovali ze svého účtu Maps.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var MapsAccountKey = "<your account key>";
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey
    });
    ```
    Tento segment inicializuje rozhraní API pro mapové ovládací prvky pro váš klíč účtu Azure Maps. **Atlas** je obor názvů, který obsahuje rozhraní API a související vizuální komponenty. **Atlas.Map** zajišťuje ovládací prvek pro vizuální a interaktivní webovou mapu.

4. Uložte provedené změny do souboru a otevřete stránku HTML v prohlížeči. Toto je nejzákladnější mapa, kterou můžete vytvořit zavoláním **atlas.map** s použitím klíče účtu.

   ![Zobrazení mapy](./media/tutorial-search-location/basic-map.png)

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Přidání možností vyhledávání

Tato část ukazuje, jak pomocí rozhraní API vyhledávací služby Maps vyhledat na mapě bod zájmu. Jedná se o rozhraní RESTful API navržené pro vývojáře, kterým umožňuje hledat adresy, body zájmu a další geografické informace. Služba Search přiřazuje k zadané adrese informace o zeměpisné délce a šířce. **Modul služeb** popsaný dál slouží k vyhledání polohy pomocí rozhraní API Maps Search.

### <a name="service-module"></a>Modul služeb

1. Přidejte do své mapy novou vrstvu, na které se zobrazí výsledky hledání. Přidejte následující kód JavaScriptu do bloku skriptu za kód, který inicializuje mapu.

    ```JavaScript
    // Initialize the pin layer for search results to the map
    var searchLayerName = "search-results";
    ```

2. Pokud chcete vytvořit instanci služby klienta, přidejte do bloku skriptu za kód, který inicializuje mapu, následující kód JavaScriptu.

    ```JavaScript
    var client = new atlas.service.Client(MapsAccountKey);
    ```

3. Všechny funkce na mapě by se měly načíst až po načtení mapy. Můžete to zajistit umístěním všech funkcí mapy do bloku eventListener mapy. Přidáním následujícího řádku kódu přidáte do mapy [eventListener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener) a zajistíte úplné načtení mapy před přidáním funkcí.
    
    ```JavaScript
         map.addEventListener("load", function() {
         });
    ```

4. Přidáním následujícího bloku skriptu **do modulu eventListener pro načtení mapy** sestavte dotaz. Používá službu Fuzzy Search, což je základní rozhraní API služby Search Service. Služba Fuzzy Search zpracovává většinu přibližných vstupů, například kombinaci  tokenů adresy a bodu zájmu (POI). Vyhledá nejbližší čerpací stanice v zadaném okruhu. Odpověď se pak parsuje do formátu GeoJSON a převede do podoby bodů, které se přidají do mapy jako špendlíky. Poslední část skriptu přidá meze fotoaparátu pro mapu pomocí vlastnosti [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) mapy.

    ```JavaScript

            // Execute a POI search query then add pins to the map for each result once a response is received
            client.search.getSearchFuzzy("gasoline station", {
            lat: 47.6292,
            lon: -122.2337,
            radius: 100000
            }).then(response => {
       
            // Parse the response into GeoJSON 
            var geojsonResponse = new atlas.service.geojson.GeoJsonSearchResponse(response);

            // Create the point features that will be added to the map as pins
            var searchPins = geojsonResponse.getGeoJsonResults().features.map(poiResult => {
               var poiPosition = [poiResult.properties.position.lon, poiResult.properties.position.lat];
               return new atlas.data.Feature(new atlas.data.Point(poiPosition), {
                name: poiResult.properties.poi.name,
                address: poiResult.properties.address.freeformAddress,
                position: poiPosition[1] + ", " + poiPosition[0]
               });
            });

            // Add pins to the map for each POI
            map.addPins(searchPins, {
               name: searchLayerName,
               cluster: false, 
               icon: "pin-round-darkblue" 
            });

            // Set the camera bounds
            map.setCameraBounds({
               bounds: geojsonResponse.getGeoJsonResults().bbox,
               padding: 50
            );
        });
    ```
5. Uložte soubor **MapSearch.html** a aktualizujte prohlížeč. Teď by se měla zobrazit mapa, v jejímž středu je Seattle, s modrými špendlíky, které označují umístění čerpacích stanic v příslušné oblasti.

   ![Zobrazení mapy s výsledky hledání](./media/tutorial-search-location/pins-map.png)

6. Můžete zobrazit nezpracovaná data, která mapa vykresluje, zadáním následujícího požadavku HTTP v prohlížeči. Nahraďte \<your account key\> svým primárním klíčem.

   ```http
   https://atlas.microsoft.com/search/fuzzy/json?api-version=1.0&query=gasoline%20station&subscription-key=<your account key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

V tomto okamžiku může stránka MapSearch zobrazit umístění bodů zájmu, které se vrátí z dotazu na vyhledání přibližných shod. Teď přidáme několik interaktivních funkcí a další informace o umístěních.

## <a name="add-interactive-data"></a>Přidání interaktivních dat

Mapa, kterou jsme vytvořili, zatím z výsledků hledání používá pouze data o zeměpisné délce a šířce. Pokud se však podíváte na nezpracovaný JSON, který vrací vyhledávací služba Maps, uvidíte, že obsahuje další informace o jednotlivých čerpacích stanicích, včetně názvu a adresy. Tato data můžete zahrnout do mapy s použitím interaktivních, automaticky otevíraných oken.

1. Do bloku *script* přidejte následující řádky, které pro body zájmu vrácené službou Search Service vytvoří automaticky otevíraná okna:

    ```JavaScript
    // Add a popup to the map which will display some basic information about a search result on hover over a pin
    var popup = new atlas.Popup();
    map.addEventListener("mouseover", searchLayerName, (e) => {
        var popupContentElement = document.createElement("div");
        popupContentElement.style.padding = "5px";

        var popupNameElement = document.createElement("div");
        popupNameElement.innerText = e.features[0].properties.name;
        popupContentElement.appendChild(popupNameElement);

        var popupAddressElement = document.createElement("div");
        popupAddressElement.innerText = e.features[0].properties.address;
        popupContentElement.appendChild(popupAddressElement);

        var popupPositionElement = document.createElement("div");
        popupPositionElement.innerText = e.features[0].properties.position;
        popupContentElement.appendChild(popupPositionElement);

        popup.setPopupOptions({
            position: e.features[0].geometry.coordinates,
            content: popupContentElement
        });

        popup.open(map);
    });
    ```
    **atlas.Popup** v rozhraní API poskytuje okno s informacemi ukotvené na požadované pozici na mapě. Tento fragment kódu nastaví obsah a pozici automaticky otevíraného okna. Kromě toho přidá do ovládacího prvku `map` naslouchací proces událostí, který bude čekat na přejetí _myší_ přes automaticky otevírané okno.

2. Uložte soubor a aktualizujte prohlížeč. Teď mapa v prohlížeči při najetí myší na některý ze špendlíků vyhledávání ukazuje automaticky otevíraná okna s informacemi.

    ![Ovládací prvek Mapa a Search Service v Azure](./media/tutorial-search-location/popup-map.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření účtu s Azure Maps
> * Získání primárního klíče pro váš účet
> * Vytvoření nové webové stránky s použitím rozhraní API pro mapové ovládací prvky
> * Vyhledání okolního bodu zájmu pomocí služby Search Service

Přístup k vzorovému kódu pro tento kurz můžete získat tady:

> [Vyhledávání polohy pomocí Azure Maps](https://github.com/Azure-Samples/azure-maps-samples/blob/master/src/search.html)

V dalším kurzu se dozvíte, jak zobrazit trasu mezi dvěma umístěními.

> [!div class="nextstepaction"]
> [Trasa do cíle](./tutorial-route-location.md)

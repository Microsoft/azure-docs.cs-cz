---
title: Vytvořit úložiště pomocí Azure Maps | Dokumentace Microsoftu
description: Vytvořte úložiště s využitím map Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 11/15/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 28c2d65e1b1858b653775b4b298c9ab3e1d31e6e
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55991408"
---
# <a name="create-a-store-locator-by-using-azure-maps"></a>Vytvořit úložiště s využitím Azure Maps

Tento kurz vás provede procesem vytvoření lokátoru jednoduché úložiště s využitím map Azure. Lokátory Store jsou běžné. Mnoho popsaných konceptů, které se používají v tomto typu aplikace platí pro mnoho typů aplikací. Lokátor úložiště nabízí zákazníkům je nezbytnost pro většinu firem, které prodej přímo pro uživatele. V tomto kurzu se naučíte:
    
> [!div class="checklist"]
* Vytvořte novou webovou stránku pomocí rozhraní API ovládacího prvku mapy služby Azure.
* Načíst vlastní data ze souboru a zobrazit na mapě.
* Pomocí služby Azure Search mapy najít adresu nebo zadejte dotaz.
* Získejte podle umístění uživatele z prohlížeče a zobrazit na mapě.
* Kombinovat různé úrovně a vytvářet vlastní symboly na mapě.  
* Cluster datových bodů.  
* Přidejte ovládací prvky Přiblížení mapy.

<a id="Intro"></a>

Přeskočit na článek [úložiště za provozu Lokátor příklad](https://azuremapscodesamples.azurewebsites.net/?sample=Simple%20Store%20Locator) nebo [zdrojový kód](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator). 

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto kurzu, musíte nejprve [vytvoření účtu Azure Maps](./tutorial-search-location.md#createaccount) a [získat klíč předplatného pro váš účet](./tutorial-search-location.md#getkey).

## <a name="design"></a>Návrh

Než přejdete do kódu, je vhodné začít s návrhem. Lokátor vašeho úložiště může být jednoduché nebo složité, jak chcete, aby se. V tomto kurzu vytvoříme jednoduchou obchodů. Můžeme zahrnout některé tipy na cestě k vám pomohla rozšířit některé funkce, pokud budete chtít. Můžeme vytvořit lokátor úložiště pro fiktivní společnosti nazývané Contoso kávu. Následující obrázek znázorňuje obrázek Obecné rozložení obchodů, co vytváříme v tomto kurzu:

<br/>
<center>![Drátový model lokátoru úložiště pro umístění kavárny kávy Contoso](./media/tutorial-create-store-locator/SimpleStoreLocatorWireframe.png)</center>

Pokud chcete maximalizovat užitečnost tento Lokátor úložiště, zahrnujeme přizpůsobivé rozložení, která se přizpůsobí při šířka obrazovky uživatele je menší než 700 pixelů na šířku. Přizpůsobivé rozložení usnadňuje použití obchodů na malé obrazovce, jako je třeba na mobilním zařízení. Tady je obrázek malou obrazovkou rozložení:  

<br/>
<center>![Drátový model Contoso kávy ukládání Lokátor na mobilním zařízení](./media/tutorial-create-store-locator/SimpleStoreLocatorMobileWireframe.png)</center>

Objekty wireframe zobrazit aplikace s poměrně jednoduché. Aplikace obsahuje vyhledávací pole, seznamu blízké úložišť, mapu, která má některé značky (symbolů) a automaticky otevírané okno, které se zobrazí další informace, když uživatel vybere značku. Podrobněji tady jsou funkce, které je neoddělitelnou tento Lokátor úložiště v tomto kurzu:

* Na mapě jsou načteny všechny umístění ze souboru importovaná data oddělený tabulátory.
* Uživatel může posunout Přiblížení mapy, hledání a klikněte na tlačítko Moje GPS umístění.
* Upraví rozložení stránky podle šířka obrazovky zařízení.  
* Záhlaví se zobrazuje logo pro Windows store.  
* Uživatele můžete použít vyhledávací pole a tlačítko hledání vyhledejte umístění, třeba adresy, PSČ nebo město. 
* A `keypress` přidání události do vyhledávacího pole spustí vyhledávání, pokud uživatel stiskne klávesu Enter. Často přehlédnuta tuto funkci, ale vytvoří lepší uživatelské prostředí.
* Pokud se přesune na mapě, se vypočítá vzdálenost do každého umístění v Centru pro mapy. Seznam výsledků se aktualizuje a zobrazí v horní části mapy nejbližší umístění.  
* Když vyberete některý výsledek v seznamu výsledků, mapy je zarovnaný na střed přes do vybraného umístění a v místním okně se zobrazí informace o umístění.  
* Výběrem určitého umístění na mapě, spustí se také automaticky otevírané okno.
* Pokud uživatel Oddálí, umístění jsou seskupené v clusterech. Clustery jsou reprezentovány kroužek s číslem uvnitř kroužku. Clustery formuláře a samostatné, když uživatel provede změny úrovně zvětšení.
* Výběr clusteru na dvou úrovních mapy přiblíží a Vycentruje přes umístění clusteru.

<a id="create a data-set"></a>

## <a name="create-the-store-location-dataset"></a>Vytvořte datovou sadu umístění úložiště

Než vyvíjíme aplikace Lokátor úložiště, musíte vytvořit datové sady úložišť, které chceme zobrazit na mapě. V tomto kurzu používáme datovou sadu pro fiktivní kavárny volá kávy Contoso. Datovou sadu pro tento Lokátor jednoduché úložiště se spravuje v sešitu aplikace Excel. Datová sada obsahuje 10,213 Contoso kávy kavárny umístění rozloženy devíti zemích: USA, Kanadě, Spojeném království, Francie, Německo, Itálie, Nizozemsko, Dánsko a Španělsko. Zde je snímek dat vypadá jako:

<br/>
<center>![Snímek obrazovky dat zjišťování úložiště v sešitu aplikace Excel](./media/tutorial-create-store-locator/StoreLocatorDataSpreadsheet.png)</center>

Je možné [stáhněte si Excelový sešit](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data). 

Podíváme na snímku obrazovky dat, můžeme provádět následující poznámky:
    
* Informace o poloze se uloží s použitím **položce AddressLine**, **Město**, **magistrát** (země), **AdminDivision** (stát/kraj) , **Poštovní směrovací číslo** (PSČ) a **země** sloupce.  
* **Zeměpisná šířka** a **délky** sloupce obsahují souřadnice pro každé umístění kavárny kávy Contoso. Pokud nemáte k dispozici informace souřadnice, slouží k určení umístění souřadnic vyhledávací služby ve službě Azure Maps.
* Některé další sloupce obsahují metadata související v kavárnách: telefonní číslo, logickou sloupce pro Wi-Fi hotspotů a vozíku usnadnění a úložiště otevírání a zavírání časy ve 24hodinovém formátu. Můžete vytvořit vlastní sloupce obsahující metadata, která souvisí s víc dat o poloze.

> [!Note]
> Azure Maps vykreslí data v kulovité projekci Mercator "EPSG:3857", ale čte data v "EPSG:4325" používajících WGS84 datum. 

Existuje mnoho způsobů, jak vystavit datovou sadu, která aplikace. Jedním z přístupů je načíst data do databáze a zpřístupňují webové služby, který dotazuje data a odesílá výsledky do prohlížeče uživatele. Tato možnost je ideální pro velké datové sady nebo datových sad, které se často aktualizují. Tato možnost však vyžaduje podstatně více vývojové práce a má vyšší náklady. 

Další možností je převést na ploché textový soubor, který prohlížeč můžete snadno analyzovat tuto datovou sadu. Samotný soubor je možné hostovat pomocí zbývajících částí aplikace. Tato možnost zajišťují, aby vše jednoduchý, ale je vhodný pouze pro menší datové sady, protože uživatel stáhne všechna data. Používáme plochého souboru pro tuto datovou sadu, protože velikost datového souboru je menší než 1 MB.  

Převést na plochého souboru sešitu, uložte sešit jako soubor s oddělovači na kartě. Každý sloupec je oddělen složenými znak tabulátoru, které díky snadno analyzovat sloupce v našem kódu. Můžete použít formátu hodnot oddělených čárkami (CSV), ale tato možnost vyžaduje další analýzy logiku. Jakékoli pole, které obsahuje čárku kolem něj by být vnořen do uvozovek. Chcete-li tato data exportovat jako soubor odděleného tabulátory v aplikaci Excel, vyberte **uložit jako**. V **uložit jako typ** rozevíracího seznamu vyberte **Text (kartě delimited)(*.txt)**. Název souboru *ContosoCoffee.txt*. 

<br/>
<center>![Snímek obrazovky Uložit jako typ dialogového okna](./media/tutorial-create-store-locator/SaveStoreDataAsTab.png)</center>

Pokud otevřete textový soubor v poznámkovém bloku, vypadá podobně jako na následujícím obrázku:

<br/>
<center>![Snímek obrazovky, který zobrazuje sadu dat oddělený tabulátory soubor poznámkového bloku](./media/tutorial-create-store-locator/StoreDataTabFile.png)</center>


## <a name="set-up-the-project"></a>Nastavení projektu

Chcete-li vytvořit projekt, můžete použít [sady Visual Studio](https://visualstudio.microsoft.com) nebo editor kódu podle vašeho výběru. Ve složce vašeho projektu vytvořit tři soubory: *index.html*, *index.css*, a *index.js*. Tyto soubory definují rozložení, styl a logiku pro aplikace. Vytvořte složku s názvem *data* a přidejte *ContosoCoffee.txt* ke složce. Vytvořte jinou složku s názvem *image*. V této aplikaci používáme deset imagí ikony, tlačítka a značky na mapě. Je možné [stáhnout tyto image](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data). Složky vašeho projektu by měla vypadat jako na následujícím obrázku:

<br/>
<center>![Snímek obrazovky s jednoduchou Store Lokátor složky projektu](./media/tutorial-create-store-locator/StoreLocatorVSProject.png)</center>

## <a name="create-the-user-interface"></a>Vytvoření uživatelského rozhraní

K vytvoření uživatelského rozhraní, přidejte kód pro *index.html*:

1. Přidejte následující `meta` klíčová slova `head` z *index.html*. Značky definují znakové sady (UTF-8), informace aplikace Internet Explorer a Microsoft Edge použít nejnovější verze prohlížeče a zadejte oblast zobrazení, která funguje dobře pro responzivní rozložení.

    ```HTML
    <meta charset="utf-8" /> 
    <meta http-equiv="x-ua-compatible" content="IE=Edge" /> 
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />
    ```

1. Přidání odkazů na Azure Maps webový ovládací prvek jazyka JavaScript a soubory šablon stylů CSS:

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=2" type="text/css" /> 
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=2"></script> 
    ```
    
1. Přidáte odkaz na modul služby Azure Maps. Modul je knihovna JavaScript, která zabalí služby Azure Maps REST a zpřístupňuje je snadno použitelný v jazyce JavaScript. Modul je užitečné pro provozování vyhledávací funkce.

    ```HTML
    <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=2"></script>
    ```
    
1. Přidání odkazů na *index.js* a *index.css*:

    ```HTML
    <link rel="stylesheet" href="index.css" type="text/css" /> 
    <script src="index.js"></script>
    ```
    
1. V textu dokumentu, přidejte `header` značky. Uvnitř `header` značky, přidejte název logo a společnosti.

    ```HTML
    <header> 
        <img src="images/Logo.png" /> 
        <span>Contoso Coffee</span> 
    </header>
    ```

1. Přidat `main` označit a vytvořit panel hledání, který má tlačítko textové pole a hledání. Přidejte také `div` odkazy pro mapy, panel seznamu a tlačítko Moje GPS umístění.

    ```HTML
    <main> 
        <div class="searchPanel"> 
            <div> 
                <input id="searchTbx" type="search" placeholder="Find a store" /> 
                <button id="searchBtn" title="Search"></button> 
            </div> 
        </div> 

        <div id="listPanel"></div> 

        <div id="myMap"></div> 

        <button id="myLocationBtn" title="My Location"></button> 
    </main>
    ```

Jakmile budete hotovi, *index.html* by měl vypadat jako [tento příklad souboru index.html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/index.html).

Dalším krokem je definování stylů CSS. Styly CSS definovat, jak jsou rozloženy komponent aplikace a vzhled vaší aplikace. Otevřít *index.css* a přidejte do ní následující kód. `@media` Styl definuje alternativní styl možnosti pro použití při šířka obrazovky je menší než 700 pixelů.  

```css
    html, body { 
        padding: 0; 
        margin: 0; 
        font-family: Gotham, Helvetica, sans-serif; 
        overflow-x: hidden; 
    } 

    header { 
        width: calc(100vw - 10px); 
        height: 30px; 
        padding: 15px 0 20px 20px; 
        font-size: 25px; 
        font-style: italic; 
        font-family: "Comic Sans MS", cursive, sans-serif; 
        line-height: 30px; 
        font-weight: bold;     
        color: white; 
        background-color: #007faa; 
    } 

    header span { 
        vertical-align: middle; 
    } 

    header img { 
        height: 30px; 
        vertical-align: middle; 
    } 

    .searchPanel { 
        position: relative; 
        width: 350px; 
    } 

        .searchPanel div { 
            padding: 20px; 
        } 

        .searchPanel input { 
            width: calc(100% - 50px); 
            font-size: 16px; 
            border: 0; 
            border-bottom: 1px solid #ccc; 
        } 

    #listPanel { 
        position: absolute; 
        top: 135px; 
        left: 0px; 
        width: 350px; 
        height: calc(100vh - 135px); 
        overflow-y: auto; 
    } 

    #myMap { 
        position: absolute; 
        top: 65px; 
        left: 350px; 
        width: calc(100vw - 350px); 
        height: calc(100vh - 65px); 
    } 

    .statusMessage { 
        margin: 10px; 
    } 

    #myLocationBtn, #searchBtn { 
        margin: 0; 
        padding: 0; 
        border: none; 
        border-collapse: collapse; 
        width: 32px; 
        height: 32px; 
        text-align: center; 
        cursor: pointer; 
        line-height: 32px; 
        background-repeat: no-repeat; 
        background-size: 20px; 
        background-position: center center; 
        z-index: 200;     
    } 

    #myLocationBtn { 
        position: absolute; 
        top: 150px; 
        right: 10px; 
        box-shadow: 0px 0px 4px rgba(0,0,0,0.16); 
        background-color: white; 
        background-image: url("images/GpsIcon.png"); 
    } 

        #myLocationBtn:hover { 
            background-image: url("images/GpsIcon-hover.png"); 
        } 

    #searchBtn { 
        background-color: transparent; 
        background-image: url("images/SearchIcon.png"); 
    } 

        #searchBtn:hover { 
            background-image: url("images/SearchIcon-hover.png"); 
        } 

    .listItem { 
        height: 50px; 
        padding: 20px; 
        font-size: 14px; 
    } 

        .listItem:hover { 
            cursor: pointer; 
            background-color: #f1f1f1; 
        } 

    .listItem-title { 
        color: #007faa; 
        font-weight: bold; 
    } 

    .storePopup { 
        min-width: 150px;   
    } 

        .storePopup .popupTitle { 
            border-top-left-radius: 4px; 
            border-top-right-radius: 4px; 
            padding: 8px; 
            height: 30px; 
            background-color: #007faa; 
            color: white; 
            font-weight: bold; 
        } 

        .storePopup .popupSubTitle { 
            font-size: 10px; 
            line-height: 12px; 
        } 

        .storePopup .popupContent { 
            font-size: 11px; 
            line-height: 18px; 
            padding: 8px; 
        } 

        .storePopup img { 
            vertical-align:middle; 
            height: 12px; 
            margin-right: 5px;     
        } 

    /* Adjust the layout of the page when the screen width is less than 700 pixels. */ 
    @media screen and (max-width: 700px) { 
        .searchPanel { 
            width: 100vw; 
        } 

        #listPanel { 
            top: 385px; 
            width: 100%; 
            height: calc(100vh - 385px); 
        } 

        #myMap { 
            width: 100vw; 
            height: 250px; 
            top: 135px; 
            left: 0px; 
        } 

        #myLocationBtn { 
            top: 220px; 
        } 
    } 

    .mapCenterIcon { 
        display: block; 
        width: 10px; 
        height: 10px; 
        border-radius: 50%; 
        background: orange; 
        border: 2px solid white;     
        cursor: pointer;     
        box-shadow: 0 0 0 rgba(0, 204, 255, 0.4);     
        animation: pulse 3s infinite;     
    } 

    @keyframes pulse { 
        0% {     
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0.4); 
        } 

        70% { 
            box-shadow: 0 0 0 50px rgba(0, 204, 255, 0);     
        } 

        100% { 
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0); 
        } 
    }

```

Při spuštění aplikace nyní zobrazí hlavičku, vyhledávací pole a tlačítko hledání, ale na mapě nejsou viditelná, protože nebyla dosud nebyla načtena. Pokud se pokusíte provést vyhledávání, nic se nestane. Musíme nastavit logiky JavaScript, který je popsaný v další části k přístupu k funkcím úložiště lokátoru.

## <a name="wire-the-application-with-javascript"></a>Propojit aplikaci s použitím jazyka JavaScript

V tomto okamžiku je všechno nastavené v uživatelském rozhraní. Teď potřebujeme přidat JavaScript, který chcete načíst a analyzovat data a potom vykreslit data na mapě. Chcete-li začít, otevřete *index.js* a přidejte kód, jak je popsáno v následujících krocích.

1. Přidání globální možností pro usnadnění nastavení k aktualizaci. Kromě toho definujte proměnné, na mapě, automaticky otevírané okno, zdroji dat, vrstvu ikonu, značku HTML, který zobrazuje středu prohledávaná oblast a instance objektu klienta služby Azure Maps vyhledávání.

    ```Javascript
    //The maximum zoom level to cluster data point data on the map. 
    var maxClusterZoomLevel = 11; 

    //The URL to the store location data. 
    var storeLocationDataUrl = 'data/ContosoCoffee.txt'; 

    //The URL to the icon image. 
    var iconImageUrl = 'images/CoffeeIcon.png'; 
    var map, popup, datasource, iconLayer, centerMarker, searchURL;
    ```

1. Přidejte kód, který *index.js*. Následující kód inicializuje na mapě, přidá [naslouchací proces událostí](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) , který čeká na stránce dokončení načítání, sváže události k monitorování načítání mapy a základem tlačítko hledání a tlačítko umístění. 

  Když uživatel vybere tlačítko Hledat nebo když uživatel stiskne klávesu Enter po zadání umístění, do vyhledávacího pole, je zahájeno vyhledávání přibližných shod s dotazem uživatele. Předání v poli zemi ISO 2 hodnoty `countrySet` možnost omezit rozsah výsledků vyhledávání do těchto zemí. Omezení zemích, vyhledávání pomáhá zvýšit jeho přesnost výsledků, které jsou vráceny. 
  
  Po dokončení vyhledávání se provést první výsledek a nastavení fotoaparátu/kamery mapy přes tuto oblast. Když uživatel vybere tlačítko umístění, použijte rozhraní API zeměpisné polohy HTML5, která je integrována do prohlížeče k načtení podle umístění uživatele a středu mapy přes jejich umístění.  

  > [!Tip]
  > Při použití automaticky otevíraných oken, je nejlepší vytvořit jeden `Popup` instance a znovu použít instanci, aktualizuje její obsah a pozici. Pro každý `Popup`instance je přidat do kódu, více elementů modelu DOM se přidají do stránky. Existuje více elementů modelu DOM se na stránku v prohlížeči se ke sledování více věcí. Pokud existuje příliš mnoho položek, může být pomalé v prohlížeči.

    ```Javascript
    function initialize() { 
        //Initialize a map instance. 
        map = new atlas.Map('myMap', { 
            center: [-90, 40], 
            zoom: 2,

            //Add your Azure Maps subscription key to the map SDK.
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        }); 

        //Create a pop-up window, but leave it closed so we can update it and display it later. 
        popup = new atlas.Popup(); 

        //Use SubscriptionKeyCredential with a subscription key
        const subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());
    
        //Use subscriptionKeyCredential to create a pipeline
        const pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
            retryOptions: { maxTries: 4 }, // Retry options
        });
    
        //Create an instance of the SearchURL client.
        searchURL = new atlas.service.SearchURL(pipeline);

        //If the user selects the search button, geocode the value the user passed in. 
        document.getElementById('searchBtn').onclick = performSearch; 

        //If the user presses Enter in the search box, perform a search. 
        document.getElementById('searchTbx').onkeyup = function (e) {
            if (e.keyCode === 13) { 
                performSearch(); 
            } 
        }; 

        //If the user selects the My Location button, use the Geolocation API to get the user's location. Center and zoom the map on that location. 
        document.getElementById('myLocationBtn').onclick = setMapToUserLocation; 

        //Wait until the map resources are ready.
        map.events.add('ready', function () { 

        //Add your post-map load functionality. 

        }); 
    } 

    //Create an array of country ISO 2 values to limit searches to. 
    var countrySet = ['US', 'CA', 'GB', 'FR','DE','IT','ES','NL','DK'];       

    function performSearch() { 
        var query = document.getElementById('searchTbx').value; 

        //Perform a fuzzy search on the users query.
        searchURL.searchFuzzy(atlas.service.Aborter.timeout(3000), query, {
            //Pass in the array of country ISO2 for which we want to limit the search to.
            countrySet: countrySet
        }).then(results => {
            //Parse the response into GeoJSON so that the map can understand.
            var data = results.geojson.getFeatures();
    
            if (data.features.length > 0) {
                //Set the camera to the bounds of the results.
                map.setCamera({
                    bounds: data.features[0].bbox,
                    padding: 40
                });
            } else {
                document.getElementById('listPanel').innerHTML = '<div class="statusMessage">Unable to find the location you searched for.</div>';
            } 
        });
    } 

    function setMapToUserLocation() { 
        //Request the user's location. 
        navigator.geolocation.getCurrentPosition(function (position) { 
            //Convert the Geolocation API position to a longitude and latitude position value that the map can interpret and center the map over it. 
            map.setCamera({ 
                center: [position.coords.longitude, position.coords.latitude], 
                zoom: maxClusterZoomLevel + 1 
            }); 
        }, function (error) { 
            //If an error occurs when the API tries to access the user's position information, display an error message. 
            switch (error.code) { 
                case error.PERMISSION_DENIED: 
                    alert('User denied the request for geolocation.'); 
                    break; 
                case error.POSITION_UNAVAILABLE: 
                    alert('Position information is unavailable.'); 
                    break; 
                case error.TIMEOUT: 
                    alert('The request to get user position timed out.'); 
                    break; 
                case error.UNKNOWN_ERROR: 
                    alert('An unknown error occurred.'); 
                    break; 
            } 
        }); 
    } 

    //Initialize the application when the page is loaded. 
    window.onload = initialize;
    ```

1. Na mapě `load` naslouchací proces událostí, přidejte ovládací prvek Lupa a značku HTML zobrazení center prohledávaná oblast.

    ```Javascript
    //Add a zoom control to the map. 
    map.controls.add(new atlas.control.ZoomControl(), { 
        position: 'top-right'
    }); 

    //Add an HTML marker to the map to indicate the center to use for searching. 
    centerMarker = new atlas.HtmlMarker({ 
        htmlContent: '<div class="mapCenterIcon"></div>', 
        position: map.getCamera().center 
    });

    map.markers.add(centerMarker);
    ```

1. Na mapě `load` naslouchací proces událostí, přidat zdroj dat. Proveďte volání načíst a analyzovat datovou sadu. Zapnutí clusterování ve zdroji dat. Clustering pro data zdrojové skupiny překrývající se body v clusteru. Samostatné clustery do jednotlivých bodů jako uživatel přiblíží. To umožňuje více plynulé uživatelské prostředí a zvyšuje výkon.

    ```Javascript
    //Create a data source, add it to the map, and then enable clustering. 
    datasource = new atlas.source.DataSource(null, { 
        cluster: true, 
        clusterMaxZoom: maxClusterZoomLevel - 1 
    }); 

    map.sources.add(datasource); 

    //Load all the store data now that the data source is defined.  
    loadStoreData();
    ```

1. Po načtení datové sady v objektu map `load` naslouchací proces událostí, definují sadu vrstvy vykreslit data. Vrstva bublinu slouží k vykreslení Clusterované datových bodů. Vrstva symbol se použije k vykreslení, kolik bodů v každém clusteru nad bublinu vrstvou. Druhá vrstva symbol vykreslí vlastní ikonu u jednotlivých lokalit na mapě. 

  Přidat `mouseover` a `mouseout` události na bublinu a Ikona vrstvy k provedení změny kurzoru myši, když uživatel najede myší na ikonu na mapě nebo clusteru. Přidat `click` událostí na bublinu vrstvy clusteru. To `click` události Přiblížení mapy ve dvou úrovních a centra mapy v clusteru, když uživatel vybere jakéhokoli jiného clusteru. Přidat `click` událostí k vrstvě ikonu. To `click` události se zobrazí automaticky otevírané okno zobrazující podrobnosti kavárny, když uživatel vybere ikonu jednotlivých umístění. Přidejte událost do mapy ke sledování po dokončení přesunutí na mapě. Když se tato událost aktivuje, aktualizujte položky v panelu seznamu.  

    ```Javascript
    //Create a bubble layer to render clustered data points. 
    var clusterBubbleLayer = new atlas.layer.BubbleLayer(datasource, null, { 
        radius: 12, 
        color: '#007faa', 
        strokeColor: 'white', 
        strokeWidth: 2, 
        filter: ['has', 'point_count'] //Only render data points that have a point_count property; clusters have this property. 
    }); 

    //Create a symbol layer to render the count of locations in a cluster. 
    var clusterLabelLayer = new atlas.layer.SymbolLayer(datasource, null, { 
        iconOptions: { 
            image: 'none' //Hide the icon image. 
        }, 
        
        textOptions: { 
            textField: '{point_count_abbreviated}', 
            size: 12, 
            font: ['StandardFont-Bold'], 
            offset: [0, 0.4], 
            color: 'white' 
        } 
    }); 

    map.layers.add([clusterBubbleLayer, clusterLabelLayer]); 

    //Load a custom image icon into the map resources.     
    map.imageSprite.add('myCustomIcon', iconImageUrl).then(function () {            

    //Create a layer to render a coffee cup symbol above each bubble for an individual location. 
    iconLayer = new atlas.layer.SymbolLayer(datasource, null, { 
        iconOptions: { 
            //Pass in the ID of the custom icon that was loaded into the map resources. 
            image: 'myCustomIcon', 

            //Optionally, scale the size of the icon. 
            font: ['SegoeUi-Bold'], 

            //Anchor the center of the icon image to the coordinate. 
            anchor: 'center', 

            //Allow the icons to overlap. 
            allowOverlap: true 
        }, 

        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer. 
    }); 

    map.layers.add(iconLayer); 

    //When the mouse is over the cluster and icon layers, change the cursor to a pointer. 
    map.events.add('mouseover', [clusterBubbleLayer, iconLayer], function () { 
        map.getCanvasContainer().style.cursor = 'pointer'; 
    }); 

    //When the mouse leaves the item on the cluster and icon layers, change the cursor back to the default (grab). 
    map.events.add('mouseout', [clusterBubbleLayer, iconLayer], function () { 
        map.getCanvasContainer().style.cursor = 'grab'; 
    }); 

    //Add a click event to the cluster layer. When the user selects a cluster, zoom into it by two levels.  
    map.events.add('click', clusterBubbleLayer, function (e) { 
        map.setCamera({ 
            center: e.position, 
            zoom: map.getCamera().zoom + 2 
        }); 
    }); 

    //Add a click event to the icon layer and show the shape that was selected. 
    map.events.add('click', iconLayer, function (e) { 
        showPopup(e.shapes[0]); 
    }); 

    //Add an event to monitor when the map is finished rendering the map after it has moved. 
    map.events.add('render', function () { 
        //Update the data in the list. 
        updateListItems(); 
    });
    ```

1. Při načtení dataset kavárny, je nutné nejprve stáhnout. Textový soubor pak, musí se rozdělit do řádků. První řádek obsahuje informace záhlaví. Chcete-li kód usnadňuje její sledování, se nám analyzovat hlavičku do objektu, který pak můžete použít k vyhledání buňky index každé vlastnosti. Po první řádek projít zbývající řádky a vytvoření bodu funkce. Přidáte funkci bodu ke zdroji dat. Nakonec aktualizujte panel seznamu.

    ```Javascript
    function loadStoreData() { 

    //Download the store location data. 
    fetch(storeLocationDataUrl)     
        .then(response => response.text()) 
        .then(function (text) { 

            //Parse the tab-delimited file data into GeoJSON features. 
            var features = []; 

            //Split the lines of the file. 
            var lines = text.split('\n'); 

            //Grab the header row. 
            var row = lines[0].split('\t'); 

            //Parse the header row and index each column to make the code for parsing each row easier to follow. 
            var header = {}; 
            var numColumns = row.length; 
            for (var i = 0; i < row.length; i++) { 
                header[row[i]] = i; 
            } 

            //Skip the header row and then parse each row into a GeoJSON feature. 
            for (var i = 1; i < lines.length; i++) { 
                row = lines[i].split('\t'); 

                //Ensure that the row has the correct number of columns. 
                if (row.length >= numColumns) { 

                    features.push(new atlas.data.Feature(new atlas.data.Point([parseFloat(row[header['Longitude']]), parseFloat(row[header['Latitude']])]), { 
                        AddressLine: row[header['AddressLine']], 
                        City: row[header['City']], 
                        Municipality: row[header['Municipality']], 
                        AdminDivision: row[header['AdminDivision']], 
                        Country: row[header['Country']], 
                        PostCode: row[header['PostCode']], 
                        Phone: row[header['Phone']], 
                        StoreType: row[header['StoreType']], 
                        IsWiFiHotSpot: (row[header['IsWiFiHotSpot']].toLowerCase() === 'true') ? true : false, 
                        IsWheelchairAccessible: (row[header['IsWheelchairAccessible']].toLowerCase() === 'true') ? true : false, 
                        Opens: parseInt(row[header['Opens']]), 
                        Closes: parseInt(row[header['Closes']]) 
                    })); 
                } 
            } 

            //Add the features to the data source. 
            datasource.add(new atlas.data.FeatureCollection(features)); 

            //Initially, update the list items. 
            updateListItems(); 
        }); 
    }
    ```

1. Při aktualizaci seznamu panel se vypočítá vzdálenost od středu mapy ke všem funkcím bodu v aktuální zobrazení mapy. Funkce se potom seřazeno podle vzdálenosti. HTML se vygeneruje pro zobrazení jednotlivých umístěních v panelu seznamu.

    ```Javascript
    var listItemTemplate = '<div class="listItem" onclick="itemSelected(\'{id}\')"><div class="listItem-title">{title}</div>{city}<br />Open until {closes}<br />{distance} miles away</div>'; 

    function updateListItems() { 
        //Hide the center marker.
        centerMarker.setOptions({
            visible: false
        });

        //Get the current camera and view information for the map. 
        var camera = map.getCamera(); 
        var listPanel = document.getElementById('listPanel'); 

        //Get all the shapes that have been rendered in the bubble layer.  
        var data = map.layers.getRenderedShapes(map.getCamera().bounds, [iconLayer]); 

        data.forEach(function (shape) { 
            if (shape instanceof atlas.Shape) { 
                //Calculate the distance from the center of the map to each shape, and then store the data in a distance property.  
                shape.distance = atlas.math.getDistanceTo(camera.center, shape.getCoordinates(), 'miles'); 
            } 
        }); 

        //Sort the data by distance. 
        data.sort(function (x, y) { 
            return x.distance - y.distance; 
        }); 

        //Check to see whether the user is zoomed out a substantial distance. If they are, tell the user to zoom in and to perform a search or select the My Location button. 
        if (camera.zoom < maxClusterZoomLevel) { 
            //Close the pop-up window; clusters might be displayed on the map.  
            popup.close(); 
            listPanel.innerHTML = '<div class="statusMessage">Search for a location, zoom the map, or select the My Location button to see individual locations.</div>'; 
        } else { 
            //Update the location of the centerMarker property. 
            centerMarker.setOptions({ 
                position: camera.center, 
                visible: true 
            }); 

            //List the ten closest locations in the side panel. 
            var html = [], properties; 

            /* 
            Generating HTML for each item that looks like this: 
            <div class="listItem" onclick="itemSelected('id')"> 
                <div class="listItem-title">1 Microsoft Way</div> 
                Redmond, WA 98052<br /> 
                Open until 9:00 PM<br /> 
                0.7 miles away 
            </div> 
            */ 

            data.forEach(function (shape) { 
                properties = shape.getProperties(); 
                html.push('<div class="listItem" onclick="itemSelected(\'', shape.getId(), '\')"><div class="listItem-title">', 
                properties['AddressLine'], 
                '</div>', 
                //Get a formatted addressLine2 value that consists of City, Municipality, AdminDivision, and PostCode. 
                getAddressLine2(properties), 
                '<br />', 

                //Convert the closing time to a format that is easier to read. 
                getOpenTillTime(properties), 
                '<br />', 

                //Route the distance to two decimal places.  
                (Math.round(shape.distance * 100) / 100), 
                ' miles away</div>'); 
            }); 

            listPanel.innerHTML = html.join(''); 

            //Scroll to the top of the list panel in case the user has scrolled down. 
            listPanel.scrollTop = 0; 
        } 
    } 

    //This converts a time that's in a 24-hour format to an AM/PM time or noon/midnight string. 
    function getOpenTillTime(properties) { 
        var time = properties['Closes']; 
        var t = time / 100; 
        var sTime; 

        if (time === 1200) { 
            sTime = 'noon'; 
        } else if (time === 0 || time === 2400) { 
            sTime = 'midnight'; 
        } else {     
            sTime = Math.round(t) + ':'; 

            //Get the minutes. 
            t = (t - Math.round(t)) * 100; 

            if (t === 0) { 
                sTime += '00'; 
            } else if (t < 10) { 
                sTime += '0' + t; 
            } else { 
                sTime += Math.round(t); 
            } 

            if (time < 1200) { 
                sTime += ' AM'; 
            } else { 
                sTime += ' PM'; 
            } 
        } 

        return 'Open until ' + sTime; 
    } 

    //Create an addressLine2 string that contains City, Municipality, AdminDivision, and PostCode. 
    function getAddressLine2(properties) { 
        var html = [properties['City']]; 

        if (properties['Municipality']) { 
            html.push(', ', properties['Municipality']); 
        } 

        if (properties['AdminDivision']) { 
            html.push(', ', properties['AdminDivision']); 
        } 

        if (properties['PostCode']) { 
            html.push(' ', properties['PostCode']); 
        } 

        return html.join(''); 
    }
    ```

1. Když uživatel vybere položku v seznamu panelu, tvar, ke kterému se vztahuje položky je načíst ze zdroje dat. Automaticky otevírané okno se vygeneruje, který je založen na informace o vlastnosti uložené ve tvaru. Mapa je zarovnaný na střed nad tvar. Pokud mapa je menší než 700 pixelů na šířku, je posunutí zobrazení mapy, v automaticky otevíraném okně je zobrazen.

    ```Javascript
    //When a user selects a result in the side panel, look up the shape by its ID value and display the pop-up window. 
    function itemSelected(id) { 
        //Get the shape from the data source by using its ID.  
        var shape = datasource.getShapeById(id); 
        showPopup(shape); 

        //Center the map over the shape on the map. 
        var center = shape.getCoordinates(); 
        var offset;
    
        //If the map is less than 700 pixels wide, then the layout is set for small screens.
        if (map.getCanvas().width < 700) {
            //When the map is small, offset the center of the map relative to the shape so that there is room for the popup to appear.
            offset = [0, -80];
        }
    
        map.setCamera({
            center: center,
            centerOffset: offset
        });
    } 

    function showPopup(shape) { 
        var properties = shape.getProperties(); 

        /* Generating HTML for the pop-up window that looks like this: 

            <div class="storePopup"> 
                <div class="popupTitle"> 
                    3159 Tongass Avenue 
                    <div class="popupSubTitle">Ketchikan, AK 99901</div> 
                </div> 
                <div class="popupContent"> 
                    Open until 22:00 PM<br/> 
                    <img title="Phone Icon" src="images/PhoneIcon.png"> 
                    <a href="tel:1-800-XXX-XXXX">1-800-XXX-XXXX</a> 
                    <br>Amenities: 
                    <img title="Wi-Fi Hotspot" src="images/WiFiIcon.png"> 
                    <img title="Wheelchair Accessible" src="images/WheelChair-small.png"> 
                </div> 
            </div> 
        */ 

        var html = ['<div class="storePopup">']; 
        html.push('<div class="popupTitle">', 
            properties['AddressLine'], 
            '<div class="popupSubTitle">', 
            getAddressLine2(properties), 
            '</div></div><div class="popupContent">', 

            //Convert the closing time to a format that's easier to read. 
            getOpenTillTime(properties), 

            //Route the distance to two decimal places.  
            '<br/>', (Math.round(shape.distance * 100) / 100), 
            ' miles away', 
            '<br /><img src="images/PhoneIcon.png" title="Phone Icon"/><a href="tel:', 
            properties['Phone'], 
            '">',  
            properties['Phone'], 
            '</a>' 
        ); 

        if (properties['IsWiFiHotSpot'] || properties['IsWheelchairAccessible']) { 
            html.push('<br/>Amenities: '); 
            
            if (properties['IsWiFiHotSpot']) { 
                html.push('<img src="images/WiFiIcon.png" title="Wi-Fi Hotspot"/>') 
            } 

            if (properties['IsWheelchairAccessible']) { 
                html.push('<img src="images/WheelChair-small.png" title="Wheelchair Accessible"/>') 
            } 
        } 

        html.push('</div></div>'); 

        //Update the content and position of the pop-up window for the specified shape information. 
        popup.setOptions({ 

            //Create a table from the properties in the feature. 
            content:  html.join(''),     
            position: shape.getCoordinates() 
        }); 

        //Open the pop-up window. 
        popup.open(map); 
    }
    ```

Teď máte plně funkční obchodů. Ve webovém prohlížeči otevřete *index.html* souboru pro Lokátor úložiště. Když clustery se zobrazí na mapě, můžete vyhledat umístění pomocí vyhledávacího pole, výběrem tlačítka pro umístění, tak, že vyberete cluster nebo přiblížení na mapě zobrazit umístění jednotlivých.

Při prvním uživatel vybere tlačítko umístění prohlížeč zobrazí upozornění zabezpečení, která požádá o oprávnění k přístupu k umístění uživatele. Pokud uživatel souhlasí sdílet svoje umístění, mapy přiblíží podle umístění uživatele a jsou uvedeny blízké v kavárnách. 

<br/>
<center>![Snímek obrazovky prohlížeče uživatele požádat o přístup k umístění uživatele](./media/tutorial-create-store-locator/GeolocationApiWarning.png)</center>

Při přiblížení blízko v oblasti, která má kavárny umístění, oddělení clusterů do jednotlivých umístění. Vyberte jednu z ikon na mapě nebo vyberte položku v postranní panel zobrazíte automaticky otevírané okno s informacemi pro danou lokaci.

<br/>
<center>![Snímek obrazovky dokončené úložiště lokátoru](./media/tutorial-create-store-locator/FinishedSimpleStoreLocator.png)</center>

Pokud změníte velikost okna prohlížeče na méně než 700 pixelů na šířku nebo na mobilním zařízení otevřete aplikaci, změnami rozložení lepší vhodné pro menší obrazovky. 

<br/>
<center>![Snímek obrazovky s malou obrazovkou verzi Lokátor úložiště](./media/tutorial-create-store-locator/FinishedSimpleStoreLocatorSmallScreen.png)</center>

## <a name="next-steps"></a>Další postup

V tomto kurzu se dozvíte, jak chcete vytvořit lokátor základní úložiště s využitím map Azure. Lokátor úložiště, které vytvoříte v tomto kurzu může mít všechny funkce, které potřebujete. Můžete přidat funkce do vašeho úložiště Lokátor nebo používat další funkce zálohy pro další vlastní uživatelské prostředí: 

> [!div class="checklist"]
* Povolit [návrhy při psaní](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20Autosuggest%20and%20JQuery%20UI) do vyhledávacího pole.  
* Přidat [podporu více jazyků](https://azuremapscodesamples.azurewebsites.net/?sample=Map%20Localization). 
* Povolí uživateli [filtrovat umístění trase](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Data%20Along%20Route). 
* Přidat možnost [nastavit filtry](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Symbols%20by%20Property). 
* Přidání podpory pro zadejte hodnotu počáteční vyhledávání pomocí řetězce dotazu. Pokud vaše úložiště Lokátor zahrnout tuto možnost, můžete uložit do oblíbených uživatelů a sdílet hledání. Také poskytuje snadný způsob předat hledání na tuto stránku z jiné stránky.  
* Nasazení vaší Lokátor úložiště jako [webové aplikaci Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html). 
* Store vašich dat v databázi a vyhledat nejbližší umístění. Další informace najdete v tématu [prostorovými daty formátu SQL Server typy přehled](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-types-overview?view=sql-server-2017) a [prostorová data dotázat na nejbližší soused](https://docs.microsoft.com/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor?view=sql-server-2017).

Přístup k vzorovému kódu pro tento kurz můžete získat tady:

> [Vytvořit úložiště s využitím Azure Maps](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator)

[Podívejte se na živou ukázku](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Simple%20Store%20Locator)

Další informace o pokrytí a možnostech Azure Maps:

> [!div class="nextstepaction"]
> [Úrovně přiblížení a mřížka dlaždic](zoom-levels-and-tile-grid.md)

Další příklady kódu a prostředí pro interaktivní psaní kódu:

> [!div class="nextstepaction"]
> [Jak používat mapový ovládací prvek](how-to-use-map-control.md)

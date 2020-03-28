---
title: 'Kurz: Vytvoření aplikace pro vyhledávače obchodů pomocí Azure Maps | Mapy Microsoft Azure'
description: V tomto kurzu se dozvíte, jak vytvořit webovou aplikaci lokátoru obchodu pomocí sady Microsoft Azure Maps web SDK.
author: philmea
ms.author: philmea
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 49b57b213a452d6c594bbc1ca537e68bd7a83864
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333845"
---
# <a name="tutorial-create-a-store-locator-by-using-azure-maps"></a>Kurz: Vytvoření lokátoru obchodu pomocí Azure Maps

Tento kurz vás provede procesem vytváření jednoduchého lokátoru úložiště pomocí Azure Maps. Lokátory obchodu jsou běžné. Mnoho konceptů, které se používají v tomto typu aplikace jsou použitelné pro mnoho jiných typů aplikací. Nabídka vyhledávače obchodů zákazníkům je nutností pro většinu podniků, které prodávají přímo spotřebitelům. V tomto kurzu se naučíte:
    
> [!div class="checklist"]
> * Vytvořte novou webovou stránku pomocí rozhraní Azure Map Control API.
> * Načtěte vlastní data ze souboru a zobrazte je na mapě.
> * Pomocí služby Azure Maps Search vyhledejte adresu nebo zadejte dotaz.
> * Získejte polohu uživatele z prohlížeče a zobrazte ji na mapě.
> * Zkombinujte více vrstev a vytvořte na mapě vlastní symboly.  
> * Datové body clusteru.  
> * Přidejte do mapy ovládací prvky lupy.

<a id="Intro"></a>

Přejděte na [příklad lokátoru živého obchodu](https://azuremapscodesamples.azurewebsites.net/?sample=Simple%20Store%20Locator) nebo zdrojový [kód](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator). 

## <a name="prerequisites"></a>Požadavky

Chcete-li provést kroky v tomto kurzu, musíte nejprve vytvořit účet Azure Maps a získat primární klíč (klíč předplatného). Postupujte podle pokynů v [tématu Vytvoření účtu](quick-demo-map-app.md#create-an-account-with-azure-maps) k vytvoření předplatného účtu Azure Maps s cenovou úrovní S1 a postupujte podle pokynů v [tématu získání primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account) k získání primárního klíče pro váš účet. Další informace o ověřování v Azure Maps najdete v [tématu správa ověřování v Azure Maps](how-to-manage-authentication.md).

## <a name="design"></a>Návrh

Než přejdete do kódu, je vhodné začít s návrhem. Lokátor obchodu může být tak jednoduchý nebo složitý, jak chcete. V tomto kurzu vytvoříme jednoduchý vyhledávač obchodů. Na cestě obsahujeme několik tipů, které vám pomohou rozšířit některé funkce, pokud se rozhodnete. Vytvoříme vyhledávač obchodů pro fiktivní společnost contoso coffee. Následující obrázek znázorňuje drátový model obecného rozložení lokátoru obchodu, který vytváříme v tomto kurzu:

<center>

![Drátěný model aplikace lokátoru obchodu pro umístění kavárny Contoso](./media/tutorial-create-store-locator/SimpleStoreLocatorWireframe.png)</center>

Abychom maximalizovali užitečnost tohoto vyhledávače úložiště, zahrnujeme responzivní rozložení, které upravuje šířku obrazovky uživatele menší než 700 pixelů. Responzivní rozložení usnadňuje použití lokátoru obchodu na malé obrazovce, například na mobilním zařízení. Zde je drátový model rozložení malé obrazovky:  

<center>

![Drátový model aplikace lokátoru kavárny Contoso na mobilním zařízení](./media/tutorial-create-store-locator/SimpleStoreLocatorMobileWireframe.png)</center>

Drátové modely ukazují poměrně přímočarou aplikaci. Aplikace má vyhledávací pole, seznam blízkých obchodů a mapu, která má některé značky, například symboly. A má vyskakovací okno, které zobrazuje další informace, když uživatel vybere značku. Podrobněji zde jsou funkce, které začleňujeme do tohoto vyhledávače obchodů v tomto kurzu:

* Všechna umístění z importovaného datového souboru odděleného tabulátorem jsou načtena na mapě.
* Uživatel může mapu posouvat a zvětšovat, provádět vyhledávání a vybírat tlačítko GPS s funkcí Moje poloha.
* Rozložení stránky se upraví podle šířky obrazovky zařízení.  
* Záhlaví zobrazuje logo obchodu.  
* Uživatel může použít vyhledávací pole a tlačítko vyhledávání k vyhledání místa, například adresy, PSČ nebo města. 
* Událost `keypress` přidaná do vyhledávacího pole spustí hledání, pokud uživatel stiskne klávesu Enter. Tato funkce je často přehlížena, ale vytváří lepší uživatelské prostředí.
* Když se mapa přesune, vypočítá se vzdálenost ke každému místu od středu mapy. Seznam výsledků je aktualizován tak, aby zobrazoval nejbližší umístění v horní části mapy.  
* Když vyberete výsledek v seznamu výsledků, mapa se vystředí nad vybraným umístěním a informace o umístění se zobrazí v rozbalovacím okně.  
* Výběr konkrétního umístění na mapě také spustí vyskakovací okno.
* Když uživatel oddálí umístění jsou seskupeny do clusterů. Clustery jsou reprezentovány kruhem s číslem uvnitř kruhu. Clustery se formují a oddělují, když uživatel změní úroveň přiblížení.
* Výběr clusteru přiblíží mapu o dvě úrovně a středy nad umístěním clusteru.

<a id="create a data-set"></a>

## <a name="create-the-store-location-dataset"></a>Vytvoření datové sady umístění úložiště

Než vyvineme aplikaci pro vyhledávač e-store, musíme vytvořit datovou sadu úložišť, které chceme zobrazit na mapě. V tomto kurzu používáme datovou sadu pro fiktivní kavárnu s názvem Contoso Coffee. Datová sada pro tento jednoduchý lokátor úložiště je spravována v sešitu aplikace Excel. Datová sada obsahuje 10 213 míst v kavárně Contoso Coffee, která jsou rozmístěna v devíti zemích nebo oblastech: Ve Spojených státech, Kanadě, Velké Británii, Francii, Německu, Itálii, Nizozemsku, Dánsku a Španělsku. Zde je snímek obrazovky s tím, jak data vypadají:

<center>

![Snímek obrazovky s daty lokátoru obchodu v excelovém sešitu](./media/tutorial-create-store-locator/StoreLocatorDataSpreadsheet.png)</center>

Můžete [si stáhnout excelový sešit](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data). 

Při pohledu na snímek dat můžeme provést následující pozorování:
    
* Informace o poloze se ukládají pomocí sloupců **AddressLine**, **City**, **Municipality** (county), **AdminDivision** (stát/provincie), **PSČ** (PSČ) a **Země.**  
* Sloupce **Zeměpisná šířka** a **zeměpisná délka** obsahují souřadnice pro každé umístění kavárny Contoso Coffee. Pokud nemáte informace o souřadnicích, můžete použít vyhledávací služby v Azure Maps k určení souřadnic umístění.
* Některé další sloupce obsahují metadata související s kavárnami: telefonní číslo, logické sloupce a časy otevírání a zavírání v obchodě ve 24hodinovém formátu. Logické sloupy jsou určeny pro Wi-Fi a bezbariérový přístup. Můžete vytvořit vlastní sloupce, které obsahují metadata, která jsou relevantnější pro vaše údaje o poloze.

> [!Note]
> Azure Maps vykresluje data v projekci sférického Mercatoru "EPSG:3857", ale čte data v "EPSG:4325", které používají základnu WGS84. 

Existuje mnoho způsobů, jak vystavit datovou sadu do aplikace. Jedním z přístupů je načíst data do databáze a vystavit webové služby, která dotazy data. Výsledky pak můžete odeslat do prohlížeče uživatele. Tato možnost je ideální pro velké datové sady nebo pro datové sady, které jsou často aktualizovány. Tato možnost však vyžaduje více vývojové práce a má vyšší náklady. 

Dalším přístupem je převést tuto datovou sadu do plochého textového souboru, který může prohlížeč snadno analyzovat. Samotný soubor může být hostován se zbytkem aplikace. Tato možnost udržuje věci jednoduché, ale je to dobrá volba pouze pro menší datové sady, protože uživatel stáhne všechna data. Pro tuto datovou sadu používáme plochý textový soubor, protože velikost datového souboru je menší než 1 MB.  

Chcete-li sešit převést na plochý textový soubor, uložte jej jako soubor oddělený tabulátorem. Každý sloupec je oddělen znakem karty, což usnadňuje analýzu sloupců v našem kódu. Můžete použít formát hodnoty oddělené čárkami (CSV), ale tato možnost vyžaduje další logiku analýzy. Jakékoli pole, které má kolem něj čárku, by bylo zabaleno do uvozovek. Chcete-li tato data exportovat jako soubor oddělený tabulátorem v excelu, vyberte **Uložit jako**. V rozevíracím seznamu **Uložit jako typ** vyberte Text **(oddělte tabulátor)(*.txt).** Pojmenujte soubor *ContosoCoffee.txt*. 

<center>

![Snímek obrazovky s dialogovým oknem Uložit jako typ](./media/tutorial-create-store-locator/SaveStoreDataAsTab.png)</center>

Pokud otevřete textový soubor v poznámkovém bloku, vypadá podobně jako na následujícím obrázku:

<center>

![Snímek obrazovky se souborem poznámkového bloku, který zobrazuje datovou sadu oddělenou tabulátorem](./media/tutorial-create-store-locator/StoreDataTabFile.png)</center>


## <a name="set-up-the-project"></a>Nastavení projektu

Chcete-li vytvořit projekt, můžete použít [Visual Studio](https://visualstudio.microsoft.com) nebo editor kódu podle vašeho výběru. Ve složce projektu vytvořte tři soubory: *index.html*, *index.css*a *index.js*. Tyto soubory definují rozložení, styl a logiku pro aplikaci. Vytvořte složku s názvem *data* a přidejte do ní *soubor ContosoCoffee.txt.* Vytvořte další složku s názvem *obrázky*. V této aplikaci používáme 10 obrázků pro ikony, tlačítka a značky na mapě. Zde si můžete [stáhnout tyto obrázky](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data). Složka projektu by nyní měla vypadat na následujícím obrázku:

<center>

![Snímek obrazovky složky projektu Aplikace Simple Store Locator](./media/tutorial-create-store-locator/StoreLocatorVSProject.png)</center>

## <a name="create-the-user-interface"></a>Vytvoření uživatelského rozhraní

Chcete-li vytvořit uživatelské rozhraní, přidejte kód do *souboru index.html*:

1. Přidejte `meta` následující značky do `head` *souboru index.html*. Značka `charset` definuje znakovou sadu (UTF-8). Hodnota aplikace `http-equiv` Internet Explorer a Microsoft Edge informuje o použití nejnovějších verzí prohlížeče. A poslední `meta` značka určuje výřez, který funguje dobře pro responzivní rozložení.

    ```HTML
    <meta charset="utf-8">
    <meta http-equiv="x-ua-compatible" content="IE=Edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

1. Přidejte odkazy na soubory JavaScriptu a CSS webového ovládacího prvku Azure Maps:

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

1. Přidejte odkaz na modul Azure Maps Services. Modul je javascriptová knihovna, která zabalí služby Azure Maps REST a usnadňuje jejich použití v JavaScriptu. Modul je užitečný pro napájení funkcí vyhledávání.

    ```HTML
    <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
    ```

1. Přidejte odkazy na *index index.js* a *index.css*:

    ```HTML
    <link rel="stylesheet" href="index.css" type="text/css">
    <script src="index.js"></script>
    ```

1. V textu dokumentu přidejte `header` značku. Do `header` značky přidejte logo a název společnosti.

    ```HTML
    <header>
        <img src="images/Logo.png" />
        <span>Contoso Coffee</span>
    </header>
    ```

1. Přidejte `main` značku a vytvořte vyhledávací panel s textovým polem a tlačítkem hledání. Přidejte `div` také odkazy na mapu, panel seznamu a tlačítko GPS moje poloha.

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

Po dokončení by měl *index.html* vypadat jako [tento příklad souboru index.html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/index.html).

Dalším krokem je definování stylů CSS. Styly CSS definují, jak jsou rozloženy součásti aplikace a vzhled aplikace. Otevřete *index.css* a přidejte do něj následující kód. Styl `@media` definuje alternativní volby stylu, které se mají použít, když je šířka obrazovky menší než 700 obrazových bodů.  

   ```CSS
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

Spusťte aplikaci nyní, uvidíte záhlaví, vyhledávací pole a tlačítko hledání. Ale mapa není viditelná, protože ještě nebyla načtena. Pokud se pokusíte provést vyhledávání, nic se nestane. Musíme nastavit javascriptovou logiku, která je popsána v další části. Tato logika přistupuje ke všem funkcím lokátoru úložiště.

## <a name="wire-the-application-with-javascript"></a>Kabel aplikace pomocí JavaScriptu

Vše je nyní nastaveno v uživatelském rozhraní. Stále potřebujeme přidat JavaScript pro načtení a analýzu dat a následné vykreslení dat na mapě. Chcete-li začít, otevřete *index.js* a přidejte do něj kód, jak je popsáno v následujících krocích.

1. Přidejte globální možnosti, které usnadní aktualizaci nastavení. Definujte proměnné pro mapu, vyskakovací okno, zdroj dat, vrstvu ikon a značku HTML. Nastavte značku HTML tak, aby označovala střed oblasti hledání. A definujte instanci klienta vyhledávací služby Azure Maps.

    ```JavaScript
    //The maximum zoom level to cluster data point data on the map.
    var maxClusterZoomLevel = 11;

    //The URL to the store location data.
    var storeLocationDataUrl = 'data/ContosoCoffee.txt';

    //The URL to the icon image.
    var iconImageUrl = 'images/CoffeeIcon.png';
    var map, popup, datasource, iconLayer, centerMarker, searchURL;
    ```

1. Přidejte kód do *souboru index.js*. Následující kód inicializuje mapu. Přidali jsme [naslouchací proces události,](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) abychom počkali, až se stránka načte. Poté jsme napojili události, abychom sledovali načítání mapy a poskytli funkce vyhledávacímu tlačítku a tlačítku Moje poloha.

   Když uživatel vybere tlačítko hledání nebo zadá umístění do vyhledávacího pole a stiskne klávesu Enter, zahájí se přibližné vyhledávání dotazu uživatele. Předejte v poli hodnot ISO `countrySet` 2 zemi možnost omezit výsledky vyhledávání na tyto země nebo oblasti. Omezení země nebo oblasti vyhledávání pomáhá zvýšit přesnost výsledků, které jsou vráceny. 
  
   Po dokončení hledání posnižujte první výsledek a nastavte přes tuto oblast mapovou kameru. Když uživatel vybere tlačítko Moje umístění, načtěte polohu uživatele pomocí rozhraní HTML5 Geolocation API. Toto rozhraní API je integrováno do prohlížeče. Potom vystředěte mapu nad jejich polohou.  

   > [!Tip]
   > Při použití automaticky otevíraných oken je nejlepší `Popup` vytvořit jednu instanci a znovu použít instanci aktualizací jejího obsahu a umístění. Pro `Popup`každou instanci, kterou přidáte do kódu, je na stránku přidáno více prvků DOM. Čím více prvků DOM jsou na stránce, tím více věcí, které prohlížeč musí sledovat. Pokud je příliš mnoho položek, může být prohlížeč pomalý.

    ```JavaScript
    function initialize() {
        //Initialize a map instance.
        map = new atlas.Map('myMap', {
            center: [-90, 40],
            zoom: 2,

            //Add your Azure Maps primary subscription key to the map SDK.
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
            retryOptions: { maxTries: 4 } // Retry options
        });

        //Create an instance of the SearchURL client.
        searchURL = new atlas.service.SearchURL(pipeline);

        //If the user selects the search button, geocode the value the user passed in.
        document.getElementById('searchBtn').onclick = performSearch;

        //If the user presses Enter in the search box, perform a search.
        document.getElementById('searchTbx').onkeyup = function(e) {
            if (e.keyCode === 13) {
                performSearch();
            }
        };

        //If the user selects the My Location button, use the Geolocation API to get the user's location. Center and zoom the map on that location.
        document.getElementById('myLocationBtn').onclick = setMapToUserLocation;

        //Wait until the map resources are ready.
        map.events.add('ready', function() {

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
        navigator.geolocation.getCurrentPosition(function(position) {
            //Convert the Geolocation API position to a longitude and latitude position value that the map can interpret and center the map over it.
            map.setCamera({
                center: [position.coords.longitude, position.coords.latitude],
                zoom: maxClusterZoomLevel + 1
            });
        }, function(error) {
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

1. V posluchači `ready` událostí mapy přidejte ovládací prvek lupy a značku HTML, která zobrazí střed oblasti hledání.

    ```JavaScript
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

1. V naslouchací `ready` proces události mapy přidejte zdroj dat. Potom volání načíst a analyzovat datovou sadu. Povolte clustering ve zdroji dat. Clustering na skupiny zdrojů dat překrývající se body společně v clusteru. Clustery se při přiblížení rozdělí do jednotlivých bodů. Toto chování poskytuje lepší uživatelské prostředí a zlepšuje výkon.

    ```JavaScript
    //Create a data source, add it to the map, and then enable clustering.
    datasource = new atlas.source.DataSource(null, {
        cluster: true,
        clusterMaxZoom: maxClusterZoomLevel - 1
    });

    map.sources.add(datasource);

    //Load all the store data now that the data source is defined.  
    loadStoreData();
    ```

1. Po načtení datové sady v `ready` naslouchací proces události mapy definujte sadu vrstev, které vykreslí data. Bublinová vrstva se používá k vykreslení clusterovaných datových bodů. Vrstva symbolů se používá k vykreslení počtu bodů v každém clusteru nad bublinovou vrstvou. Druhá vrstva symbolu vykreslí vlastní ikonu pro jednotlivá umístění na mapě.

   `mouseover` Přidáním `mouseout` a událostmi do vrstev bublin a ikon změníte kurzor myši, když uživatel najeví myší na cluster nebo ikonu na mapě. Přidejte `click` událost do vrstvy bublin clusteru. Tato `click` událost přiblíží mapu o dvě úrovně a vystředí mapu nad clusterem, když uživatel vybere libovolný cluster. Přidejte `click` událost do vrstvy ikon. Tato `click` událost zobrazí vyskakovací okno, které zobrazuje podrobnosti o kavárně, když uživatel vybere ikonu individuální polohy. Přidejte událost do mapy, abyste sledovali, kdy se mapa dokončí. Když se tato událost spustí, aktualizujte položky v panelu seznamu.  

    ```JavaScript
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
            textField: ['get', 'point_count_abbreviated'],
            size: 12,
            font: ['StandardFont-Bold'],
            offset: [0, 0.4],
            color: 'white'
        }
    });

    map.layers.add([clusterBubbleLayer, clusterLabelLayer]);

    //Load a custom image icon into the map resources.
    map.imageSprite.add('myCustomIcon', iconImageUrl).then(function() {

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
    map.events.add('mouseover', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'pointer';
    });

    //When the mouse leaves the item on the cluster and icon layers, change the cursor back to the default (grab).
    map.events.add('mouseout', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'grab';
    });

    //Add a click event to the cluster layer. When the user selects a cluster, zoom into it by two levels.  
    map.events.add('click', clusterBubbleLayer, function(e) {
        map.setCamera({
            center: e.position,
            zoom: map.getCamera().zoom + 2
        });
    });

    //Add a click event to the icon layer and show the shape that was selected.
    map.events.add('click', iconLayer, function(e) {
        showPopup(e.shapes[0]);
    });

    //Add an event to monitor when the map is finished rendering the map after it has moved.
    map.events.add('render', function() {
        //Update the data in the list.
        updateListItems();
    });
    ```

1. Po načtení datové sady kavárny je třeba ji nejprve stáhnout. Potom textový soubor musí být rozdělena do řádků. První řádek obsahuje informace záhlaví. Chcete-li kód snadněji sledovat, analyzujeme záhlaví do objektu, který pak můžeme použít k vyhledání indexu buněk každé vlastnosti. Po první linii projděte zbývající čáry a vytvořte bodový prvek. Přidejte bodovou funkci do zdroje dat. Nakonec aktualizujte panel seznamu.

    ```JavaScript
    function loadStoreData() {

    //Download the store location data.
    fetch(storeLocationDataUrl)
        .then(response => response.text())
        .then(function(text) {

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

1. Po aktualizaci panelu seznamu se vypočítá vzdálenost. Tato vzdálenost je od středu mapy ke všem bodovým prvkům v aktuálním zobrazení mapy. Funkce jsou pak seřazeny podle vzdálenosti. HTML je generovánpro zobrazení každého umístění v panelu seznamu.

    ```JavaScript
    var listItemTemplate = '<div class="listItem" onclick="itemSelected(\'{id}\')"><div class="listItem-title">{title}</div>{city}<br />Open until {closes}<br />{distance} miles away</div>';

    function updateListItems() {
        //Hide the center marker.
        centerMarker.setOptions({
            visible: false
        });

        //Get the current camera and view information for the map.
        var camera = map.getCamera();
        var listPanel = document.getElementById('listPanel');

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

            //Get all the shapes that have been rendered in the bubble layer. 
            var data = map.layers.getRenderedShapes(map.getCamera().bounds, [iconLayer]);

            //Create an index of the distances of each shape.
            var distances = {};

            data.forEach(function (shape) {
                if (shape instanceof atlas.Shape) {

                    //Calculate the distance from the center of the map to each shape and store in the index. Round to 2 decimals.
                    distances[shape.getId()] = Math.round(atlas.math.getDistanceTo(camera.center, shape.getCoordinates(), 'miles') * 100) / 100;
                }
            });

            //Sort the data by distance.
            data.sort(function (x, y) {
                return distances[x.getId()] - distances[y.getId()];
            });

            data.forEach(function(shape) {
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

                //Get the distance of the shape.
                distances[shape.getId()],
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

1. Když uživatel vybere položku v panelu seznamu, obrazec, ke kterému se vztahuje položka se načte ze zdroje dat. Vygeneruje se automaticky otevírané okno založené na informacích o vlastnostech uložených v obrazci. Mapa je vystředěna nad obrazcem. Pokud je mapa široká méně než 700 pixelů, zobrazení mapy je posunuto, takže je viditelné vyskakovací okno.

    ```JavaScript
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

         //Calculate the distance from the center of the map to the shape in miles, round to 2 decimals.
        var distance = Math.round(atlas.math.getDistanceTo(map.getCamera().center, shape.getCoordinates(), 'miles') * 100)/100;

        var html = ['<div class="storePopup">'];
        html.push('<div class="popupTitle">',
            properties['AddressLine'],
            '<div class="popupSubTitle">',
            getAddressLine2(properties),
            '</div></div><div class="popupContent">',

            //Convert the closing time to a format that's easier to read.
            getOpenTillTime(properties),

            //Add the distance information.  
            '<br/>', distance,
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
                html.push('<img src="images/WiFiIcon.png" title="Wi-Fi Hotspot"/>');
            }

            if (properties['IsWheelchairAccessible']) {
                html.push('<img src="images/WheelChair-small.png" title="Wheelchair Accessible"/>');
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

Nyní máte plně funkční vyhledávač obchodů. Ve webovém prohlížeči otevřete soubor *index.html* pro lokátor úložiště. Když se clustery zobrazí na mapě, můžete vyhledat umístění pomocí vyhledávacího pole, výběrem tlačítka Moje poloha, výběrem clusteru nebo přiblížením mapy, abyste viděli jednotlivá umístění.

Při prvním výběru tlačítka Moje poloha prohlížeč zobrazí upozornění zabezpečení, které vás požádá o oprávnění k přístupu k poloze uživatele. Pokud uživatel souhlasí se sdílením své polohy, mapa se přiblíží k poloze uživatele a zobrazí se nedaleké kavárny. 

<center>

![Snímek obrazovky s požadavkem prohlížeče na přístup k poloze uživatele](./media/tutorial-create-store-locator/GeolocationApiWarning.png)</center>

Když přiblížíte dostatečně blízko v oblasti, která má umístění kavárny, clustery se rozdělí do jednotlivých umístění. Vyberte jednu z ikon na mapě nebo vyberte položku v bočním panelu, abyste viděli vyskakovací okno. Vyskakovací okno zobrazuje informace o vybraném umístění.

<center>

![Snímek obrazovky s lokátorem dokončeného obchodu](./media/tutorial-create-store-locator/FinishedSimpleStoreLocator.png)</center>

Pokud změníte velikost okna prohlížeče na šířku menší než 700 pixelů nebo otevřete aplikaci na mobilním zařízení, rozložení se změní tak, aby bylo vhodnější pro menší obrazovky. 

<center>

![Snímek obrazovky s malou obrazovkou lokátoru obchodu](./media/tutorial-create-store-locator/FinishedSimpleStoreLocatorSmallScreen.png)</center>

## <a name="next-steps"></a>Další kroky

V tomto kurzu se dozvíte, jak vytvořit základní lokátor úložiště pomocí Azure Maps. Lokátor úložiště, který vytvoříte v tomto kurzu, může mít všechny funkce, které potřebujete. Do lokátoru obchodu můžete přidat funkce nebo použít více funkcí pro další použití pro vlastní uživatelské prostředí: 

> [!div class="checklist"]
> * Povolte [návrhy při psaní](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20Autosuggest%20and%20JQuery%20UI) do vyhledávacího pole.  
> * Přidejte [podporu pro více jazyků](https://azuremapscodesamples.azurewebsites.net/?sample=Map%20Localization). 
> * Umožněte uživateli [filtrovat umístění podél trasy](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Data%20Along%20Route). 
> * Přidejte možnost [nastavení filtrů](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Symbols%20by%20Property). 
> * Přidejte podporu pro určení počáteční vyhledávací hodnoty pomocí řetězce dotazu. Když tuto možnost zahrnete do lokátoru obchodu, uživatelé mohou vyhledávat můžete označit záložkami a sdílet je. Poskytuje také snadný způsob, jak předat vyhledávání na tuto stránku z jiné stránky.  
> * Nasaďte lokátor obchodu jako [webovou aplikaci Služby Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html). 
> * Ukládejte data do databáze a vyhledejte umístění v okolí. Další informace naleznete v [přehledu typů prostorových dat serveru SQL Server](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-types-overview?view=sql-server-2017) a v [dotazu prostorových dat pro nejbližší soused](https://docs.microsoft.com/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor?view=sql-server-2017).

> [!div class="nextstepaction"]
> [Zobrazit úplný zdrojový kód](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator)

> [!div class="nextstepaction"]
> [Zobrazit živý vzorek](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Simple%20Store%20Locator)

Další informace o pokrytí a možnostech Azure Maps:

> [!div class="nextstepaction"]
> [Úrovně přiblížení a mřížka dlaždic](zoom-levels-and-tile-grid.md)

Další příklady kódu a prostředí pro interaktivní psaní kódu:

> [!div class="nextstepaction"]
> [Jak používat ovládací prvek mapy](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Použití výrazů pro styly založené na datech](data-driven-style-expressions-web-sdk.md)

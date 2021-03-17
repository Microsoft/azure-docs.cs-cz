---
title: 'Kurz: Vytvoření aplikace lokátoru úložiště pomocí Azure Maps | Mapy Microsoft Azure'
description: Kurz týkající se vytváření webových aplikací lokátoru úložiště Pomocí sady Azure Maps Web SDK můžete vytvořit webovou stránku, zadat dotaz na vyhledávací službu a zobrazit výsledky na mapě.
author: anastasia-ms
ms.author: v-stharr
ms.date: 08/11/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 801c2fe1710952a12584bf10dd8e5c77de3b839c
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625094"
---
# <a name="tutorial-create-a-store-locator-by-using-azure-maps"></a>Kurz: vytvoření lokátoru úložiště pomocí Azure Maps

Tento kurz vás provede procesem vytvoření jednoduchého lokátoru úložiště pomocí Azure Maps. Lokátory úložiště jsou běžné. Mnohé z konceptů, které se používají v tomto typu aplikace, platí i pro mnoho dalších typů aplikací. Nabídka lokátoru úložiště zákazníkům je potřeba pro většinu firem, které prodávají přímo zákazníkům. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte novou webovou stránku pomocí rozhraní Azure Ovládací prvek Mapa API.
> * Načte vlastní data ze souboru a zobrazí je na mapě.
> * Pomocí služby Azure Maps Search vyhledejte adresu nebo zadejte dotaz.
> * Získá umístění uživatele z prohlížeče a zobrazí ho na mapě.
> * Kombinováním více vrstev můžete vytvořit vlastní symboly na mapě.  
> * Datové body clusteru.  
> * Přidejte ovládací prvky přiblížení na mapu.

<a id="Intro"></a>

Přejděte dopředu na [příklad lokátoru Live Storu](https://azuremapscodesamples.azurewebsites.net/?sample=Simple%20Store%20Locator) nebo [zdrojový kód](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator).

## <a name="prerequisites"></a>Požadavky

1. [Vytvoření účtu Azure Maps s cenovou úrovní S1](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Získejte primární klíč předplatného](quick-demo-map-app.md#get-the-primary-key-for-your-account), označovaný také jako primární klíč nebo klíč předplatného.

Další informace o ověřování v Azure Maps najdete v tématu [Správa ověřování v Azure Maps](how-to-manage-authentication.md).

## <a name="design"></a>Návrh

Před přechodem do kódu je dobré začít s návrhem. Vaše Lokátory úložiště můžou být jednoduché nebo složité podle toho, co chcete. V tomto kurzu vytvoříme jednoduchý Lokátor úložiště. Obsahujeme několik tipů, které vám pomůžou s rozšiřováním některých funkcí, pokud se rozhodnete. Vytvoříme Lokátor úložiště pro fiktivní společnost s názvem contoso káva. Následující obrázek ukazuje drátěný model obecného rozložení lokátoru úložiště, který v tomto kurzu sestavíme:

![Drátěný model aplikace lokátoru úložiště pro umístění ve společnosti Contoso pro kavárny](./media/tutorial-create-store-locator/SimpleStoreLocatorWireframe.png)

Abychom maximalizovali užitečnost tohoto lokátoru úložiště, zahrnuli jsme rozložení, které se přizpůsobí, když je šířka obrazovky uživatele menší než 700 pixelů. Rozložení s odezvou usnadňuje používání lokátoru úložiště na malé obrazovce, například na mobilním zařízení. Zde je drátěný model rozložení malého obrazovky:  

![Drátový model aplikace lokátoru společnosti Contoso pro úložiště v mobilním zařízení](./media/tutorial-create-store-locator/SimpleStoreLocatorMobileWireframe.png)</

Drátěné diagramy znázorňují poměrně jasné aplikace. Aplikace má vyhledávací pole, seznam přilehlých obchodů a mapu, která obsahuje některé značky, například symboly. A má automaticky otevírané okno, které zobrazí další informace, když uživatel vybere značku. Podrobněji najdete tady funkce, které v tomto kurzu sestavíme do tohoto lokátoru úložiště:

* Na mapě jsou načtena všechna umístění z importovaného datového souboru s oddělovači a na kartě.
* Uživatel může mapu posunout a přiblížit, provést hledání a vybrat tlačítko GPS "moje umístění".
* Rozložení stránky se přizpůsobí v závislosti na šířce obrazovky zařízení.  
* V záhlaví se zobrazuje logo Storu.  
* Uživatel může pomocí vyhledávacího pole a tlačítka pro hledání vyhledat umístění, jako je adresa, PSČ nebo město. 
* `keypress`Událost přidaná do vyhledávacího pole spustí hledání, pokud uživatel stiskne klávesu ENTER. Tato funkce je často přehlédnutíná, ale vytváří lepší uživatelské prostředí.
* Když se mapa přesune, vypočítá se vzdálenost do každého umístění od středu mapy. Seznam výsledků se aktualizuje tak, aby zobrazoval nejbližší umístění v horní části mapy.  
* Když vyberete výsledek v seznamu výsledků, mapa bude zarovnána na střed vybraného umístění a informace o umístění se zobrazí v překryvném okně.  
* Výběr konkrétního umístění na mapě také aktivuje automaticky otevírané okno.
* Když se uživatel přiblíží, umístění se seskupují v clusterech. Clustery jsou reprezentovány kroužkem s číslem uvnitř kružnice. Formulář clusterů a samostatné změny uživatele mění úroveň přiblížení.
* Výběr clusteru přiblíží na mapě dvě úrovně a umístí na střed umístění clusteru.

<a id="create a data-set"></a>

## <a name="create-the-store-location-dataset"></a>Vytvořit datovou sadu umístění úložiště

Před vývojem aplikace lokátoru úložiště musíme vytvořit datovou sadu úložišť, které chceme na mapě zobrazit. V tomto kurzu používáme datovou sadu pro fiktivní kavárnu s názvem contoso káva. Datová sada pro tento jednoduchý Lokátor úložiště je spravovaná v excelovém sešitu. Datová sada obsahuje 10 213 contoso káva v kavárně se rozšíří v devíti zemích nebo oblastech: USA, Kanada, Spojené království, Francie, Německo, Itálie, Nizozemsko, Dánsko a Španělsko. Tady je snímek obrazovky, jak vypadají data:

![Snímek obrazovky s daty lokátoru úložiště v excelovém sešitu](./media/tutorial-create-store-locator/StoreLocatorDataSpreadsheet.png)

Můžete [Stáhnout excelový sešit](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data).

Podívejte se na snímek obrazovky s daty, můžeme udělat následující poznámky:

* Informace o umístění se ukládají pomocí sloupců **AddressLine**, **City**, **obec** (okres), **AdminDivision** (stát/provincie), **PostCode** (PSČ) a **Country (země** ).  
* Sloupce **Zeměpisná šířka** a **Délka** obsahují souřadnice pro každé místo v kavárně pro kavárnu v rámci společnosti Contoso. Pokud nemáte informace o souřadnicích, můžete použít vyhledávací služby v Azure Maps k určení souřadnic umístění.
* Některé další sloupce obsahují metadata týkající se kavárny: telefonní číslo, logické sloupce a časy počátečního a koncového skladu ve 24hodinovém formátu. Logické sloupce jsou pro přístup Wi-Fi a invalidního vozíku. Můžete vytvořit vlastní sloupce, které obsahují metadata, která jsou pro data o poloze relevantnější.

> [!NOTE]
> Azure Maps vykreslí data v kulové Mercator projekci "EPSG: 3857", ale přečte data v "EPSG: 4326", která používají WGS84 datum.

Existuje mnoho způsobů, jak datovou sadu zpřístupnit aplikaci. Jedním z možností je načíst data do databáze a vystavit webovou službu, která dotazuje data. Pak můžete výsledky odeslat do prohlížeče uživatele. Tato možnost je ideální pro velké datové sady nebo pro datové sady, které se často aktualizují. Tato možnost ale vyžaduje více vývojových prací a má vyšší náklady.

Dalším přístupem je převést tuto datovou sadu na nestrukturovaný textový soubor, který může prohlížeč snadno analyzovat. Samotný soubor může být hostován se zbytkem aplikace. Tato možnost udržuje něco jednoduchého, ale je dobrá možnost jenom pro menší datové sady, protože uživatel stáhne všechna data. Pro tuto datovou sadu používáme nestrukturovaný textový soubor, protože velikost datového souboru je menší než 1 MB.  

Chcete-li převést sešit na nestrukturovaný textový soubor, uložte sešit jako soubor s oddělovači na kartě. Každý sloupec je oddělen znakem tabulátoru, což umožňuje, aby se sloupce v našem kódu snadno analyzovaly. Můžete použít formát hodnot oddělených čárkami (CSV), ale tato možnost vyžaduje více logiky analýzy. Každé pole, které má čárku kolem, by bylo zabaleno do uvozovek. Pokud chcete tato data exportovat jako soubor s hodnotami oddělenými tabulátory v Excelu, vyberte **Uložit jako**. V rozevíracím seznamu **Uložit jako typ** vyberte **text (oddělený tabulátory) (*. txt)**. Pojmenujte soubor *ContosoCoffee.txt*.

![Snímek obrazovky dialogového okna Uložit jako typ](./media/tutorial-create-store-locator/SaveStoreDataAsTab.png)

Pokud otevřete textový soubor v poznámkovém bloku, vypadá to podobně jako na následujícím obrázku:

![Snímek obrazovky se souborem programu Poznámkový blok, který zobrazuje datovou sadu s hodnotami oddělenými tabulátory](./media/tutorial-create-store-locator/StoreDataTabFile.png)

## <a name="set-up-the-project"></a>Nastavení projektu

Chcete-li vytvořit projekt, můžete použít [aplikaci Visual Studio](https://visualstudio.microsoft.com) nebo Editor kódu dle vašeho výběru. Ve složce projektu vytvořte tři soubory: *index.html*, *index. CSS* a *index.js*. Tyto soubory definují rozložení, styl a logiku pro aplikaci. Vytvořte složku s názvem *data* a přidejte *ContosoCoffee.txt* do složky. Vytvořte další složku s názvem *Image*. V této aplikaci používáme 10 imagí pro ikony, tlačítka a značky na mapě. [Tyto image](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data)si můžete stáhnout. Složka projektu by teď měla vypadat jako na následujícím obrázku:

![Snímek obrazovky složky projektu lokátoru jednoduchého úložiště](./media/tutorial-create-store-locator/StoreLocatorVSProject.png)

## <a name="create-the-user-interface"></a>Vytvoření uživatelského rozhraní

Chcete-li vytvořit uživatelské rozhraní, přidejte kód do *index.html*:

1. Přidejte následující `meta` značky do `head` *index.html*. `charset`Značka definuje znakovou sadu (UTF-8). Hodnota `http-equiv` oznamuje aplikaci Internet Explorer a Microsoft Edge, aby používala nejnovější verze prohlížeče. A poslední `meta` značka Určuje zobrazení, které funguje dobře pro reakce na uspořádání.

    ```HTML
    <meta charset="utf-8">
    <meta http-equiv="x-ua-compatible" content="IE=Edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

1. Přidat odkazy do souborů JavaScript a CSS webového ovládacího prvku Azure Maps:

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

1. Přidejte odkaz na modul služby Azure Maps Services. Modul je knihovna JavaScriptu, která obaluje Azure Maps služby REST a usnadňuje jejich použití v JavaScriptu. Modul je vhodný pro zajištění funkce vyhledávání.

    ```HTML
    <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
    ```

1. Přidejte odkazy na *index.js* a *index. CSS*:

    ```HTML
    <link rel="stylesheet" href="index.css" type="text/css">
    <script src="index.js"></script>
    ```

1. Do těla dokumentu přidejte `header` značku. Uvnitř `header` značky přidejte logo a název společnosti.

    ```HTML
    <header>
        <img src="images/Logo.png" />
        <span>Contoso Coffee</span>
    </header>
    ```

1. Přidejte `main` značku a vytvořte panel hledání, který obsahuje textové pole a tlačítko Hledat. Přidejte také `div` odkazy pro mapu, panel seznamu a tlačítko GPS.

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

Až skončíte, *index.html* by měl vypadat jako v [tomto příkladu index.html souboru](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/index.html).

Dalším krokem je definování stylů CSS. Styly CSS definují způsob, jakým jsou komponenty aplikace vymezeny, a vzhled aplikace. Otevřete *index. CSS* a přidejte do něj následující kód. `@media`Styl definuje alternativní možnosti stylu, které se použijí, pokud je šířka obrazovky menší než 700 pixelů.  

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

Spusťte aplikaci nyní, zobrazí se tlačítko záhlaví, vyhledávací pole a hledání. Mapa ale není viditelná, protože ještě nebyla načtena. Pokud se pokusíte provést hledání, nedojde k žádné akci. Musíme nastavit logiku JavaScriptu, která je popsána v následující části. Tato logika přistupuje ke všem funkcím lokátoru úložiště.

## <a name="wire-the-application-with-javascript"></a>Vedení aplikace pomocí JavaScriptu

V uživatelském rozhraní se teď nastavuje vše. Pořád potřebujeme přidat JavaScript, aby se data načetla a analyzovala, a pak se na mapě vykreslí data. Začněte tím, že otevřete *index.js* a do něj přidáte kód, jak je popsáno v následujícím postupu.

1. Přidejte globální možnosti, aby bylo nastavení snazší aktualizovat. Definujte proměnné pro mapu, překryvné okno, zdroj dat, vrstvu ikony a značku HTML. Nastavte značku HTML tak, aby označovala střed oblasti hledání. A definujte instanci klienta služby Search Azure Maps.

    ```JavaScript
    //The maximum zoom level to cluster data point data on the map.
    var maxClusterZoomLevel = 11;

    //The URL to the store location data.
    var storeLocationDataUrl = 'data/ContosoCoffee.txt';

    //The URL to the icon image.
    var iconImageUrl = 'images/CoffeeIcon.png';
    var map, popup, datasource, iconLayer, centerMarker, searchURL;
    ```

1. Přidejte kód pro *index.js*. Následující kód inicializuje mapu. Přidali jsme [naslouchací proces událostí](/javascript/api/azure-maps-control/atlas.map#events) , který bude počkat na dokončení načítání stránky. Pak se v případě, že se budou monitorovat načítající mapy, zadávají události a na tlačítko Hledat a na tlačítko "umístění" získáte funkce.

   Když uživatel vybere tlačítko hledání nebo zadá umístění do vyhledávacího pole, pak stiskne klávesu ENTER, bude zahájeno přibližné vyhledávání proti dotazu uživatele. Předejte pole hodnot ISO 2 země nebo oblasti do `countrySet` Možnosti pro omezení výsledků hledání na tyto země nebo oblasti. Omezení zemí nebo oblastí pro hledání pomáhá zvýšit přesnost vrácených výsledků. 
  
   Po dokončení hledání Vezměte v úvahu první výsledek a nastavte kameru mapy přes tuto oblast. Když uživatel vybere tlačítko pro umístění uživatele, načtěte jeho umístění pomocí rozhraní API geografického umístění HTML5. Toto rozhraní API je integrováno do prohlížeče. Pak vycentrovat mapu na jejich umístění.  

   > [!Tip]
   > Při použití automaticky otevíraných oken je vhodné vytvořit jedinou `Popup` instanci a opětovně použít instanci aktualizací jejího obsahu a pozice. Pro každou `Popup` instanci, kterou přidáte do kódu, je na stránku přidáno více elementů DOM. Další prvky modelu DOM, na kterých se nachází, jsou na stránce mnohem více věcí, které prohlížeč musí sledovat. Pokud je příliš mnoho položek, může se stát, že prohlížeč bude pomalý.

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

        //If the user selects the My Location button, use the Geolocation API (Preview) to get the user's location. Center and zoom the map on that location.
        document.getElementById('myLocationBtn').onclick = setMapToUserLocation;

        //Wait until the map resources are ready.
        map.events.add('ready', function() {

            //Add your post-map load functionality.

        });
    }

    //Create an array of country/region ISO 2 values to limit searches to. 
    var countrySet = ['US', 'CA', 'GB', 'FR','DE','IT','ES','NL','DK'];

    function performSearch() {
        var query = document.getElementById('searchTbx').value;

        //Perform a fuzzy search on the users query.
        searchURL.searchFuzzy(atlas.service.Aborter.timeout(3000), query, {
            //Pass in the array of country/region ISO2 for which we want to limit the search to.
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
            //Convert the Geolocation API (Preview) position to a longitude and latitude position value that the map can interpret and center the map over it.
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

1. V `ready` naslouchací službě událostí v mapě přidejte ovládací prvek zvětšení a značku HTML pro zobrazení středu oblasti hledání.

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

1. V naslouchací službě událostí v mapě `ready` přidejte zdroj dat. Pak zavolejte na načíst a analyzujte datovou sadu. Povolte clusteringu ve zdroji dat. Clustering ve skupinách zdrojů dat překrývá body dohromady v clusteru. Clustery se v jednotlivých bodech oddělují jako přiblížení uživatele. Toto chování nabízí lepší uživatelské prostředí a zvyšuje výkon.

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

1. Po načtení datové sady v `ready` naslouchací službě událostí mapy Definujte sadu vrstev pro vykreslování dat. Bublinová vrstva se používá k vykreslování clusterovaných datových bodů. Vrstva symbolů se používá k vykreslení počtu bodů v každém clusteru nad bublinovou vrstvou. Druhá vrstva symbolu vykresluje vlastní ikonu pro jednotlivá umístění na mapě.

   Přidejte `mouseover` `mouseout` události a do vrstev bublin a ikon, abyste změnili ukazatel myši, když uživatel najede myší na cluster nebo ikonu na mapě. Přidejte `click` událost do bublinové vrstvy clusteru. Tato `click` událost se zvětší na mapě dvou úrovní a porovná mapu s clusterem, když uživatel vybere libovolný cluster. Přidejte `click` událost do vrstvy ikon. Tato `click` událost zobrazí automaticky otevírané okno, ve kterém se zobrazí podrobnosti o kavárně, když uživatel vybere ikonu jednotlivého umístění. Přidejte událost do mapy, která se monitoruje po dokončení přesunutí mapy. Když se tato událost aktivuje, aktualizujte položky na panelu seznamu.  

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

1. Když je datová sada pro kavárny nahraná, musí se nejdřív stáhnout. Pak musí být textový soubor rozdělen na řádky. První řádek obsahuje informace záhlaví. Aby bylo možné tento kód snadněji sledovat, analyzujeme hlavičku na objekt, který můžeme použít k vyhledání indexu buněk jednotlivých vlastností. Po prvním řádku projdete zbývajícími řádky a vytvoříte funkci Point. Přidejte funkci Point do zdroje dat. Nakonec aktualizujte panel seznamu.

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

1. Po aktualizaci panelu seznamu se vzdálenost vypočítá. Tato vzdálenost je od středu mapy až po všechny funkce bodů v aktuálním zobrazení mapy. Funkce se pak seřadí podle vzdálenosti. HTML se vygeneruje, aby se zobrazilo každé umístění na panelu seznamu.

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

1. Když uživatel vybere položku na panelu seznamu, tvar, ke kterému se položka vztahuje, se načte ze zdroje dat. Automaticky otevírané okno je vygenerováno na základě informací o vlastnostech uložených v obrazci. Mapa je zarovnána na střed k obrazci. Pokud je mapa menší než 700 pixelů v šířce, je zobrazení mapy posunuto, aby se automaticky otevírané okno zobrazilo.

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

Teď máte plně funkční Lokátor úložiště. Ve webovém prohlížeči otevřete soubor *index.html* pro Lokátor úložiště. Po zobrazení clusterů na mapě můžete vyhledat umístění pomocí vyhledávacího pole, a to tak, že vyberete tlačítko "umístění", výběrem clusteru nebo přiblížením na mapě zobrazíte jednotlivá umístění.

Když uživatel poprvé vybere tlačítko pro umístění, zobrazí se upozornění zabezpečení, které požádá o oprávnění k přístupu k umístění uživatele. Pokud uživatel souhlasí s sdílením svého umístění, mapa se přiblíží do umístění uživatele a zobrazí se okolní kavárny.

![Snímek obrazovky žádosti prohlížeče o přístup k umístění uživatele](./media/tutorial-create-store-locator/GeolocationApiWarning.png)

Když přiblížíte dostatek místa v oblasti, která má umístění v kavárně, clustery se rozdělují do jednotlivých umístění. Vyberte jednu z ikon na mapě nebo vyberte položku na bočním panelu, aby se zobrazilo překryvné okno. Automaticky otevírané okno zobrazuje informace o vybraném umístění.

![Snímek obrazovky konečného lokátoru úložiště](./media/tutorial-create-store-locator/FinishedSimpleStoreLocator.png)

Pokud změníte velikost okna prohlížeče na méně než 700 pixelů na šířku nebo otevřete aplikaci na mobilním zařízení, změní se rozložení tak, aby bylo lépe vhodné pro menší obrazovky.

![Snímek obrazovky s verzí na malém displeji lokátoru úložiště](./media/tutorial-create-store-locator/FinishedSimpleStoreLocatorSmallScreen.png)

V tomto kurzu jste zjistili, jak vytvořit základní Lokátor úložiště pomocí Azure Maps. Lokátor úložiště, který vytvoříte v tomto kurzu, může mít všechny funkce, které potřebujete. Do lokátoru Storu můžete přidat funkce nebo využít více funkcí pro více uživatelských zkušeností: 

 * Při [psaní](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20Autosuggest%20and%20JQuery%20UI) do vyhledávacího pole můžete povolit návrhy.  
 * Přidání [podpory pro více jazyků](https://azuremapscodesamples.azurewebsites.net/?sample=Map%20Localization). 
 * Umožní uživateli [filtrovat umístění podél trasy](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Data%20Along%20Route). 
 * Přidejte možnost [nastavení filtrů](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Symbols%20by%20Property). 
 * Přidejte podporu pro určení počáteční hodnoty vyhledávání pomocí řetězce dotazu. Když zahrnete tuto možnost do svého lokátoru úložiště, můžou uživatelé hledat v záložek a sdílet je. Poskytuje také snadnou metodu, jak můžete předat vyhledávání na této stránce z jiné stránky.  
 * Nasaďte svůj Lokátor úložiště jako [Azure App Service webovou aplikaci](../app-service/quickstart-html.md). 
 * Uložte svá data do databáze a vyhledejte umístění v okolí. Další informace najdete v tématu [Přehled typů prostorových dat SQL Server](/sql/relational-databases/spatial/spatial-data-types-overview?preserve-view=true&view=sql-server-2017) a [dotazování prostorových dat pro nejbližší sousední uzel](/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor?preserve-view=true&view=sql-server-2017).

Můžete [Zobrazit úplný zdrojový kód](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator), [zobrazit živý ukázku](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Simple%20Store%20Locator) a získat další informace o pokrytí a funkcích Azure Maps pomocí [úrovní přiblížení a mřížky dlaždic](zoom-levels-and-tile-grid.md). Můžete také [použít výrazy stylu řízené daty](data-driven-style-expressions-web-sdk.md) pro použití v obchodní logice.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nejsou k dispozici žádné prostředky, které vyžadují vyčištění.

## <a name="next-steps"></a>Další kroky

Další příklady kódu a prostředí pro interaktivní psaní kódu:

> [!div class="nextstepaction"]
> [Jak používat mapový ovládací prvek](how-to-use-map-control.md)

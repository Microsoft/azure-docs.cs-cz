---
title: Kurz – migrace webové aplikace z mapy Google na mapy Microsoft Azure
description: Kurz migrace webové aplikace z webu Google Maps na mapy Microsoft Azure
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: devx-track-js
ms.openlocfilehash: 4dee8de8f42b78ecdab9d9e15bb277d58fa8ba70
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905056"
---
# <a name="tutorial---migrate-a-web-app-from-google-maps"></a>Kurz – migrace webové aplikace z Map Google

Většina webových aplikací, které používají mapy Google, používá sadu SDK pro Google Maps V3 JavaScript. Azure Maps Web SDK je vhodná sada SDK založená na Azure, na kterou se má migrovat. Sada Azure Maps Web SDK umožňuje přizpůsobit interaktivní mapy pomocí vlastního obsahu a snímků. Svou aplikaci můžete spustit na webových nebo mobilních aplikacích. Tento ovládací prvek využívá WebGL a umožňuje vykreslovat rozsáhlé datové sady s vysokým výkonem. Pomocí JavaScriptu nebo TypeScript se budete vyvíjet pomocí této sady SDK. V tomto kurzu se naučíte, jak:

> [!div class="checklist"]
> * Načtení mapy
> * Lokalizace mapy
> * Přidejte značky, lomené čáry a mnohoúhelníky.
> * Zobrazit informace v překryvném okně nebo informačním okně
> * Načtení a zobrazení dat KML a data o jednotlivých standardech JSON
> * Značky clusteru
> * Překrytí vrstvy dlaždice
> * Zobrazení provozních dat
> * Přidání překrytí základní desky

Naučíte se také: 

> [!div class="checklist"]
> * Jak provádět běžné úlohy mapování pomocí Azure Maps Web SDK
> * Osvědčené postupy pro zlepšení výkonu a uživatelského prostředí
> * Tipy, jak zajistit, aby vaše aplikace používaly více funkcí k dispozici v Azure Maps

Pokud migrujete existující webovou aplikaci, zkontrolujte, zda je použita knihovna Open Source ovládacího prvku mapy. Příklady knihovny open source ovládacího prvku mapa jsou: cesium, leták a OpenLayers. Stále můžete migrovat aplikaci i v případě, že používá open source knihovnu ovládacího prvku mapy a nechcete používat Azure Maps Web SDK. V takovém případě připojte svoji aplikaci ke službám Azure Maps dlaždic (satelitní dlaždice[cest](/rest/api/maps/render/getmaptile) \| [satellite tiles](/rest/api/maps/render/getmapimagerytile)). Následující body jsou podrobně popsány v tématu použití Azure Maps v některých běžně používaných knihovench ovládacích prvků pro mapování open source.

- Cesium – ovládací prvek 3D mapy pro web. [Ukázka kódu](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [Dokumentace](https://cesiumjs.org/)
- Leták – zjednodušený 2D mapový ovládací prvek pro web. [Ukázka kódu](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [Dokumentace](https://leafletjs.com/)
- OpenLayers – 2D ovládací prvek mapy pro web, který podporuje projekce. [Ukázka kódu](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [Dokumentace](https://openlayers.org/)

Při vývoji pomocí JavaScriptu rozhraní může být užitečné jeden z následujících open-source projektů:

- [NG-Azure-Maps](https://github.com/arnaudleclerc/ng-azure-maps) -úhlová Obálka kolem Azure Maps.
- [AzureMapsControl. Components](https://github.com/arnaudleclerc/AzureMapsControl.Components) – komponenta Azure Maps Blazor.
- [Azure Maps reagující na komponentu](https://github.com/WiredSolutions/react-azure-maps) – reakce na reakci ovládacího prvku Azure Maps.
- [Vue Azure Maps](https://github.com/rickyruiz/vue-azure-maps) – komponenta Azure Maps pro aplikaci Vue.

## <a name="prerequisites"></a>Předpoklady 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/).
2. [Vytvořit účet Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Získejte primární klíč předplatného](quick-demo-map-app.md#get-the-primary-key-for-your-account), označovaný také jako primární klíč nebo klíč předplatného. Další informace o ověřování v Azure Maps najdete v tématu [Správa ověřování v Azure Maps](how-to-manage-authentication.md).

## <a name="key-features-support"></a>Podpora klíčových funkcí

V tabulce jsou uvedeny klíčové funkce rozhraní API v sadě Google Maps V3 JavaScript SDK a funkce podporovaného rozhraní API v sadě Azure Maps Web SDK.

| Funkce mapy Google     | Podpora Azure Maps Web SDK |
|-------------------------|:--------------------------:|
| Značky                 | ✓                          |
| Clusteringu značek       | ✓                          |
| Lomené čáry & mnohoúhelníků    | ✓                          |
| Datové vrstvy             | ✓                          |
| Základní překryvy         | ✓                          |
| Heat mapy               | ✓                          |
| Vrstvy dlaždic             | ✓                          |
| KML vrstva               | ✓                          |
| Nástroje pro kreslení           | ✓                          |
| Služba pro INCODE        | ✓                          |
| Služba itinerář      | ✓                          |
| Služba matice Distance | ✓                          |
| Služba zvýšení oprávnění       | ✓                          |

## <a name="notable-differences-in-the-web-sdks"></a>Významné rozdíly v sadách web SDK

Níže jsou uvedeny některé klíčové rozdíly mezi službami Google Maps a Azure Maps Web SDK, které mají být v potaz:

- Kromě poskytování hostovaného koncového bodu pro přístup k Azure Maps webové sadě SDK je k dispozici balíček NPM. Vložte balíček Web SDK do aplikace. Další informace najdete v této [dokumentaci](how-to-use-map-control.md). Tento balíček obsahuje také definice TypeScript.
- Nejprve musíte vytvořit instanci třídy map v Azure Maps. Počkejte, než se mapy `ready` nebo `load` událost aktivují předtím, než programově spolupracuje s mapou. Tato objednávka zajistí, že se načetly všechny prostředky mapy a že jsou připravené k jejímu použití.
- Obě platformy pro základní mapy používají podobný systém dlaždic. Dlaždice v Google Maps jsou v dimenzi 256 pixelů. dlaždice v Azure Maps jsou však v dimenzi 512 pixelů. Chcete-li získat stejné zobrazení mapy jako v Azure Maps jako Google Maps, odečtěte úroveň přiblížení Google Maps o číslo 1 v Azure Maps.
- Souřadnice v Google Maps se označují jako `latitude,longitude` , zatímco Azure Maps používá `longitude,latitude` . Formát Azure Maps je zarovnán se standardem `[x, y]` , který následuje po většině platforem GIS.
- Tvary v sadě Azure Maps Web SDK jsou založené na schématu geometrického kódu. Pomocné třídy jsou zpřístupněny prostřednictvím [oboru názvů *Atlas. data*](/javascript/api/azure-maps-control/atlas.data). Je to také [*Atlas. Třída Shape*](/javascript/api/azure-maps-control/atlas.shape) Tato třída se používá ke zalamování objektů. JSON, aby bylo možné snadno aktualizovat a udržovat datovou vazby.
- Souřadnice v Azure Maps jsou definovány jako objekty pozice. Souřadnice se zadává jako číslo pole ve formátu `[longitude,latitude]` . Nebo je zadaný pomocí New Atlas. data. Position (zeměpisná délka, zeměpisná šířka).
    > [!TIP]
    > Třída Position má statickou pomocnou metodu pro import souřadnic, které jsou ve formátu "Zeměpisná délka". Metoda [Atlas. data. Position. fromLatLng](/javascript/api/azure-maps-control/atlas.data.position) se často dá nahradit `new google.maps.LatLng` metodou v kódu Google Maps.
- Namísto zadání informací o stylech na každém obrazci, který je přidán k mapě, Azure Maps odděluje styly od dat. Data jsou uložena ve zdroji dat a jsou připojena k vrstvám vykreslování. Azure Maps kód používá zdroje dat k vykreslování dat. Tento přístup poskytuje vyšší výhody výkonu. Kromě toho mnoho vrstev podporuje styly řízené daty, kde obchodní logika může být přidána do možností stylu vrstvy. Tato podpora mění způsob vykreslování jednotlivých tvarů v rámci vrstvy na základě vlastností definovaných v obrazci.

## <a name="web-sdk-side-by-side-examples"></a>Webové sady SDK vedle sebe – příklady

Tato kolekce obsahuje ukázky kódu pro každou platformu a každá ukázka pokrývá běžný případ použití. Je určena k tomu, aby vám usnadnila migraci webové aplikace ze sady Google Maps V3 JavaScript SDK do sady Azure Maps Web SDK. Ukázky kódu související s webovými aplikacemi jsou k dispozici v jazyce JavaScript. Azure Maps ale také poskytuje definice TypeScript jako další možnost prostřednictvím [modulu npm](how-to-use-map-control.md).

**Témata**

- [Načtení mapy](#load-a-map)
- [Lokalizace mapy](#localizing-the-map)
- [Nastavení zobrazení mapy](#setting-the-map-view)
- [Přidání značky](#adding-a-marker)
- [Přidání vlastní značky](#adding-a-custom-marker)
- [Přidání lomené čáry](#adding-a-polyline)
- [Přidání mnohoúhelníku](#adding-a-polygon)
- [Zobrazit informační okno](#display-an-info-window)
- [Importovat soubor. JSON](#import-a-geojson-file)- 
- [Clusteringu značek](#marker-clustering)
- [Přidat Heat mapu](#add-a-heat-map)
- [Překrytí vrstvy dlaždice](#overlay-a-tile-layer)
- [Zobrazení provozních dat](#show-traffic-data)
- [Přidání překrytí základní desky](#add-a-ground-overlay)
- [Přidání dat KML do mapy](#add-kml-data-to-the-map)

### <a name="load-a-map"></a>Načtení mapy

Obě sady SDK mají stejný postup pro načtení mapy:

- Přidejte odkaz na mapovou sadu SDK.
- Přidejte `div` značku k textu stránky, který bude fungovat jako zástupný symbol pro mapu.
- Vytvořte funkci JavaScriptu, která se volá při načtení stránky.
- Vytvořte instanci příslušné mapové třídy.

**Některé klíčové rozdíly**

- Google Maps vyžaduje zadání klíče účtu v odkazu na skript rozhraní API. Pověření ověřování pro Azure Maps jsou zadána jako možnosti třídy map. Tímto pověřením může být klíč předplatného nebo informace Azure Active Directory.
- Google Maps akceptuje funkci zpětného volání v odkazu na skript rozhraní API, který se používá k volání funkce inicializace pro načtení mapy. V Azure Maps by se měla použít událost při načtení stránky.
- Při odkazování na `div` prvek, ve kterém bude mapa vykreslena, `Map` třída v Azure Maps vyžaduje pouze hodnotu, `id` zatímco Google Maps vyžaduje `HTMLElement` objekt.
- Souřadnice v Azure Maps jsou definovány jako objekty pozice, které lze zadat jako jednoduché pole čísel ve formátu `[longitude, latitude]` .
- Úroveň přiblížení v Azure Maps je jedna úroveň nižší než úroveň přiblížení v Mapách Google Maps. Tato nesrovnalost je způsobená rozdílem velikosti systému dlaždic dvou platforem.
- Azure Maps nepřidá žádné navigační ovládací prvky do plátna mapy. Takže ve výchozím nastavení nemá mapa tlačítka lupy a tlačítka pro mapování stylů. Existují však možnosti ovládacího prvku pro přidání výběru stylu mapy, tlačítek lupy, kompasu nebo ovládacího prvku otáčení a ovládacího prvku sklonu.
- Do Azure Maps se přidá obslužná rutina události, která monitoruje `ready` událost instance mapy. Tato událost se aktivuje, jakmile mapa dokončí načtení kontextu WebGL a všech potřebných prostředků. Přidejte veškerý kód, který chcete spustit po dokončení načítání mapy, do této obslužné rutiny události.

Základní příklady níže využívají Google Maps k načtení mapy uprostřed v New Yorku v souřadnicích. Zeměpisná délka:-73,985, zeměpisná šířka: 40,747 a mapa jsou na úrovni přiblížení 12.

#### <a name="before-google-maps"></a>Před: Google Maps

Umožňuje zobrazit mapu Google na umístění.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Spuštění tohoto kódu v prohlížeči zobrazí mapu, která vypadá jako na následujícím obrázku:

![Jednoduchá mapování Google](media/migrate-google-maps-web-app/simple-google-map.png)

#### <a name="after-azure-maps"></a>Po: Azure Maps

Načtěte mapu se stejným zobrazením v Azure Maps společně s ovládacím prvkem stylu mapy a tlačítky zvětšení.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-73.985, 40.747],  //Format coordinates as longitude, latitude.
                zoom: 11,   //Subtract the zoom level by one.

                //Specify authentication information when loading the map.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom controls to bottom right of map.
                map.controls.add(new atlas.control.ZoomControl(), {
                    position: 'bottom-right'
                });

                //Add map style control in top left corner of map.
                map.controls.add(new atlas.control.StyleControl(), {
                    position: 'top-left'
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Spuštění tohoto kódu v prohlížeči zobrazí mapu, která vypadá jako na následujícím obrázku:

![Jednoduchá Azure Maps](media/migrate-google-maps-web-app/simple-azure-maps.png)

Přečtěte si podrobnou dokumentaci k nastavení a použití ovládacího prvku Azure Maps mapa ve webové aplikaci kliknutím [sem](how-to-use-map-control.md).

> [!NOTE]
> Na rozdíl od Google Maps Azure Maps nevyžaduje počáteční centrum a úroveň přiblížení pro načtení mapy. Pokud se při načítání mapy nezadávají tyto informace, pokusí se Azure Maps určit město uživatele. Tato mapa bude zarovnána na střed a přiblížení.

**Další zdroje informací:**

- Azure Maps také poskytuje navigační ovládací prvky pro otočení a rozteč zobrazení mapy, jak je popsáno [zde](map-add-controls.md).

### <a name="localizing-the-map"></a>Lokalizace mapy

Pokud je vaše cílová skupina rozdělená do několika zemí nebo oblastí nebo hovoří o různých jazycích, je lokalizace důležitá.

#### <a name="before-google-maps"></a>Před: Google Maps

Pokud chcete lokalizovat mapy Google, přidejte parametry jazyka a oblasti.

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

Tady je příklad Google Maps s jazykem nastaveným na "fr-FR".

![Lokalizace Google Maps](media/migrate-google-maps-web-app/google-maps-localization.png)

#### <a name="after-azure-maps"></a>Po: Azure Maps

Azure Maps poskytuje dva různé způsoby nastavení jazyka a regionálního zobrazení mapy. První možností je přidat tyto informace do oboru názvů Global *Atlas* . Výsledkem bude, že ve výchozím nastavení budou všechny instance mapového ovládacího prvku ve vaší aplikaci. Následující nastaví jazyk na francouzštinu ("fr-FR") a místní zobrazení na "auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

Druhá možnost je předat tyto informace do možností mapy při načítání mapy. Nějak tak:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

> [!NOTE]
> Pomocí Azure Maps lze načíst více instancí mapy na stejné stránce s různými nastaveními jazyka a oblasti. Po načtení je také možné aktualizovat Tato nastavení v mapě. 

Podrobný seznam [podporovaných jazyků](supported-languages.md) najdete v Azure Maps.

Tady je příklad Azure Maps s jazykem nastaveným na "fr" a oblastí uživatele nastavenou na "fr-FR".

![Azure Maps lokalizace](media/migrate-google-maps-web-app/azure-maps-localization.png)

### <a name="setting-the-map-view"></a>Nastavení zobrazení mapy

Dynamické mapy v Azure i ve službě Google Maps lze programově přesunout do nových geografických umístění. Uděláte to tak, že zavoláte příslušné funkce v JavaScriptu. V příkladech se dozvíte, jak nastavit, aby se mapa zobrazovala v satelitním obrazovém rámečku, vycentrovat mapu do umístění a změnit úroveň přiblížení na 15 v Google Maps. Používají se následující souřadnice umístění: Zeměpisná délka:-111,0225 a zeměpisná šířka: 35,0272.

> [!NOTE]
> Mapy Google používá v dimenzích dlaždice, které jsou 256 pixelů, zatímco Azure Maps používá větší dlaždici 512-pixel. Proto Azure Maps vyžaduje menší počet síťových požadavků pro načtení stejné oblasti rozvržení jako Google Maps. Vzhledem k tom, jak jsou pyramidy dlaždice v mapových ovládacích prvcích fungovat, je třeba při použití Azure Maps odečíst úroveň přiblížení v Google Maps číslem One. Tato aritmetická operace zajišťuje, že větší dlaždice v Azure Maps vykreslí stejnou oblast rozvržení jako v Mapách Google.

#### <a name="before-google-maps"></a>Před: Google Maps

Přesuňte ovládací prvek mapy Google Maps pomocí `setOptions` metody. Tato metoda umožňuje zadat střed mapy a úroveň přiblížení.

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

![Zobrazení sady Map Google](media/migrate-google-maps-web-app/google-maps-set-view.png)

#### <a name="after-azure-maps"></a>Po: Azure Maps

V Azure Maps změňte umístění mapy pomocí `setCamera` metody a změňte styl mapy pomocí `setStyle` metody. Souřadnice v Azure Maps jsou ve formátu "Zeměpisná délka, zeměpisná šířka" a hodnota úrovně přiblížení je odečtena o jednu.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite'
});
```

![Zobrazení sady Azure Maps](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)

**Další zdroje informací:**

- [Výběr stylu mapy](choose-map-style.md)
- [Podporované styly map](supported-map-styles.md)

### <a name="adding-a-marker"></a>Přidání značky

V Azure Maps existuje více způsobů, jak lze na mapě vykreslovat data z těchto bodů:

- **Značky HTML** – vykresluje body pomocí tradičních elementů DOM. Značky HTML podporují přetahování.
- **Vrstva symbolu** – vykreslí body s ikonou nebo textem v rámci WebGL kontextu.
- **Bublinová vrstva** – vykreslí body jako kružnice na mapě. Poloměry kroužků se dají škálovat na základě vlastností v datech.

Vykreslení vrstev symbolů a bublinových vrstev v kontextu WebGL. Obě vrstvy mohou vykreslovat velké sady bodů na mapě. Tyto vrstvy vyžadují, aby data byla uložená ve zdroji dat. Zdroje dat a vrstvy vykreslování by měly být přidány do mapy po `ready` vyvolání události. Značky HTML jsou vykresleny jako elementy DOM v rámci stránky a nepoužívají zdroj dat. Čím více elementů modelu DOM má stránku, tím pomalejší stránka bude. Pokud je vykreslování více než několik stovek bodů na mapě, doporučuje se místo toho použít jednu z vrstev vykreslování.

Pojďme přidat značku na mapu s číslem 10 překrytí jako popisek. Použijte zeměpisnou délku:-0,2 a zeměpisnou šířku: 51,5.

#### <a name="before-google-maps"></a>Před: Google Maps

Pomocí Map Google přidejte značky do mapy pomocí `google.maps.Marker` třídy a určete mapu jako jednu z možností.

```javascript
//Create a marker and add it to the map.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    label: '10',
    map: map
});
```

![Značka Google Maps](media/migrate-google-maps-web-app/google-maps-marker.png)

**Po: Azure Maps použití značek HTML**

V Azure Maps použijte značky HTML k zobrazení bodu na mapě. Pro aplikace, které potřebují pouze zobrazit malý počet bodů na mapě, jsou doporučeny značky HTML. Chcete-li použít značku HTML, vytvořte instanci `atlas.HtmlMarker` třídy. Nastavte možnosti text a pozice a přidejte značku na mapu pomocí `map.markers.add` metody.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

![Azure Maps značka HTML](media/migrate-google-maps-web-app/azure-maps-html-marker.png)

**Po: Azure Maps používání vrstvy symbolů**

Pro vrstvu symbolů přidejte data do zdroje dat. Připojte zdroj dat ke vrstvě. Kromě toho by měl být zdroj dat a vrstva po `ready` vyvyvolání události přidány do mapy. Chcete-li vykreslit jedinečnou textovou hodnotu nad symbolem, musí být textové informace uloženy jako vlastnost datového bodu. Vlastnost musí být odkazována v `textField` Možnosti vrstvy. Tento přístup je trochu větší práce než použití značek HTML, ale lepší výkon.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Create a point feature, add a property to store a label for it, and add it to the data source.
                datasource.add(new atlas.data.Feature(new atlas.data.Point([-0.2, 51.5]), {
                    label: '10'
                }));

                //Add a layer for rendering point data as symbols.
                map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                    textOptions: {
                        //Use the label property to populate the text for the symbols.
                        textField: ['get', 'label'],
                        color: 'white',
                        offset: [0, -1]
                    }
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Azure Maps Symbolová vrstva](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)

**Další zdroje informací:**

- [Vytvoření zdroje dat](create-data-source-web-sdk.md)
- [Přidat vrstvu symbolů](map-add-pin.md)
- [Přidat bublinovou vrstvu](map-add-bubble-layer.md)
- [Data bodů clusteru](clustering-point-data-web-sdk.md)
- [Přidat značky HTML](map-add-custom-html.md)
- [Použití výrazů pro styly založené na datech](data-driven-style-expressions-web-sdk.md)
- [Možnosti ikony vrstvy symbolů](/javascript/api/azure-maps-control/atlas.iconoptions)
- [Možnost textu vrstvy symbolu](/javascript/api/azure-maps-control/atlas.textoptions)
- [Třída značek HTML](/javascript/api/azure-maps-control/atlas.htmlmarker)
- [Možnosti značky HTML](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-custom-marker"></a>Přidání vlastní značky

Vlastní obrázky můžete použít k reprezentaci bodů na mapě. Níže uvedená mapa používá vlastní obrázek k zobrazení bodu na mapě. Bod se zobrazí v zeměpisné šířce: 51,5 a zeměpisná délka:-0,2. Kotva Posune pozici značky, aby bod ikony připínáčku byl zarovnán se správnou polohou na mapě.

<center>

![Obrázek žlutého připínáčku](media/migrate-google-maps-web-app/yellow-pushpin.png)<br/>
yellow-pushpin.png</center>


#### <a name="before-google-maps"></a>Před: Google Maps

Vytvořte vlastní značku zadáním `Icon` objektu, který obsahuje, `url` do obrázku. Určete `anchor` bod pro zarovnání bodu odšpendlíku s souřadnicí na mapě. Hodnota ukotvení v Google Maps je relativní vzhledem k levému hornímu rohu obrázku.

```javascript
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    icon: {
        url: 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png',
        anchor: new google.maps.Point(5, 30)
    },
    map: map
});
```


![Vlastní značka Google Maps](media/migrate-google-maps-web-app/google-maps-custom-marker.png)

**Po: Azure Maps použití značek HTML**

Chcete-li přizpůsobit značku HTML, předejte kód HTML `string` nebo `HTMLElement` `htmlContent` možnost značky. Pomocí `anchor` Možnosti zadejte relativní pozici značky vzhledem k souřadnici pozice. Přiřaďte k možnosti jeden z devíti definovaných referenčních bodů `anchor` . Mezi tyto definované body patří: "Center", "Top", "Bottom", "Left", "Right", "Top-left", "top-right", "Bottom-left", "Bottom-Right". Ve výchozím nastavení je obsah ukotven ke spodnímu středu obsahu HTML. Aby bylo snazší migrovat kód z Google Maps, nastavte na `anchor` "Left-left" a pak použijte `pixelOffset` možnost se stejným posunem použitým v Mapách Google. Posuny Azure Maps v opačném směru posunu v Mapách Google. Vynásobit tak posuny znaménkem minus jedna.

> [!TIP]
> Přidejte `pointer-events:none` jako styl obsahu HTML, abyste zakázali výchozí chování při přetahování v Microsoft Edge. tím se zobrazí nežádoucí ikona.

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

![Azure Maps vlastní značku HTML](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)

**Po: Azure Maps používání vrstvy symbolů**

Vrstvy symbolů v Azure Maps podporují také vlastní image. Nejdřív načtěte obrázek do prostředků mapy a přiřaďte ho k jedinečnému ID. Odkázat na obrázek ve vrstvě symbolů. Pomocí `offset` Možnosti zarovnejte obrázek se správným bodem na mapě. Použijte `anchor` možnost k určení relativní pozice symbolu vzhledem k souřadnicím pozice. Použijte jeden z devíti definovaných referenčních bodů. Jsou to tyto body: "Center", "Top", "Bottom", "Left", "Right", "Top-left", "top-right", "Bottom-left", "Bottom-Right". Ve výchozím nastavení je obsah ukotven ke spodnímu středu obsahu HTML. Aby bylo snazší migrovat kód z Google Maps, nastavte na `anchor` "Left-left" a pak použijte `offset` možnost se stejným posunem použitým v Mapách Google. Posuny Azure Maps v opačném směru posunu v Mapách Google. Vynásobit tak posuny znaménkem minus jedna.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png').then(function () {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Create a point and add it to the data source.
                    datasource.add(new atlas.data.Point([-0.2, 51.5]));

                    //Add a layer for rendering point data as symbols.
                    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            //Set the image option to the id of the custom icon that was loaded into the map resources.
                            image: 'my-yellow-pin',
                            anchor: 'top-left',
                            offset: [-5, -30]
                        }
                    }));
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Azure Maps vrstva symbolu vlastní ikony](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</

> [!TIP]
> Chcete-li vykreslit rozšířené vlastní body, použijte více vrstev vykreslování dohromady. Řekněme například, že chcete mít více špendlíků, které mají stejnou ikonu na různých barevných kruzích. Místo vytváření svazků obrázků pro jednotlivé překryvy barev přidejte vrstvu symbolů nad bublinovou vrstvu. Odkázat na stejný zdroj dat špendlíky. Tento přístup bude efektivnější než vytváření a údržba svazků různých imagí.

**Další zdroje informací:**

- [Vytvoření zdroje dat](create-data-source-web-sdk.md)
- [Přidat vrstvu symbolů](map-add-pin.md)
- [Přidat značky HTML](map-add-custom-html.md)
- [Použití výrazů pro styly založené na datech](data-driven-style-expressions-web-sdk.md)
- [Možnosti ikony vrstvy symbolů](/javascript/api/azure-maps-control/atlas.iconoptions)
- [Možnost textu vrstvy symbolu](/javascript/api/azure-maps-control/atlas.textoptions)
- [Třída značek HTML](/javascript/api/azure-maps-control/atlas.htmlmarker)
- [Možnosti značky HTML](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-polyline"></a>Přidání lomené čáry

Použijte lomené čáry k vyjádření čáry nebo cesty na mapě. Pojďme na mapě vytvořit přerušovanou lomenou čáru.

#### <a name="before-google-maps"></a>Před: Google Maps

Třída lomené čáry přijímá sadu možností. Předat pole souřadnic v `path` Možnosti lomené čáry.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Define a symbol using SVG path notation, with an opacity of 1.
var lineSymbol = {
    path: 'M 0,-1 0,1',
    strokeOpacity: 1,
    scale: 4
};

//Create the polyline.
var line = new google.maps.Polyline({
    path: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1)
    ],
    strokeColor: 'red',
    strokeOpacity: 0,
    strokeWeight: 4,
    icons: [{
        icon: lineSymbol,
        offset: '0',
        repeat: '20px'
    }]
});

//Add the polyline to the map.
line.setMap(map);
```

![Lomená čáry Google Maps](media/migrate-google-maps-web-app/google-maps-polyline.png)

#### <a name="after-azure-maps"></a>Po: Azure Maps

Jsou volány čárové čáry `LineString` nebo `MultiLineString` objekty. Tyto objekty lze přidat do zdroje dat a vykresleny pomocí spojnicové vrstvy. Přidejte `LineString` do zdroje dat a potom přidejte zdroj dat do a `LineLayer` vykreslete ho.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a line and add it to the data source.
datasource.add(new atlas.data.LineString([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5]
]));

//Add a layer for rendering line data.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 4,
    strokeDashArray: [3, 3]
}));
```
![Azure Maps lomená čáry](media/migrate-google-maps-web-app/azure-maps-polyline.png)

**Další zdroje informací:**

- [Přidat řádky do mapy](map-add-line-layer.md)
- [Možnosti vrstvy čáry](/javascript/api/azure-maps-control/atlas.linelayeroptions)
- [Použití výrazů pro styly založené na datech](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Přidání mnohoúhelníku

Mapy Azure Maps a Google poskytují podobnou podporu pro mnohoúhelníky. Mnohoúhelníky slouží k reprezentaci oblasti na mapě. Následující příklady ukazují, jak vytvořit mnohoúhelník, který tvoří trojúhelník založený na souřadnici středu mapy.

#### <a name="before-google-maps"></a>Před: Google Maps

Třída mnohoúhelníku přijímá sadu možností. Předejte pole souřadnic k `paths` Možnosti mnohoúhelníku.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create a polygon.
var polygon = new google.maps.Polygon({
    paths: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1),
        center
    ],
    strokeColor: 'red',
    strokeWeight: 2,
    fillColor: 'rgba(0, 255, 0, 0.5)'
});

//Add the polygon to the map
polygon.setMap(map);
```

![Mnohoúhelník mapy Google](media/migrate-google-maps-web-app/google-maps-polygon.png)

#### <a name="after-azure-maps"></a>Po: Azure Maps

Přidejte `Polygon` objekty nebo do `MultiPolygon` zdroje dat. Vykreslete objekt na mapě pomocí vrstev. Vykreslí oblast mnohoúhelníku pomocí mnohoúhelníkové vrstvy. A vykreslete obrys mnohoúhelníku pomocí vrstvy čáry.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a polygon and add it to the data source.
datasource.add(new atlas.data.Polygon([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5],
    center
]));

//Add a polygon layer for rendering the polygon area.
map.layers.add(new atlas.layer.PolygonLayer(datasource, null, {
    fillColor: 'rgba(0, 255, 0, 0.5)'
}));

//Add a line layer for rendering the polygon outline.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 2
}));
```
![Azure Maps mnohoúhelník](media/migrate-google-maps-web-app/azure-maps-polygon.png)

**Další zdroje informací:**

- [Přidat mnohoúhelník k mapě](map-add-shape.md)
- [Přidání kruhu k mapě](map-add-shape.md#add-a-circle-to-the-map)
- [Možnosti vrstvy mnohoúhelníku](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)
- [Možnosti vrstvy čáry](/javascript/api/azure-maps-control/atlas.linelayeroptions)
- [Použití výrazů pro styly založené na datech](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>Zobrazit informační okno

Další informace o entitě se dají zobrazit na mapě jako `google.maps.InfoWindow` Třída v Mapách Google Maps. V Azure Maps lze tuto funkci dosáhnout pomocí `atlas.Popup` třídy. Další příklady přidávají na mapu značku. Po kliknutí na značku se zobrazí informační okno nebo místní nabídka.

#### <a name="before-google-maps"></a>Před: Google Maps

Vytvořte instanci informačního okna pomocí `google.maps.InfoWindow` konstruktoru.

```javascript
//Add a marker in which to display an infowindow for.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(47.6, -122.33),
    map: map
});

//Create an infowindow.
var infowindow = new google.maps.InfoWindow({
    content: '<div style="padding:5px"><b>Hello World!</b></div>'
});

//Add a click event to the marker to open the infowindow.
marker.addListener('click', function () {
    infowindow.open(map, marker);
});
```
![Místní nabídka mapy Google](media/migrate-google-maps-web-app/google-maps-popup.png)

#### <a name="after-azure-maps"></a>Po: Azure Maps

Pomocí automaticky otevíraného okna můžete zobrazit další informace o umístění. Předat HTML `string` nebo `HTMLElement` objekt do `content` Možnosti automaticky otevírané okno. V případě potřeby lze automaticky otevíraná okna zobrazit nezávisle na jakémkoli tvaru. Proto budou automaticky otevíraná okna vyžadovat `position` zadání hodnoty. Zadejte `position` hodnotu. Chcete-li zobrazit místní nabídku, zavolejte `open` metodu a předejte jí, `map` ve které se má automaticky otevíraná okna zobrazovat.

```javascript
//Add a marker to the map in which to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:5px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```
![Místní nabídka Azure Maps](media/migrate-google-maps-web-app/azure-maps-popup.png)

> [!NOTE]
> Můžete provést stejnou věc se symbolem, bublinou, čárou nebo mnohoúhelníkovou vrstvou, a to předáním zvolené vrstvy do kódu události map namísto značky.

**Další zdroje informací:**

- [Přidání místního okna](map-add-popup.md)
- [Automaticky otevírané okno s mediálním obsahem](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [Automaticky otevíraná okna v obrazcích](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [Opakované použití automaticky otevíraného okna s více PIN kódy](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [Automaticky otevíraná třída](/javascript/api/azure-maps-control/atlas.popup)
- [Možnosti místního okna](/javascript/api/azure-maps-control/atlas.popupoptions)

### <a name="import-a-geojson-file"></a>Importovat soubor. JSON

Google Maps podporuje načítání a dynamické stylování dat typu data v angličtině prostřednictvím `google.maps.Data` třídy. Funkce této třídy zarovnává mnohem více se stylem založeným na datech Azure Maps. Ale existuje nějaký klíčový rozdíl. Pomocí Map Google zadáte funkci zpětného volání. Obchodní logika pro stylování každé funkce, která se zpracovává jednotlivě ve vlákně uživatelského rozhraní. Ale v Azure Maps vrstvy podporují určení výrazů řízených daty jako možnosti stylu. Tyto výrazy jsou zpracovávány v době vykreslování v samostatném vlákně. Azure Maps přístup zvyšuje výkon při vykreslování. Tato výhoda se zaznamená, když je potřeba rychle vykreslovat větší datové sady.

Následující příklady načtou v rámci sadě USGS informační kanál pro zemětřesení za posledních sedm dní. Data zemětřesení se vykreslují jako kružnice škálované na mapě. Barva a měřítko jednotlivých kruhů jsou založené na velikosti každého zemětřesení, který je uložený ve `"mag"` Vlastnosti každé funkce v sadě dat. Pokud je velikost větší než nebo rovna pěti, bude kroužek červeně. Pokud je větší nebo rovno třem, ale menší než pět, kruh bude oranžová. Pokud je menší než tři, bude kroužek zelený. Poloměr každého kruhu bude exponenciálně vynásobený z rozsahu, který vynásobí 0,1.

#### <a name="before-google-maps"></a>Před: Google Maps

Zadejte v metodě jednu funkci zpětného volání `map.data.setStyle` . Uvnitř funkce zpětného volání použijte obchodní logiku pro každou funkci. Načtěte informační kanál pro injson pomocí `map.data.loadGeoJson` metody.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Define a callback to style each feature.
            map.data.setStyle(function (feature) {

                //Extract the 'mag' property from the feature.
                var mag = parseFloat(feature.getProperty('mag'));

                //Set the color value to 'green' by default.
                var color = 'green';

                //If the mag value is greater than 5, set the color to 'red'.
                if (mag >= 5) {
                    color = 'red';
                }
                //If the mag value is greater than 3, set the color to 'orange'.
                else if (mag >= 3) {
                    color = 'orange';
                }

                return /** @type {google.maps.Data.StyleOptions} */({
                    icon: {
                        path: google.maps.SymbolPath.CIRCLE,

                        //Scale the radius based on an exponential of the 'mag' value.
                        scale: Math.exp(mag) * 0.1,
                        fillColor: color,
                        fillOpacity: 0.75,
                        strokeWeight: 2,
                        strokeColor: 'white'
                    }
                });
            });

            //Load the data feed.
            map.data.loadGeoJson(earthquakeFeed);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Formát injson pro Google Maps](media/migrate-google-maps-web-app/google-maps-geojson.png)

#### <a name="after-azure-maps"></a>Po: Azure Maps

Injson je základní datový typ v Azure Maps. Importujte jej do zdroje dat pomocí `datasource.importFromUrl` metody. Použijte bublinovou vrstvu. Bublinová vrstva poskytuje funkce pro vykreslování kruhů s měřítkem na základě vlastností funkcí ve zdroji dat. Namísto funkce zpětného volání je obchodní logika převedena na výraz a předána do možností stylu. Výrazy definují, jak obchodní logika funguje. Výrazy mohou být předány do jiného vlákna a vyhodnocovány proti datům funkce. Do Azure Maps lze přidat více zdrojů dat a vrstev s jinou obchodní logikou. Tato funkce umožňuje vykreslit více datových sad na mapě různými způsoby.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as scaled circles.
                map.layers.add(new atlas.layer.BubbleLayer(datasource, null, {
                    //Make the circles semi-transparent.
                    opacity: 0.75,

                    color: [
                        'case',

                        //If the mag value is greater than 5, return 'red'.
                        ['>=', ['get', 'mag'], 5],
                        'red',

                        //If the mag value is greater than 3, return 'orange'.
                        ['>=', ['get', 'mag'], 3],
                        'orange',

                        //Return 'green' as a fallback.
                        'green'
                    ],

                    //Scale the radius based on an exponential of the 'mag' value.
                    radius: ['*', ['^', ['e'], ['get', 'mag']], 0.1]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```



![Azure Maps pro injson](media/migrate-google-maps-web-app/azure-maps-geojson.png)

**Další zdroje informací:**

- [Přidat vrstvu symbolů](map-add-pin.md)
- [Přidat bublinovou vrstvu](map-add-bubble-layer.md)
- [Data bodů clusteru](clustering-point-data-web-sdk.md)
- [Použití výrazů pro styly založené na datech](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>Clusteringu značek

Při vizualizaci řady datových bodů na mapě se body mohou překrývat. Překrytí usnadňuje vzhled mapy a mapa se obtížně čte a používá. Data bodu clusteringu je proces kombinování datových bodů poblíž sebe a jejich reprezentace na mapě jako jeden clusterovaný datový bod. Když se uživatel přiblíží k mapě, rozdělí se clustery do jednotlivých datových bodů. Datové body clusteru pro zlepšení uživatelského prostředí a mapování výkonu.

V následujících příkladech kód načte informační kanál pro zemětřesení data z minulého týdne a přidá ho do mapy. Clustery se vykreslují jako škálované a barevné kroužky. Měřítko a Barva kruhů závisí na počtu bodů, které obsahují.

> [!NOTE]
> Mapy Google a Azure Maps používají mírně odlišné algoritmy clusteringu. V takovém případě se v některých případech může distribuce bodu měnit.

#### <a name="before-google-maps"></a>Před: Google Maps

Použijte knihovnu MarkerCluster ke značkám clusteru. Ikony clusteru jsou omezené na obrázky, které mají čísla jedna až pět jako jejich název. Hostují se ve stejném adresáři.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Download the GeoJSON data.
            fetch(earthquakeFeed)
                .then(function (response) {
                    return response.json();
                }).then(function (data) {
                    //Loop through the GeoJSON data and create a marker for each data point.
                    var markers = [];

                    for (var i = 0; i < data.features.length; i++) {

                        markers.push(new google.maps.Marker({
                            position: new google.maps.LatLng(data.features[i].geometry.coordinates[1], data.features[i].geometry.coordinates[0])
                        }));
                    }

                    //Create a marker clusterer instance and tell it where to find the cluster icons.
                    var markerCluster = new MarkerClusterer(map, markers,
                        { imagePath: 'https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/m' });
                });
        }
    </script>

    <!-- Load the marker cluster library. -->
    <script src="https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/markerclusterer.js"></script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```



![Clusteringu Google Maps](media/migrate-google-maps-web-app/google-maps-clustering.png)

#### <a name="after-azure-maps"></a>Po: Azure Maps

Přidejte a spravujte data ve zdroji dat. Připojte zdroje dat a vrstvy a potom data vykreslete. `DataSource`Třída v Azure Maps poskytuje několik možností clusteringu.

- `cluster` – Instruuje zdroj dat na data bodu clusteru.
- `clusterRadius` -Mezi protokolem RADIUS a propojenými body clusteru.
- `clusterMaxZoom` – Maximální úroveň přiblížení, při které dojde k clusteringu. Pokud přiblížíte více než tuto úroveň, všechny body se vykreslí jako symboly.
- `clusterProperties` – Definuje vlastní vlastnosti, které se vypočítávají pomocí výrazů pro všechny body v jednotlivých clusterech a přidají se do vlastností každého bodu clusteru.

Když je clustering povolený, bude zdroj dat odesílat clusterované a neseskupené datové body do vrstev pro vykreslování. Zdroj dat je schopný clusterovat stovky tisíc datových bodů. Datový bod v clusteru má následující vlastnosti:

| Název vlastnosti             | Typ    | Description   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | Indikuje, že funkce představuje cluster. |
| `cluster_id`              | řetězec  | Jedinečné ID clusteru, které lze použít s `getClusterExpansionZoom` `getClusterChildren` metodami DataSource, a `getClusterLeaves` . |
| `point_count`             | číslo  | Počet bodů, které cluster obsahuje.  |
| `point_count_abbreviated` | řetězec  | Řetězec, který zkracuje hodnotu, `point_count` Pokud je Long. (například 4 000 se bude 4K)  |

`DataSource`Třída má následující pomocnou funkci pro přístup k dalším informacím o clusteru pomocí `cluster_id` .

| Metoda | Návratový typ | Description |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | &lt;Geometrie funkce pole promise &lt; &lt; , libovolný &gt; \| tvar&gt;&gt; | Načte podřízené objekty daného clusteru na další úrovni přiblížení. Tyto podřízené položky mohou být kombinací tvarů a podclusterů. Podclustery budou funkcemi s vlastnostmi, které odpovídají ClusteredProperties. |
| `getClusterExpansionZoom(clusterId: number)` | Číslo promise &lt;&gt; | Vypočítá úroveň přiblížení, při které se cluster začne zvětšovat nebo rozdělovat. |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | &lt;Geometrie funkce pole promise &lt; &lt; , libovolný &gt; \| tvar&gt;&gt; | Načte všechny body v clusteru. Nastavte, `limit` aby se vracela podmnožina bodů, a použijte `offset` stránku k v bodech. |

Při vykreslování clusterovaných dat na mapě je často vhodné použít dvě nebo více vrstev. Následující příklad používá tři vrstvy. Bublinová vrstva pro kreslení barevných kruhů škálované podle velikosti clusterů. Vrstva symbolů pro vykreslení velikosti clusteru jako textu. A používá druhou vrstvu symbolů pro vykreslování neclusterovaných bodů. Existuje mnoho dalších způsobů, jak vykreslovat data v clusteru. Další informace najdete v dokumentaci k [datům bodu clusteru](clustering-point-data-web-sdk.md) .

Přímo importujte data typu injson pomocí `importDataFromUrl` funkce `DataSource` třídy, uvnitř Azure Maps mapy.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource(null, {
                    //Tell the data source to cluster point data.
                    cluster: true
                });
                map.sources.add(datasource);

                //Create layers for rendering clusters, their counts and unclustered points and add the layers to the map.
                map.layers.add([
                    //Create a bubble layer for rendering clustered data points.
                    new atlas.layer.BubbleLayer(datasource, null, {
                        //Scale the size of the clustered bubble based on the number of points inthe cluster.
                        radius: [
                            'step',
                            ['get', 'point_count'],
                            20,         //Default of 20 pixel radius.
                            100, 30,    //If point_count >= 100, radius is 30 pixels.
                            750, 40     //If point_count >= 750, radius is 40 pixels.
                        ],

                        //Change the color of the cluster based on the value on the point_cluster property of the cluster.
                        color: [
                            'step',
                            ['get', 'point_count'],
                            'lime',            //Default to lime green. 
                            100, 'yellow',     //If the point_count >= 100, color is yellow.
                            750, 'red'         //If the point_count >= 750, color is red.
                        ],
                        strokeWidth: 0,
                        filter: ['has', 'point_count'] //Only rendered data points which have a point_count property, which clusters do.
                    }),

                    //Create a symbol layer to render the count of locations in a cluster.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            image: 'none' //Hide the icon image.
                        },
                        textOptions: {
                            textField: ['get', 'point_count_abbreviated'],
                            offset: [0, 0.4]
                        }
                    }),

                    //Create a layer to render the individual locations.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
                    })
                ]);

                //Retrieve a GeoJSON data set and add it to the data source. 
                datasource.importDataFromUrl(earthquakeFeed);
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```



![Azure Maps clusteringu](media/migrate-google-maps-web-app/azure-maps-clustering.png)

**Další zdroje informací:**

- [Přidat vrstvu symbolů](map-add-pin.md)
- [Přidat bublinovou vrstvu](map-add-bubble-layer.md)
- [Data bodů clusteru](clustering-point-data-web-sdk.md)
- [Použití výrazů pro styly založené na datech](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Přidat Heat mapu

Heat mapy, označované také jako mapy hustoty bodů, jsou typem vizualizace dat. Slouží k reprezentaci hustoty dat pomocí rozsahu barev. A často se používají k zobrazení dat "aktivních bodů" na mapě. Tepelné mapy jsou skvělým způsobem, jak vykreslovat sady velkých bodů dat.

V následujících příkladech je načten informační kanál zemětřesení pro všechny za minulý měsíc, z sadě USGS a vykresluje se jako vážená Heat mapa. `"mag"`Vlastnost se používá jako váha.

#### <a name="before-google-maps"></a>Před: Google Maps

Pokud chcete vytvořit Heat mapu, načtěte knihovnu vizualizace přidáním `&libraries=visualization` do adresy URL skriptu rozhraní API. Vrstva Heat mapy v Google Maps nepodporuje přímo data typu data JSON. Nejprve Stáhněte data a převeďte je na pole váženého datového bodu:

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {

            var map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2,
                mapTypeId: 'satellite'
            });

            //Download the GeoJSON data.
            fetch(url).then(function (response) {
                return response.json();
            }).then(function (res) {
                var points = getDataPoints(res);

                var heatmap = new google.maps.visualization.HeatmapLayer({
                    data: points
                });
                heatmap.setMap(map);
            });
        }

        function getDataPoints(geojson, weightProp) {
            var points = [];

            for (var i = 0; i < geojson.features.length; i++) {
                var f = geojson.features[i];

                if (f.geometry.type === 'Point') {
                    points.push({
                        location: new google.maps.LatLng(f.geometry.coordinates[1], f.geometry.coordinates[0]),
                        weight: f.properties[weightProp]
                    });
                }
            }

            return points;
        } 
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]&libraries=visualization" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```



![Heat mapa Google Maps](media/migrate-google-maps-web-app/google-maps-heatmap.png)

#### <a name="after-azure-maps"></a>Po: Azure Maps

Načtěte data o úrovni injson do zdroje dat a propojte zdroj dat s vrstvou Heat mapy. Vlastnost, která bude použita pro váhu, lze předat do `weight` Možnosti pomocí výrazu. Přímo importujte data typu injson do Azure Maps pomocí `importDataFromUrl` funkce `DataSource` třídy.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,
                style: 'satellite',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as a heat map.
                map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
                    weight: ['get', 'mag'],
                    intensity: 0.005,
                    opacity: 0.65,
                    radius: 10
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```



![Azure Maps Heat mapa](media/migrate-google-maps-web-app/azure-maps-heatmap.png)

**Další zdroje informací:**

- [Přidání vrstvy heat mapy](map-add-heat-map-layer.md)
- [Třída vrstvy tepelné mapy](/javascript/api/azure-maps-control/atlas.layer.heatmaplayer)
- [Možnosti vrstvy tepelné mapy](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)
- [Použití výrazů pro styly založené na datech](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Překrytí vrstvy dlaždice

Vrstvy dlaždic v Azure Maps se označují jako překryvy obrázků v Google Maps. Vrstvy dlaždic vám umožňují překrýt velké obrázky, které byly rozdělené do menších dlaždic, které se zarovnají se systémem mapy dláždění. Tento přístup se běžně používá k překrytí rozsáhlých imagí nebo velkých datových sad.

V následujících příkladech je překrytá vrstva s paprskovou dlaždicí počasí z Iowaho prostředí Mesonet of Iowa State University.

#### <a name="before-google-maps"></a>Před: Google Maps

Ve službě Google Maps lze vrstvy dlaždic vytvořit pomocí `google.maps.ImageMapType` třídy.

```javascript
map.overlayMapTypes.insertAt(0, new google.maps.ImageMapType({
    getTileUrl: function (coord, zoom) {
        return "https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/" + zoom + "/" + coord.x + "/" + coord.y;
    },
    tileSize: new google.maps.Size(256, 256),
    opacity: 0.8
}));
```



![Vrstva dlaždic mapy Google](media/migrate-google-maps-web-app/google-maps-tile-layer.png)

#### <a name="after-azure-maps"></a>Po: Azure Maps

Přidejte vrstvu dlaždic do mapy podobně jako jakoukoli jinou vrstvu. Použijte formátovanou adresu URL, která má ve x, y, zástupné symboly lupy; `{x}`, `{y}` , `{z}`  Pokud chcete sdělit vrstvu, kde má být přístup k dlaždicím. Vrstvy dlaždic Azure Maps také podporují `{quadkey}` , `{bbox-epsg-3857}` a `{subdomain}` zástupné symboly.

> [!TIP]
> V Azure Maps vrstev lze snadno vykreslovat pod jinými vrstvami, včetně základních vrstev mapy. Často je žádoucí vykreslovat vrstvy dlaždice pod popisky map, aby byly snadno čitelné. `map.layers.add`Metoda přebírá druhý parametr, který je identifikátorem vrstvy, do které chcete vložit novou vrstvu. Chcete-li vložit vrstvu dlaždice pod popisky mapy, použijte tento kód: `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```



![Vrstva Azure Maps dlaždice](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)

> [!TIP]
> Žádosti o dlaždici se dají zachytit pomocí `transformRequest` Možnosti mapy. V případě potřeby vám umožní upravit nebo přidat hlavičky do žádosti.

**Další zdroje informací:**

- [Přidání vrstev dlaždic](map-add-tile-layer.md)
- [Třída dlaždic vrstev](/javascript/api/azure-maps-control/atlas.layer.tilelayer)
- [Možnosti vrstvy dlaždic](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

### <a name="show-traffic-data"></a>Zobrazení provozních dat

Data přenosů se dají překrývají v Azure i ve službě Google Maps.

#### <a name="before-google-maps"></a>Před: Google Maps

Překrytí dat provozu na mapě pomocí vrstvy provozu.

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```



![Přenosy Google Maps](media/migrate-google-maps-web-app/google-maps-traffic.png)

#### <a name="after-azure-maps"></a>Po: Azure Maps

Azure Maps poskytuje několik různých možností pro zobrazení provozu. Zobrazit incidenty provozu, jako jsou například uzávěry provozu a nehody jako ikony na mapě. Překrytí toku přenosů a barevně kódovaných cest na mapě Barvy lze upravovat na základě odeslaného limitu rychlosti, relativně k normálnímu očekávanému zpoždění nebo absolutnímu zpoždění. Data incidentu v Azure Maps aktualizace každou minutu a data toků se aktualizují každé dvě minuty.

Přiřaďte požadovaným hodnotám `setTraffic` Možnosti.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```



![Azure Maps provoz](media/migrate-google-maps-web-app/azure-maps-traffic.png)

Pokud kliknete na jednu z ikon přenosů v Azure Maps, zobrazí se další informace v automaticky otevřeném okně.



![Incident Azure Maps provozu](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)

**Další zdroje informací:**

- [Zobrazit provoz na mapě](map-show-traffic.md)
- [Možnosti překrytí provozu](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>Přidání překrytí základní desky

Mapy Azure i Google podporují překrývání imagí na mapě. Obrázky, které se při posouvání a přiblížení mapují a přibližují, se škálují. V Mapách Google jsou obrázky s mikroodkazem označovány jako základní překrytí, zatímco v Azure Maps jsou označovány jako vrstvy obrázků. Jsou skvělé pro vytváření plánů poschodí, překrývajících se starých map nebo k zobrazení snímků z pomocí dronů.

#### <a name="before-google-maps"></a>Před: Google Maps

Zadejte adresu URL obrázku, který chcete překrýt, a ohraničující rámeček pro svázání obrázku na mapě. Tento příklad překrývá obrázek mapy [newyorském New Jersey z 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) na mapě.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.740, -74.18),
                zoom: 12
            });

            var imageBounds = {
                north: 40.773941,
                south: 40.712216,
                east: -74.12544,
                west: -74.22655
            };

            historicalOverlay = new google.maps.GroundOverlay(
                'https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg',
                imageBounds);
            historicalOverlay.setMap(map);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

Spuštění tohoto kódu v prohlížeči zobrazí mapu, která vypadá jako na následujícím obrázku:

![Překryv obrázku Google Maps](media/migrate-google-maps-web-app/google-maps-image-overlay.png)

#### <a name="after-azure-maps"></a>Po: Azure Maps

Použijte `atlas.layer.ImageLayer` třídu k překrytí nesledovaných imagí. Tato třída vyžaduje adresu URL obrázku a sadu souřadnic pro čtyři rohy obrázku. Bitová kopie musí být hostována buď ve stejné doméně, nebo musí mít povolenou CORs.

> [!TIP]
> Pokud máte pouze informace o Severní, Jižní, východní, západní a rotační oblasti a nemáte souřadnice pro každý roh obrázku, můžete použít statickou [`atlas.layer.ImageLayer.getCoordinatesFromEdges`](/javascript/api/azure-maps-control/atlas.layer.imagelayer#getcoordinatesfromedges-number--number--number--number--number-) metodu.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-74.18, 40.740],
                zoom: 12,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an image layer and add it to the map.
                map.layers.add(new atlas.layer.ImageLayer({
                    url: 'newark_nj_1922.jpg',
                    coordinates: [
                        [-74.22655, 40.773941], //Top Left Corner
                        [-74.12544, 40.773941], //Top Right Corner
                        [-74.12544, 40.712216], //Bottom Right Corner
                        [-74.22655, 40.712216]  //Bottom Left Corner
                    ]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```



![Překrytí obrázku Azure Maps](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)

**Další zdroje informací:**

- [Překryv obrázku](map-add-image-layer.md)
- [Třída image Layer](/javascript/api/azure-maps-control/atlas.layer.imagelayer)

### <a name="add-kml-data-to-the-map"></a>Přidání dat KML do mapy

Mapy Azure i Google můžou na mapě importovat a vykreslovat data KML, KMZ a GeoRSS. Azure Maps podporuje také GPX, GML, prostorové Web-Mapping soubory CSV, služby (WMS), služby pro Web-Mapping dlaždici (WMTS) a služby webových funkcí (WFS). Azure Maps přečte soubory místně do paměti a ve většině případů může zvládnout mnohem větší soubory KML. 

#### <a name="before-google-maps"></a>Před: Google Maps


```javascript
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(0, 0),
                zoom: 1
            });

             var layer = new google.maps.KmlLayer({
              url: 'https://googlearchive.github.io/js-v2-samples/ggeoxml/cta.kml',
              map: map
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

Spuštění tohoto kódu v prohlížeči zobrazí mapu, která vypadá jako na následujícím obrázku:

![KML Google Maps](media/migrate-google-maps-web-app/google-maps-kml.png)

#### <a name="after-azure-maps"></a>Po: Azure Maps

V Azure Maps je pro informating data ve webové sadě v angličtině hlavní formát dat, další formáty prostorových dat je možné snadno integrovat pomocí [modulu pro prostorové vstupně-výstupní operace](/javascript/api/azure-maps-spatial-io/). Tento modul obsahuje funkce pro čtení i zápis prostorových dat a také obsahuje jednoduchou datovou vrstvu, která může snadno vykreslovat data z libovolného z těchto formátů prostorových dat. Chcete-li číst data v souboru prostorových dat, předejte adresu URL nebo nezpracovaná data jako řetězec nebo objekt blob do `atlas.io.read` funkce. Tato akce vrátí všechna Analyzovaná data ze souboru, který lze přidat do mapy. KML je trochu složitější než většina formátů prostorových dat, protože obsahuje mnoho dalších informací o stylu. `SpatialDataLayer`Třída podporuje vykreslování většiny těchto stylů, ale před načtením dat funkce musí být obrázky napřed načteny do mapy a základní překrytí musí být přidány jako vrstvy do mapy samostatně. Při načítání dat prostřednictvím adresy URL by se měla hostovat na koncovém bodu s povoleným CORs nebo by měla být proxy služba předána jako možnost funkce Read. 

```javascript
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <!-- Add reference to the Azure Maps Spatial IO module. -->
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

    <script type='text/javascript'>
        var map, datasource, layer;

        function initMap() {
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
            map.events.add('ready', function () {
            
                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Add a simple data layer for rendering the data.
                layer = new atlas.layer.SimpleDataLayer(datasource);
                map.layers.add(layer);

                //Read a KML file from a URL or pass in a raw KML string.
                atlas.io.read('https://googlearchive.github.io/js-v2-samples/ggeoxml/cta.kml').then(async r => {
                    if (r) {

                        //Check to see if there are any icons in the data set that need to be loaded into the map resources.
                        if (r.icons) {
                            //For each icon image, create a promise to add it to the map, then run the promises in parrallel.
                            var imagePromises = [];

                            //The keys are the names of each icon image.
                            var keys = Object.keys(r.icons);

                            if (keys.length !== 0) {
                                keys.forEach(function (key) {
                                    imagePromises.push(map.imageSprite.add(key, r.icons[key]));
                                });

                                await Promise.all(imagePromises);
                            }
                        }

                        //Load all features.
                        if (r.features && r.features.length > 0) {
                            datasource.add(r.features);
                        }

                        //Load all ground overlays.
                        if (r.groundOverlays && r.groundOverlays.length > 0) {
                            map.layers.add(r.groundOverlays);
                        }

                        //If bounding box information is known for data, set the map view to it.
                        if (r.bbox) {
                            map.setCamera({ bounds: r.bbox, padding: 50 });
                        }
                    }
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```


![Azure Maps KML](media/migrate-google-maps-web-app/azure-maps-kml.png)</center>


**Další zdroje informací:**

- [Atlas. IO. Read – funkce](/javascript/api/azure-maps-spatial-io/atlas.io#read-string---arraybuffer---blob--spatialdatareadoptions-)
- [SimpleDataLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)
- [SimpleDataLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

## <a name="additional-code-samples"></a>Další ukázky kódu

Níže jsou uvedeny některé další ukázky kódu související s migrací Google Maps:

- [Nástroje pro kreslení](map-add-drawing-toolbar.md)
- [Omezení mapování na dva posouvání prstů](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
- [Omezit přiblížení rolovacího kolečka](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
- [Vytvoření celoobrazovkového ovládacího prvku](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**Orgány**

- [Použití modulu služby Azure Maps Services](how-to-use-services-module.md)
- [Hledání bodů zájmu](map-search-location.md)
- [Získat informace ze souřadnice (zpětného zobrazení kódu)](map-get-information-from-coordinate.md)
- [Zobrazení pokynů pro trasu z A do B](map-route.md)
- [Vyhledat automatické návrhy pomocí uživatelského rozhraní JQuery](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Mapování tříd webu Google Maps V3 na Azure Maps Web SDK

Následující dodatek poskytuje alternativní odkaz na běžně používané třídy v Google Maps v3 a ekvivalentní Azure Maps Web SDK.

### <a name="core-classes"></a>Základní třídy

| Mapy Google   | Azure Maps  |
|---------------|-------------|
| `google.maps.Map` | [Tamazight. Mapy](/javascript/api/azure-maps-control/atlas.map)  |
| `google.maps.InfoWindow` | [Tamazight. Oken](/javascript/api/azure-maps-control/atlas.popup)  |
| `google.maps.InfoWindowOptions` | [Tamazight. PopupOptions](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [Atlas. data. pozice](/javascript/api/azure-maps-control/atlas.data.position)  |
| `google.maps.LatLngBounds` | [Atlas. data. BoundingBox](/javascript/api/azure-maps-control/atlas.data.boundingbox) |
| `google.maps.MapOptions`  | [Tamazight. CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)<br/>[Tamazight. CameraBoundsOptions](/javascript/api/azure-maps-control/atlas.cameraboundsoptions)<br/>[Tamazight. ServiceOptions](/javascript/api/azure-maps-control/atlas.serviceoptions)<br/>[Tamazight. StyleOptions](/javascript/api/azure-maps-control/atlas.styleoptions)<br/>[Tamazight. UserInteractionOptions](/javascript/api/azure-maps-control/atlas.userinteractionoptions) |
| `google.maps.Point`  | [Tamazight. Pixel](/javascript/api/azure-maps-control/atlas.pixel)   |

## <a name="overlay-classes"></a>Třídy překrytí

| Mapy Google  | Azure Maps  |
|--------------|-------------|
| `google.maps.Marker` | [atlas.HtmlMarker](/javascript/api/azure-maps-control/atlas.htmlmarker)<br/>[Atlas. data. Point](/javascript/api/azure-maps-control/atlas.data.point)  |
| `google.maps.MarkerOptions`  | [atlas.HtmlMarkerOptions](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)<br/>[Atlas. Layer. SymbolLayer](/javascript/api/azure-maps-control/atlas.layer.symbollayer)<br/>[Tamazight. SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions)<br/>[Tamazight. IconOptions](/javascript/api/azure-maps-control/atlas.iconoptions)<br/>[Tamazight. TextOptions](/javascript/api/azure-maps-control/atlas.textoptions)<br/>[Atlas. Layer. BubbleLayer](/javascript/api/azure-maps-control/atlas.layer.bubblelayer)<br/>[Tamazight. BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions) |
| `google.maps.Polygon`  | [Atlas. data. mnohoúhelník](/javascript/api/azure-maps-control/atlas.data.polygon)               |
| `google.maps.PolygonOptions` |[Atlas. Layer. PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer)<br/> [Tamazight. PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)<br/> [Atlas. Layer. LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer)<br/> [Tamazight. LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)|
| `google.maps.Polyline` | [Atlas. data. LineString](/javascript/api/azure-maps-control/atlas.data.linestring)         |
| `google.maps.PolylineOptions` | [Atlas. Layer. LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer)<br/>[Tamazight. LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions) |
| `google.maps.Circle`  | Viz [Přidání kruhu k mapě](map-add-shape.md#add-a-circle-to-the-map) .                                     |
| `google.maps.ImageMapType`  | [Tamazight. TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer)         |
| `google.maps.ImageMapTypeOptions` | [Tamazight. TileLayerOptions](/javascript/api/azure-maps-control/atlas.tilelayeroptions) |
| `google.maps.GroundOverlay`  | [Atlas. Layer. ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer)<br/>[Tamazight. ImageLayerOptions](/javascript/api/azure-maps-control/atlas.imagelayeroptions) |

## <a name="service-classes"></a>Třídy služeb

Sada Azure Maps Web SDK obsahuje modul služeb, který lze načíst samostatně. Tento modul zabalí služby Azure Maps REST s webovým rozhraním API a dá se použít v aplikacích JavaScript, TypeScript a Node.js.

| Mapy Google | Azure Maps  |
|-------------|-------------|
| `google.maps.Geocoder` | [Atlas. Service. SearchUrl](/javascript/api/azure-maps-rest/atlas.service.searchurl)  |
| `google.maps.GeocoderRequest`  | [Tamazight. SearchAddressOptions](/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions)<br/>[Tamazight. SearchAddressRevrseOptions](/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions)<br/>[Tamazight. SearchAddressReverseCrossStreetOptions](/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions)<br/>[Tamazight. SearchAddressStructuredOptions](/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions)<br/>[Tamazight. SearchAlongRouteOptions](/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions)<br/>[Tamazight. SearchFuzzyOptions](/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions)<br/>[Tamazight. SearchInsideGeometryOptions](/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions)<br/>[Tamazight. SearchNearbyOptions](/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions)<br/>[Tamazight. SearchPOIOptions](/javascript/api/azure-maps-rest/atlas.service.searchpoioptions)<br/>[Tamazight. SearchPOICategoryOptions](/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions) |
| `google.maps.DirectionsService`  | [Atlas. Service. RouteUrl](/javascript/api/azure-maps-rest/atlas.service.routeurl)  |
| `google.maps.DirectionsRequest`  | [Tamazight. CalculateRouteDirectionsOptions](/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions) |
| `google.maps.places.PlacesService` | [Atlas. Service. SearchUrl](/javascript/api/azure-maps-rest/atlas.service.searchurl)  |

## <a name="libraries"></a>Knihovny

Knihovny přidávají k mapě další funkce. Mnohé z těchto knihoven jsou v základní sadě SDK Azure Maps. Tady jsou některé ekvivalentní třídy, které se mají použít místo těchto knihoven Google Maps.

| Mapy Google           | Azure Maps   |
|-----------------------|--------------|
| Knihovna kreslení       | [Modul nástrojů pro kreslení](set-drawing-options.md) |
| Knihovna geometrie      | [Atlas. Math](/javascript/api/azure-maps-control/atlas.math)   |
| Knihovna vizualizace | [Vrstva tepelné mapy](map-add-heat-map-layer.md) |

## <a name="next-steps"></a>Další kroky

Další informace o Azure Maps Web SDK:

> [!div class="nextstepaction"]
> [Jak používat mapový ovládací prvek](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Jak používat modul nástrojů pro kreslení](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Jak používat modul služeb](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Jak používat modul pro prostorové vstupně-výstupní operace](how-to-use-spatial-io-module.md)
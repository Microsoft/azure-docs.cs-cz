---
title: Migrace webové aplikace z Google Maps | Microsoft Docs
description: Kurz migrace webové aplikace z webu Google Maps na Microsoft Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: a414d7b15f81ab81783b66f8297a207afe569365
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2019
ms.locfileid: "75562166"
---
# <a name="migrate-a-web-app-from-google-maps"></a>Migrace webové aplikace z Map Google

Většina webových aplikací využívajících Google Maps používá sadu SDK pro Google Maps V3 JavaScript. Azure Maps Web SDK je vhodná sada SDK založená na Azure, na kterou se má migrovat. Sada Azure Maps Web SDK umožňuje přizpůsobit interaktivní mapy s vlastním obsahem a pomocí obrázků pro zobrazení ve vašich webových nebo mobilních aplikacích. Tento ovládací prvek využívá WebGL a umožňuje vykreslovat rozsáhlé datové sady s vysokým výkonem. Pomocí JavaScriptu nebo TypeScript se budete vyvíjet pomocí této sady SDK.

Pokud migrujete existující webovou aplikaci, zkontrolujte, zda je použita knihovna Open Source ovládacího prvku mapa, například cesium, leták a OpenLayers. Pokud je to a nechcete používat Azure Maps Web SDK, je další možností migrace aplikace i nadále používat ovládací prvek Open Source map a připojit ho ke službám Azure Maps dlaždic ([silniční dlaždice](https://docs.microsoft.com/rest/api/maps/render/getmaptile) \| [satelitní dlaždice](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)). Níže jsou uvedeny podrobnosti o tom, jak používat Azure Maps v některých běžně používaných open source knihovny ovládacích prvků pro mapování.

- Cesium – ovládací prvek 3D mapy pro web. [Dokumentace k](https://cesiumjs.org/) [ukázce kódu](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \|
- Leták – zjednodušený 2D mapový ovládací prvek pro web. [Dokumentace k](https://leafletjs.com/) [ukázce kódu](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \|
- OpenLayers – 2D ovládací prvek mapy pro web, který podporuje projekce. [Dokumentace k](https://openlayers.org/) [ukázce kódu](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \|

## <a name="key-features-support"></a>Podpora klíčových funkcí

V následující tabulce jsou uvedeny klíčové funkce API v sadě Google Maps V3 JavaScript SDK a podpora podobného rozhraní API v sadě Azure Maps Web SDK.

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
| Služba zvýšení oprávnění       | Plánováno                    |

## <a name="notable-differences-in-the-web-sdks"></a>Významné rozdíly v sadách web SDK

Níže jsou uvedené některé z klíčových rozdílů mezi službami Google Maps a Azure Maps Web SDK, na kterých je potřeba mít na paměti:

- Kromě poskytování hostovaného koncového bodu pro přístup k Azure Maps webové sadě SDK je k dispozici také balíček NPM pro vložení webové sady SDK do aplikací, pokud jsou preferované. Další informace najdete v této [dokumentaci](how-to-use-map-control.md) . Tento balíček obsahuje také definice TypeScript.
- Po vytvoření instance třídy map v Azure Maps by váš kód měl počkat na to, aby se události Maps `ready` nebo `load` události před interakcí s mapou. Tím se zajistí, že se načetly všechny prostředky mapy a jsou připravené k otevření.
- Obě platformy pro základní mapy používají podobný systém dlaždic, ale dlaždice v Google Maps jsou v dimenzi 256 pixelů, zatímco dlaždice v Azure Maps jsou v dimenzi 512 pixelů. Aby bylo možné získat stejné zobrazení mapy jako v Azure Maps jako Google Maps, musí být úroveň přiblížení, která se používá v Google Maps, odečtená o jednu v Azure Maps.
- Souřadnice v Google Maps se označují jako zeměpisná šířka, zeměpisná délka, zatímco Azure Maps používá zeměpisnou délku, zeměpisnou šířku. Tato služba je zarovnána se standardním `[x, y]`em, po kterém následují většina platforem GIS.
- Tvary v sadě Azure Maps Web SDK jsou založené na schématu geometrického kódu. Pomocné třídy jsou zpřístupněny prostřednictvím [oboru názvů *Atlas. data* ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data?view=azure-iot-typescript-latest). Je to také [*Atlas. Třída Shape*](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape) , která se dá použít ke zalamování objektů a jejich snadné aktualizace a udržování v datové vazbě.
- Souřadnice v Azure Maps jsou definovány jako objekty pozice, které lze zadat jako jednoduché pole čísel ve formátu `[longitude, latitude]` nebo New Atlas. data. Position (zeměpisná délka, zeměpisná šířka).
    > [!TIP]
    > Třída Position má statickou pomocnou metodu pro import souřadnic, které jsou ve formátu "Zeměpisná délka". Metoda [Atlas. data. Position. fromLatLng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest) může často nahradit metodu `new google.maps.LatLng` v kódu Google Maps.
- Namísto zadání informací o stylech na každém obrazci, který je přidán k mapě, Azure Maps odděluje styly od dat. Data jsou uložena ve zdrojích dat a jsou připojena k vykreslovacím vrstvám, které Azure Maps kód používá k vykreslování dat. Tento přístup poskytuje vyšší výhody výkonu. Kromě toho mnoho vrstev podporuje styly řízené daty, kde obchodní logika může být přidána do možností stylu vrstvy, které změní způsob, jakým se jednotlivé tvary vykreslují v rámci vrstvy na základě vlastností definovaných v prvku Shape.

## <a name="web-sdk-side-by-side-examples"></a>Webové sady SDK vedle sebe – příklady

Následuje kolekce ukázek kódu pro každou platformu, která se vztahuje na běžné případy použití, které vám pomůžou migrovat svou webovou aplikaci ze sady Google Maps V3 JavaScript SDK na Azure Maps webovou sadu SDK. Ukázky kódu související s webovými aplikacemi jsou k dispozici v JavaScriptu. Azure Maps ale také poskytuje definice TypeScript jako další možnost prostřednictvím [modulu npm](how-to-use-map-control.md).

### <a name="load-a-map"></a>Načtení mapy

Načtení mapy v sadě SDK se řídí stejnou sadou kroků:

- Přidejte odkaz na mapovou sadu SDK.
- Přidejte značku `div` k textu stránky, která bude sloužit jako zástupný symbol pro mapu.
- Vytvořte funkci JavaScriptu, která se volá při načtení stránky.
- Vytvořte instanci příslušné mapové třídy.

**Některé klíčové rozdíly**

- Google Maps vyžaduje zadání klíče účtu v odkazu na skript rozhraní API. Pověření ověřování pro Azure Maps jsou zadána jako možnosti třídy map. Může se jednat o klíč předplatného nebo informace Azure Active Directory.
- Google Maps přebírá ve funkci zpětného volání v odkazu na skript rozhraní API, které se používá k volání funkce inicializace pro načtení mapy. Při Azure Maps by se měla použít událost při načtení stránky.
- Při odkazování na prvek `div`, ve kterém bude mapa vykreslena, třída `Map` v Azure Maps vyžaduje pouze `id` hodnotu, zatímco Google Maps vyžaduje objekt `HTMLElement`.
- Souřadnice v Azure Maps jsou definovány jako objekty pozice, které lze zadat jako jednoduché pole čísel ve formátu `[longitude, latitude]`.
- Úroveň přiblížení v Azure Maps je jedna úroveň nižší než příklad Google Maps, z důvodu rozdílu velikosti systémů dlaždic mezi platformami.
- Ve výchozím nastavení Azure Maps nepřidá žádné navigační ovládací prvky do plátna mapy, jako jsou tlačítka lupy a tlačítka stylů mapy. Existují však ovládací prvky pro přidání výběru stylu mapy, tlačítek lupy, kompasu nebo ovládacího prvku otáčení a ovládacího prvku pro sklon.
- Do Azure Maps se přidá obslužná rutina události, která monitoruje událost `ready` instance mapy. Spustí se, až se mapa dokončí načítání WebGL kontextu a všech potřebných prostředků. V této obslužné rutině události lze přidat libovolný kód následného načtení.

Níže uvedené příklady ukazují, jak načíst základní mapu, která je zarovnána na střed v New Yorku v souřadnicích (zeměpisná délka:-73,985, zeměpisná šířka: 40,747) a je na úrovni přiblížení 12 v Mapách Google.

**Před: Google Maps**

Následující kód je příkladem zobrazení mapy Google na střed a přiblížení v rámci umístění.

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

<center>

![jednoduché mapy Google](media/migrate-google-maps-web-app/simple-google-map.png)</center>

**Po: Azure Maps**

Následující kód ukazuje, jak načíst mapu se stejným zobrazením v Azure Maps společně s ovládacími prvky stylu mapy a tlačítky zvětšení.

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

<center>

![jednoduchý Azure Maps](media/migrate-google-maps-web-app/simple-azure-maps.png)</center>

Podrobná dokumentace k nastavení a použití mapového ovládacího prvku Azure Maps ve webové aplikaci najdete [tady](how-to-use-map-control.md).

> [!NOTE]
> Na rozdíl od Google Maps Azure Maps nevyžaduje zadání počátečního centra a úrovně přiblížení, aby se načetla mapa. Pokud tyto informace není při načítání mapy k dispozici, mapa se pokusí zjistit, v jakém městě se uživatel nachází, a bude toto rozvržení vycentrovat a přiblížit.

**Další zdroje informací:**

- Azure Maps také poskytuje navigační ovládací prvky pro otočení a rozteč zobrazení mapy, jak je popsáno [zde](map-add-controls.md).

### <a name="localizing-the-map"></a>Lokalizace mapy

Pokud jsou vaše cílová skupina rozdělená do několika zemí nebo komunikují s různými jazyky, je lokalizace důležitá.

**Před: Google Maps**

K lokalizaci map Google Maps se přidávají parametry jazyka a oblasti.

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

Tady je příklad Google Maps s jazykem nastaveným na "fr-FR".

<center>

lokalizace ![Google Maps](media/migrate-google-maps-web-app/google-maps-localization.png)</center>

**Po: Azure Maps**

Azure Maps poskytuje dva různé způsoby nastavení jazyka a regionálního zobrazení mapy. První možností je přidat tyto informace do oboru názvů Global *Atlas* , což způsobí, že se všechny instance mapového ovládacího prvku ve vaší aplikaci budou ve výchozím nastavení nacházet. Následující nastaví jazyk na francouzštinu ("fr-FR") a místní zobrazení na "auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

Druhá možnost je předat tyto informace do možností mapy při načítání mapy jako:

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
> Pomocí Azure Maps je možné načíst více instancí mapy na stejné stránce s jiným nastavením jazyka a oblasti. Kromě toho je také možné aktualizovat Tato nastavení v mapě po jeho načtení. Podrobný seznam podporovaných jazyků v Azure Maps najdete [tady](supported-languages.md).

Tady je příklad Azure Maps s jazykem nastaveným na "fr" a oblastí uživatele nastavenou na "fr-FR".

<center>

![Azure Maps lokalizace](media/migrate-google-maps-web-app/azure-maps-localization.png)</center>

### <a name="setting-the-map-view"></a>Nastavení zobrazení mapy

Dynamické mapy v Azure i ve službě Google Maps lze programově přesunout do nových geografických umístění voláním příslušných funkcí v JavaScriptu. V níže uvedených příkladech se dozvíte, jak se má mapa zobrazit v satelitním notebooku, vycentrovat mapu na umístění s souřadnicemi (zeměpisná délka:-111,0225, zeměpisná šířka: 35,0272) a změnit úroveň přiblížení na 15 v Google Maps.

> [!NOTE]
> Google Maps používá v dimenzích dlaždice, které jsou 256 pixelů, zatímco Azure Maps používá větší dlaždici 512-pixel. Tím se sníží počet síťových požadavků, které Azure Maps k načtení stejné oblasti rozvržení jako Google Maps. Vzhledem k tomu, jak pracují pyramidy dlaždice v mapových ovládacích prvcích, jsou větší dlaždice v Azure Maps znamená, že k dosažení stejné zobrazitelné oblasti jako mapy ve službě Google Maps je třeba při použití Azure Maps odečíst úroveň přiblížení v Google Maps.

**Před: Google Maps**

Mapový ovládací prvek mapy Google se dá programově přesunout pomocí metody `setOptions`, která umožňuje určit střed mapy a úroveň přiblížení.

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![zobrazení sady Google Maps](media/migrate-google-maps-web-app/google-maps-set-view.png)</center>

**Po: Azure Maps**

V Azure Maps lze umístění mapy změnit programově pomocí metody `setCamera` mapy a změnu stylu mapy lze změnit pomocí metody `setStyle`. Všimněte si, že souřadnice v Azure Maps jsou ve formátu "Zeměpisná délka, zeměpisná šířka" a hodnota úrovně přiblížení je odečtena o jednu.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite'
});
```

<center>

zobrazení ![Azure Maps sady](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)</center>

**Další zdroje informací:**

- [Zvolit styl mapy](choose-map-style.md)
- [Podporované styly mapy](supported-map-styles.md)

### <a name="adding-a-marker"></a>Přidání značky

V Azure Maps existuje více způsobů, jak lze na mapě vykreslovat data.

- **Značky HTML** – vykresluje body pomocí tradičních elementů DOM. Značky HTML podporují přetahování.
- **Vrstva symbolu** – vykreslí body s ikonou nebo textem v rámci WebGL kontextu.
- **Bublinová vrstva** – vykreslí body jako kružnice na mapě. Poloměry kroužků se dají škálovat na základě vlastností v datech.

Symbol i bublinové vrstvy jsou vykresleny v rámci WebGL kontextu a je možné vykreslovat velmi velké sady bodů na mapě. Tyto vrstvy vyžadují, aby data byla uložená ve zdroji dat. Zdroje dat a vrstvy vykreslování by měly být přidány do mapy poté, co událost `ready` vyvolala. Značky HTML se vykreslují jako prvky modelu DOM na stránce a nepoužívají zdroj dat. Čím více elementů modelu DOM má stránku, tím pomalejší stránka bude. Pokud je vykreslování více než několik stovek bodů na mapě, doporučuje se místo toho použít jednu z vrstev vykreslování.

Následující příklady přidávají značku na mapu (zeměpisná délka:-0,2, zeměpisná šířka: 51,5) s číslem 10 překrytím jako popisek.

**Před: Google Maps**

Pomocí Map Google jsou značky přidány do mapy pomocí třídy `google.maps.Marker` a zadáním mapy jako jedné z možností.

```javascript
//Create a marker and add it to the map.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    label: '10',
    map: map
});
```

<center>

](media/migrate-google-maps-web-app/google-maps-marker.png)</center> značka ![mapy Google

**Po: Azure Maps použití značek HTML**

V Azure Maps se značky HTML dají použít k zobrazení bodu na mapě a jsou doporučené pro jednoduché aplikace, které potřebují jenom zobrazit malý počet bodů na mapě. Chcete-li použít značku HTML, stačí vytvořit instanci třídy `atlas.HtmlMarker`, nastavit možnosti textu a pozice a přidat značku na mapu pomocí metody `map.markers.add`.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![Azure Maps značka HTML](media/migrate-google-maps-web-app/azure-maps-html-marker.png)</center>

**Po: Azure Maps používání vrstvy symbolů**

Při použití vrstvy symbolů musí být data přidána ke zdroji dat a zdroji dat připojenému ke vrstvě. Kromě toho by se měl zdroj dat a vrstva přidat do mapy po vyvolání události `ready`. Chcete-li vykreslit jedinečnou textovou hodnotu nad symbolem, je nutné, aby textové informace byly uloženy jako vlastnost datového bodu a tato vlastnost odkazovala v `textField` možnosti vrstvy. Jedná se o trochu větší práci než při použití značek HTML, ale poskytuje mnoho výhod výkonu.

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

<center>

![Azure Maps symbolovou vrstvu](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)</center>

**Další zdroje informací:**

- [Vytvoření zdroje dat](create-data-source-web-sdk.md)
- [Přidat vrstvu symbolů](map-add-pin.md)
- [Přidat bublinovou vrstvu](map-add-bubble-layer.md)
- [Data bodu clusteru](clustering-point-data-web-sdk.md)
- [Přidat značky HTML](map-add-custom-html.md)
- [Použití výrazů stylu založených na datech](data-driven-style-expressions-web-sdk.md)
- [Možnosti ikony vrstvy symbolů](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Možnost textu vrstvy symbolu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [Třída značek HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Možnosti značky HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-custom-marker"></a>Přidání vlastní značky

Vlastní image lze použít k reprezentaci bodů na mapě. Následující obrázek se používá v níže uvedených příkladech použití vlastní image k zobrazení bodu na mapě (zeměpisná šířka: 51,5, zeměpisná délka:-0,2) a Posune pozici značky tak, aby se bod ikony připínáčku rovnal se správnou polohou na mapě.

<center>

![žlutý obrázek připínáček](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
YLW\_připínáček. png</center>

**Před: Google Maps**

Ve službě Google Maps je vlastní značka vytvořena zadáním objektu `Icon`, který obsahuje `url` na obrázek, `anchor` bod pro zarovnání bodu obrázku připínáčku s souřadnicí na mapě. Hodnota kotvy v Mapách Google vzhledem k levému hornímu rohu obrázku

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

<center>

![vlastní značky pro Google Maps](media/migrate-google-maps-web-app/google-maps-custom-marker.png)</center>

**Po: Azure Maps použití značek HTML**

Chcete-li přizpůsobit značku HTML v Azure Maps `string` HTML nebo `HTMLElement` lze předat do `htmlContent` možnosti značky. V Azure Maps slouží možnost `anchor` k určení relativní pozice značky vzhledem k souřadnici pozice pomocí jednoho z devíti definovaných referenčních bodů. "Center", "Top", "Bottom", "Left", "Right", "Top-left", "top-right", "Bottom-left", "Bottom-Right". Ve výchozím nastavení je obsah ukotven ke spodnímu středu obsahu HTML. Aby se zjednodušila migrace kódu z Google Maps, nastavte `anchor` na "vlevo doleva" a pak použijte možnost `pixelOffset` se stejným posunem použitým v Mapách Google. Posuny v Azure Maps se pohybují v opačném směru mapy Google Maps, takže je vynásobí minus jedna.

> [!TIP]
> Přidejte `pointer-events:none` jako styl obsahu HTML, abyste zakázali výchozí chování při přetahování v Microsoft Edge, které zobrazí nežádoucí ikonu.

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Azure Maps vlastní značku HTML](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)</center>

**Po: Azure Maps používání vrstvy symbolů**

Vrstvy symbolů v Azure Maps podporují i vlastní image, ale image se nejdřív musí načíst do prostředků mapy a přiřadit jim jedinečné ID. Vrstva symbolů pak může odkazovat na toto ID. Symbol může být posunut pro zarovnání ke správnému bodu na obrázku pomocí ikony `offset` možnosti. V Azure Maps slouží možnost `anchor` k určení relativní pozice symbolu relativně k souřadnici pozice pomocí jednoho z devíti definovaných referenčních bodů. "Center", "Top", "Bottom", "Left", "Right", "Top-left", "top-right", "Bottom-left", "Bottom-Right". Ve výchozím nastavení je obsah ukotven ke spodnímu středu obsahu HTML. Aby se zjednodušila migrace kódu z Google Maps, nastavte `anchor` na "vlevo doleva" a pak použijte možnost `offset` se stejným posunem použitým v Mapách Google. Posuny v Azure Maps se pohybují v opačném směru mapy Google Maps, takže je vynásobí minus jedna.

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

<center>

![Azure Maps vlastní vrstvy symbolu ikony](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</center>

> [!TIP]
> Chcete-li vytvořit rozšířené vlastní vykreslování bodů, použijte více vrstev vykreslování dohromady. Například pokud chcete mít více špendlíků, které mají stejnou ikonu na různých barevných kruhech, místo vytvoření řady obrázků pro každou barvu překrytí vrstvy symbolů nad bublinovou vrstvu a jejich odkazování na stejný zdroj dat. To bude mnohem efektivnější než vytváření a mapa bude udržovat spoustu různých imagí.

**Další zdroje informací:**

- [Vytvoření zdroje dat](create-data-source-web-sdk.md)
- [Přidat vrstvu symbolů](map-add-pin.md)
- [Přidat značky HTML](map-add-custom-html.md)
- [Použití výrazů stylu založených na datech](data-driven-style-expressions-web-sdk.md)
- [Možnosti ikony vrstvy symbolů](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Možnost textu vrstvy symbolu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [Třída značek HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Možnosti značky HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-polyline"></a>Přidání lomené čáry

Lomené čáry slouží k reprezentaci čáry nebo cesty na mapě. Následující příklady ukazují, jak vytvořit přerušovanou lomenou čáru na mapě.

**Před: Google Maps**

Ve službě Google Maps používá třída lomená sada možností. Pole souřadnic je předáno v možnosti `path` lomené čáry.

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

<center>

![](media/migrate-google-maps-web-app/google-maps-polyline.png)</center> na lomenou čáru mapy Google

**Po: Azure Maps**

V Azure Maps se lomené čáry nazývají objekty LineString nebo MultiLineString. Tyto objekty lze přidat do zdroje dat a vykresleny pomocí spojnicové vrstvy.

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

<center>

![Azure Maps lomená čáry](media/migrate-google-maps-web-app/azure-maps-polyline.png)</center>

**Další zdroje informací:**

- [Přidat řádky do mapy](map-add-line-layer.md)
- [Možnosti vrstvy čáry](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Použití výrazů stylu založených na datech](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Přidání mnohoúhelníku

Mnohoúhelníky slouží k reprezentaci oblasti na mapě. Mapy Azure Maps a Google poskytují velmi podobnou podporu pro mnohoúhelníky. Následující příklady ukazují, jak vytvořit mnohoúhelník, který tvoří trojúhelník založený na souřadnici středu mapy.

**Před: Google Maps**

Ve službě Google Maps používá třída mnohoúhelník sadu možností. Pole souřadnic je předáno v možnosti `paths` mnohoúhelníku.

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

<center>

](media/migrate-google-maps-web-app/google-maps-polygon.png)</center> mnohoúhelníku mapy Google ![

**Po: Azure Maps**

V Azure Maps lze objekty mnohoúhelníku a více mnohoúhelníků přidat do zdroje dat a vykreslit na mapě pomocí vrstev. Oblast mnohoúhelníku se dá vykreslit v mnohoúhelníkové vrstvě. Obrys mnohoúhelníku lze vykreslit pomocí spojnicové vrstvy.

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

<center>

![Azure Maps mnohoúhelníku](media/migrate-google-maps-web-app/azure-maps-polygon.png)</center>

**Další zdroje informací:**

- [Přidat mnohoúhelník k mapě](map-add-shape.md)
- [Přidání kruhu k mapě](map-add-shape.md#add-a-circle-to-the-map)
- [Možnosti vrstvy mnohoúhelníku](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)
- [Možnosti vrstvy čáry](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Použití výrazů stylu založených na datech](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>Zobrazit informační okno

Další informace o entitě lze zobrazit v mapě jako třídu `google.maps.InfoWindow` ve službě Google Maps. v Azure Maps to lze dosáhnout pomocí třídy `atlas.Popup`. Následující příklady přidávají značku na mapu a při kliknutí na tlačítko Zobrazit informační okno nebo automaticky otevírané okno.

**Před: Google Maps**

Pomocí Map Google se vytvoří informační okno s použitím konstruktoru `google.maps.InfoWindow`.

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

<center>

automaticky otevíraná okna mapy Google ![](media/migrate-google-maps-web-app/google-maps-popup.png)</center>

**Po: Azure Maps**

V Azure Maps se místní nabídka dá použít k zobrazení dalších informací o umístění. Objekt HTML `string` nebo objekt `HTMLElement` lze předat do možnosti `content` automaticky otevírané okno. Automaticky otevíraná okna se dají v případě potřeby zobrazit nezávisle na jakémkoli tvaru, takže se musí zadat hodnota `position`. Chcete-li zobrazit místní nabídku, zavolejte metodu `open` a předejte ji do `map`, ve kterém se má automaticky otevíraná okna zobrazit.

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

<center>

automaticky otevíraná okna ![Azure Maps](media/migrate-google-maps-web-app/azure-maps-popup.png)</center>

> [!NOTE]
> Chcete-li provést stejnou věc se symbolem, bublinou, čárou nebo mnohoúhelníkovou vrstvou, jednoduše předejte vrstvu do kódu události Maps místo do značky.

**Další zdroje informací:**

- [Přidat automaticky otevírané okno](map-add-popup.md)
- [Automaticky otevírané okno s mediálním obsahem](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [Automaticky otevíraná okna v obrazcích](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [Opakované použití automaticky otevíraného okna s více PIN kódy](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [Automaticky otevíraná třída](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
- [Možnosti místního okna](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

### <a name="import-a-geojson-file"></a>Importovat soubor. JSON

Mapy Google podporují načítání a dynamické stylování dat o úrovni data ve formátu. JSON prostřednictvím třídy `google.maps.Data`. Funkce této třídy zarovnává mnohem více se stylem založeným na datech Azure Maps. Jedním z klíčových rozdílů je, že při použití funkce zpětného volání a obchodní logiky pro každou funkci, která se zpracovává jednotlivě ve vláknu UI, určíte funkci zpětného volání a obchodní logiku V Azure Maps vrstvy podporují určení výrazů řízených daty jako možnosti stylu. Tyto výrazy jsou zpracovávány v době vykreslování v samostatném vlákně a poskytují zvýšený výkon vykreslování a umožňují rychleji vykreslovat větší sady dat.

V následujících příkladech je načten informační kanál pro horizontální JSON zemětřesení za posledních sedm dní od sadě USGS a vykresluje je jako kruhy na mapě. Barva a měřítko jednotlivých kruhů jsou založené na velikosti každého zemětřesení, který je uložený ve vlastnosti `"mag"` jednotlivých funkcí v sadě dat. Pokud je velikost větší než nebo rovna pěti, bude kroužek červeně, pokud je větší nebo roven třem, ale menší než pět, kruh bude oranžová, pokud je menší než tři, kroužek bude zelený. Poloměr každého kruhu bude exponenciálně vynásobený z rozsahu, který vynásobí 0,1.

**Před: Google Maps**

V rámci služby Google Maps lze v metodě `map.data.setStyle` zadat jedinou funkci zpětného volání, která bude použita k použití obchodní logiky pro každou funkci navedenou z informačního kanálu typu injson prostřednictvím metody `map.data.loadGeoJson`.

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

<center>

![](media/migrate-google-maps-web-app/google-maps-geojson.png)</center> pro injson pro Google Maps

**Po: Azure Maps**

Injson je základní datový typ v Azure Maps a lze jej snadno importovat do zdroje dat pomocí metody `datasource.importFromUrl`. Bublinová vrstva poskytuje funkce pro vykreslování kruhů s měřítkem na základě vlastností funkcí ve zdroji dat. Namísto funkce zpětného volání je obchodní logika převedena na výraz a předána do možností stylu. Výrazy definují, jak obchodní logika funguje, aby mohla být předána do jiného vlákna a vyhodnocována proti datům funkce. Do Azure Maps je možné přidat několik zdrojů dat a vrstev s různými obchodními logikami, aby bylo možné na mapě vykreslovat více datových sad různými způsoby.

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

<center>

![Azure Mapsal JSON](media/migrate-google-maps-web-app/azure-maps-geojson.png)</center>

**Další zdroje informací:**

- [Přidat vrstvu symbolů](map-add-pin.md)
- [Přidat bublinovou vrstvu](map-add-bubble-layer.md)
- [Data bodu clusteru](clustering-point-data-web-sdk.md)
- [Použití výrazů stylu založených na datech](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>Clusteringu značek

Když vizualizujete řadu datových bodů na mapě, navzájem se navzájem překrývají mapa, zdá se, že mapa vypadá a že je obtížné ji zobrazit a použít. Clusterová data lze využít ke zlepšení tohoto uživatelského prostředí a také ke zvýšení výkonu. Data bodu clusteringu je proces kombinování bodových dat blízko sebe a jejich reprezentace na mapě jako jeden clusterovaný datový bod. Když se uživatel přiblíží k mapě, rozdělí se clustery do jednotlivých datových bodů.

Následující příklady načtou informační kanál pro zemětřesení dat z minulého týdne a přidá ho do mapy. Clustery se vykreslují jako škálované a barevné kružnice v závislosti na počtu bodů, které obsahují.

> [!NOTE]
> Pro značku clusteringu se používá několik různých algoritmů. Google a Azure Maps používají mírně odlišné algoritmy. Někdy se může stát, že se distribuce bodu v clusterech liší.

**Před: Google Maps**

V rámci značek Google Maps je možné clusterovat načtením v knihovně MarkerClusterer. Ikony clusteru jsou omezeny na bitové kopie, které mají čísla jedna až pět jako jejich název a jsou hostovány ve stejném adresáři.

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

<center>

![](media/migrate-google-maps-web-app/google-maps-clustering.png)</center> clusteru Google Maps

**Po: Azure Maps**

V Azure Maps se data přidávají a spravují zdrojem dat. Vrstvy se připojují ke zdrojům dat a vykreslují data v nich. Třída `DataSource` v Azure Maps poskytuje několik možností clusteringu.

- `cluster` – instruuje zdroj dat na data bodu clusteru.
- `clusterRadius` – poloměr propojených bodů clusteru v pixelech.
- `clusterMaxZoom` – maximální úroveň přiblížení, při které dojde k clusteringu. Pokud přiblížíte více než to, všechny body se vykreslí jako symboly.
- `clusterProperties` – definuje vlastní vlastnosti, které se vypočítávají pomocí výrazů pro všechny body v jednotlivých clusterech a přidají se do vlastností každého bodu clusteru.

Když je clustering povolený, bude zdroj dat odesílat clusterované a neseskupené datové body do vrstev pro vykreslování. Zdroj dat je schopný clusterovat stovky tisíc datových bodů. Clusterovaný datový bod má následující vlastnosti:

| Název vlastnosti             | Typ    | Popis   |
|---------------------------|---------|---------------|
| `cluster`                 | Boolean | Indikuje, že funkce představuje cluster. |
| `cluster_id`              | string  | Jedinečné ID clusteru, které lze použít s metodami DataSource `getClusterExpansionZoom`, `getClusterChildren`a `getClusterLeaves`. |
| `point_count`             | číslo  | Počet bodů, které cluster obsahuje.  |
| `point_count_abbreviated` | string  | Řetězec, který zkrátí `point_count` hodnotu, pokud je Long. (například 4 000 se bude 4K)  |

Třída `DataSource` má následující pomocnou funkci pro přístup k dalším informacím o clusteru pomocí `cluster_id`.

| Metoda | Návratový typ | Popis |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | &lt;funkce&lt;Promise&lt;geometrii, všechny&gt; \| tvar&gt;&gt; | Načte podřízené objekty daného clusteru na další úrovni přiblížení. Tyto podřízené položky mohou být kombinací tvarů a podclusterů. Podclustery budou funkcemi s vlastnostmi, které odpovídají ClusteredProperties. |
| `getClusterExpansionZoom(clusterId: number)` | Číslo&lt;Promise&gt; | Vypočítá úroveň přiblížení, při které se cluster začne zvětšovat nebo rozdělovat. |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | &lt;funkce&lt;Promise&lt;geometrii, všechny&gt; \| tvar&gt;&gt; | Načte všechny body v clusteru. Nastavte `limit` pro vrácení podmnožiny bodů a použijte `offset` ke stránkám prostřednictvím bodů. |

Při vykreslování clusterovaných dat na mapě je často nejjednodušší použít dvě nebo více vrstev. Následující příklad používá tři vrstvy, bublinovou vrstvu pro kreslení barevných paprsků škálované na základě velikosti clusterů, vrstvy symbolů pro vykreslení velikosti clusteru jako textu a druhé vrstvy symbolů pro vykreslování neclusterovaných bodů. Existuje mnoho dalších způsobů, jak vykreslovat data v clusterech v Azure Maps zvýrazněná v dokumentaci k [datům bodu clusteru](clustering-point-data-web-sdk.md) .

Data o úrovni injson je možné přímo importovat v Azure Maps pomocí funkce `importDataFromUrl` na třídě `DataSource`.

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

<center>

![clusteringu Azure Maps](media/migrate-google-maps-web-app/azure-maps-clustering.png)</center>

**Další zdroje informací:**

- [Přidat vrstvu symbolů](map-add-pin.md)
- [Přidat bublinovou vrstvu](map-add-bubble-layer.md)
- [Data bodu clusteru](clustering-point-data-web-sdk.md)
- [Použití výrazů stylu založených na datech](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Přidat Heat mapu

Heat mapy, označované také jako mapy hustoty bodů, jsou typem vizualizace dat, která představuje hustotu dat pomocí rozsahu barev. Často se používají k zobrazení dat "aktivních bodů" na mapě a představují skvělý způsob, jak vykreslovat sady velkých bodů dat.

Následující příklady načtou v sadě USGS informační kanál zemětřesení za minulý měsíc z a vykreslí je jako vážená Heat mapa, kde se jako váha používá vlastnost `"mag"`.

**Před: Google Maps**

Aby bylo možné vytvořit Heat mapu, je třeba v části Google Maps načíst knihovnu "vizualizace" přidáním `&libraries=visualization` k adrese URL skriptu rozhraní API. Vrstva Heat mapy v Google Maps nepodporuje přímo data typu data JSON a místo toho je potřeba je nejdřív stáhnout a převést na pole váženého počtu datových bodů.

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

<center>

![Heat mapa Google Maps](media/migrate-google-maps-web-app/google-maps-heatmap.png)</center>

**Po: Azure Maps**

V Azure Maps načtěte data o úrovni injson do zdroje dat a propojte zdroj dat s vrstvou Heat mapy. Vlastnost, která se použije pro váhu, se dá předat do možnosti `weight` pomocí výrazu. Data o úrovni injson je možné přímo importovat v Azure Maps pomocí funkce `importDataFromUrl` na třídě `DataSource`.

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

<center>

![Azure Maps Heat mapa](media/migrate-google-maps-web-app/azure-maps-heatmap.png)</center>

**Další zdroje informací:**

- [Přidat vrstvu Heat mapy](map-add-heat-map-layer.md)
- [Třída vrstvy tepelné mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.heatmaplayer?view=azure-iot-typescript-latest)
- [Možnosti vrstvy tepelné mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)
- [Použití výrazů stylu založených na datech](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Překrytí vrstvy dlaždice

Vrstvy dlaždic, označované také jako překryvy obrázků ve službě Google Maps, umožňují překrývat velké obrázky, které byly rozdělené do menších dlaždic, které se zarovnají se systémem mapy dláždění. To je běžný způsob, jak překrýt velké obrázky nebo velmi velké datové sady.

V následujících příkladech je překrytá vrstva s paprskovou dlaždicí počasí z Iowaho prostředí Mesonet of Iowa State University.

**Před: Google Maps**

V Mapách Google lze vrstvy dlaždic vytvořit pomocí třídy `google.maps.ImageMapType`.

```javascript
map.overlayMapTypes.insertAt(0, new google.maps.ImageMapType({
    getTileUrl: function (coord, zoom) {
        return "https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/" + zoom + "/" + coord.x + "/" + coord.y;
    },
    tileSize: new google.maps.Size(256, 256),
    opacity: 0.8
}));
```

<center>

![vrstva dlaždice mapy Google](media/migrate-google-maps-web-app/google-maps-tile-layer.png)</center>

**Po: Azure Maps**

V Azure Maps lze do mapy přidat vrstvu dlaždice podobným způsobem jako jakákoli jiná vrstva. Naformátovaná adresa URL, která má ve tvaru x, y, zástupné symboly přiblížení; `{x}`, `{y}``{z}` v uvedeném pořadí slouží k oznámení vrstvy, kde má být přístup k dlaždicím. Vrstvy dlaždic Azure Maps také podporují zástupné symboly `{quadkey}`, `{bbox-epsg-3857}` a `{subdomain}`.

> [!TIP]
> V Azure Maps vrstev lze snadno vykreslovat pod jinými vrstvami, včetně základních vrstev mapy. Často je žádoucí vykreslovat vrstvy dlaždice pod popisky map, aby byly snadno čitelné. Metoda `map.layers.add` přebírá druhý parametr, který je identifikátorem vrstvy, do které chcete vložit novou vrstvu. Chcete-li vložit vrstvu dlaždice pod popisky map, lze použít následující kód: `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

vrstva dlaždic ![Azure Maps](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)</center>

> [!TIP]
> Žádosti o dlaždici se dají zachytit pomocí možnosti `transformRequest` mapy. V případě potřeby vám umožní upravit nebo přidat hlavičky do žádosti.

**Další zdroje informací:**

- [Přidat vrstvy dlaždic](map-add-tile-layer.md)
- [Třída dlaždic vrstev](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)
- [Možnosti vrstvy dlaždic](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

### <a name="show-traffic"></a>Zobrazit provoz

Data přenosů se dají překrývají v Azure i ve službě Google Maps.

**Před: Google Maps**

V datech mapy Google je možné mapu překrytí pomocí vrstvy přenosů.

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```

<center>

![přenosů Google Maps](media/migrate-google-maps-web-app/google-maps-traffic.png)</center>

**Po: Azure Maps**

Azure Maps poskytuje několik různých možností pro zobrazení provozu. Incidenty přenosu, jako jsou například uzávěry a havárie provozu, lze zobrazit jako ikony na mapě. Tok přenosů, barevně rozlišené silnice lze na mapě překrytí a barvy je možné upravit tak, aby se využívaly relativně k zadanému limitu rychlosti, relativně k normálnímu očekávanému zpoždění nebo absolutnímu zpoždění. Data incidentu v Azure Maps jsou aktualizována každou minutu a data toku každé dvě minuty.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

](media/migrate-google-maps-web-app/azure-maps-traffic.png)</center> Azure Maps provozu ![

Pokud kliknete na jednu z ikon přenosů v Azure Maps, zobrazí se další informace v automaticky otevřeném okně.

<center>

](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)</center> incidentu ![Azure Maps provozu

**Další zdroje informací:**

- [Zobrazit provoz na mapě](map-show-traffic.md)
- [Možnosti překrytí provozu](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>Přidání překrytí základní desky

Mapy Azure i Google podporují překrytí obrazových obrázků na mapě tak, aby se při posouvání a škálování na mapě přesunuly a přiblížily. V Google Maps se označují jako základní překrytí v Azure Maps jsou označovány jako vrstvy obrázků. Ty jsou skvělé pro vytváření plánů podlaží, překrývání starých map nebo k použití snímků z pomocí dronů.

**Před: Google Maps**

Při vytváření překryvných překryvů v Google Maps potřebujete zadat adresu URL obrázku, který se má překrýt, a ohraničovací rámeček, na který se má navázat obrázek na mapě. Tento příklad překrývá obrázek mapy [newyorském New Jersey z 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) na mapě.

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

<center>

![překryv obrázků Google Maps](media/migrate-google-maps-web-app/google-maps-image-overlay.png)</center>

**Po: Azure Maps**

V Azure Maps mohou být neodkazované obrázky překryty pomocí `atlas.layer.ImageLayer` třídy. Tato třída vyžaduje adresu URL obrázku a sadu souřadnic pro čtyři rohy obrázku. Bitová kopie musí být hostována buď ve stejné doméně, nebo musí mít povolenou CORs.

> [!TIP]
> Pokud máte jenom informace o Severní, Jižní, východní, západní a rotační oblasti a ne souřadnice pro každý roh obrázku, můžete použít metodu static [`atlas.layer.ImageLayer.getCoordinatesFromEdges`](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest#getcoordinatesfromedges-number--number--number--number--number-) .

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

<center>

překrytí obrázku ![Azure Maps](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)</center>

**Další zdroje informací:**

- [Překrytí obrázku](map-add-image-layer.md)
- [Třída image Layer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

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
- [Zobrazit směr od A do B](map-route.md)
- [Vyhledat automatické návrhy pomocí uživatelského rozhraní JQuery](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Mapování tříd webu Google Maps V3 na Azure Maps Web SDK

Následující dodatek poskytuje mapování odkazů nejčastěji používaných tříd v Google Maps V3 na jejich ekvivalenty Azure Maps Web SDK.

### <a name="core-classes"></a>Základní třídy

| Mapy Google   | Azure Maps  |
|---------------|-------------|
| `google.maps.Map` | [Tamazight. Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindow` | [Tamazight. Oken](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindowOptions` | [Tamazight. PopupOptions](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [Atlas. data. pozice](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest)  |
| `google.maps.LatLngBounds` | [Atlas. data. BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) |
| `google.maps.MapOptions`  | [Tamazight. CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions?view=azure-iot-typescript-latest)<br/>[Tamazight. CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions?view=azure-iot-typescript-latest)<br/>[Tamazight. ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions?view=azure-iot-typescript-latest)<br/>[Tamazight. StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions?view=azure-iot-typescript-latest)<br/>[Tamazight. UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions?view=azure-iot-typescript-latest) |
| `google.maps.Point`  | [Tamazight. Pixel](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.pixel?view=azure-iot-typescript-latest)   |

## <a name="overlay-classes"></a>Třídy překrytí

| Mapy Google  | Azure Maps  |
|--------------|-------------|
| `google.maps.Marker` | [Tamazight. HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)<br/>[Atlas. data. Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)  |
| `google.maps.MarkerOptions`  | [Tamazight. HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)<br/>[Atlas. Layer. SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)<br/>[Tamazight. SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)<br/>[Tamazight. IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)<br/>[Tamazight. TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)<br/>[Atlas. Layer. BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)<br/>[Tamazight. BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.Polygon`  | [Atlas. data. mnohoúhelník](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)               |
| `google.maps.PolygonOptions` |[Atlas. Layer. PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)<br/> [Tamazight. PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)<br/> [Atlas. Layer. LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)<br/> [Tamazight. LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)|
| `google.maps.Polyline` | [Atlas. data. LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest)         |
| `google.maps.PolylineOptions` | [Atlas. Layer. LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-maps-typescript-latest)<br/>[Tamazight. LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-maps-typescript-latest) |
| `google.maps.Circle`  | Viz [Přidání kruhu k mapě](map-add-shape.md#add-a-circle-to-the-map) .                                     |
| `google.maps.ImageMapType`  | [Tamazight. TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)         |
| `google.maps.ImageMapTypeOptions` | [Tamazight. TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.GroundOverlay`  | [Atlas. Layer. ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)<br/>[Tamazight. ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest) |

## <a name="service-classes"></a>Třídy služeb

Sada Azure Maps Web SDK obsahuje [modul služeb](how-to-use-services-module.md) , který lze načíst samostatně. Tento modul balí služby Azure Maps REST s webovým rozhraním API a dá se použít v aplikacích JavaScript, TypeScript a Node. js.

| Mapy Google | Azure Maps  |
|-------------|-------------|
| `google.maps.Geocoder` | [Atlas. Service. SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |
| `google.maps.GeocoderRequest`  | [Tamazight. SearchAddressOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions?view=azure-iot-typescript-latest)<br/>[Tamazight. SearchAddressRevrseOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions?view=azure-iot-typescript-latest)<br/>[Tamazight. SearchAddressReverseCrossStreetOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions?view=azure-iot-typescript-latest)<br/>[Tamazight. SearchAddressStructuredOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions?view=azure-iot-typescript-latest)<br/>[Tamazight. SearchAlongRouteOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions?view=azure-iot-typescript-latest)<br/>[Tamazight. SearchFuzzyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions?view=azure-iot-typescript-latest)<br/>[Tamazight. SearchInsideGeometryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions?view=azure-iot-typescript-latest)<br/>[Tamazight. SearchNearbyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions?view=azure-iot-typescript-latest)<br/>[Tamazight. SearchPOIOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoioptions?view=azure-iot-typescript-latest)<br/>[Tamazight. SearchPOICategoryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions?view=azure-iot-typescript-latest) |
| `google.maps.DirectionsService`  | [Atlas. Service. RouteUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)  |
| `google.maps.DirectionsRequest`  | [Tamazight. CalculateRouteDirectionsOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions?view=azure-iot-typescript-latest) |
| `google.maps.places.PlacesService` | [Atlas. Service. SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |

## <a name="libraries"></a>Knihovny

Knihovny přidávají k mapě další funkce. Mnohé z nich jsou v základní sadě SDK Azure Maps. Tady jsou některé ekvivalentní třídy, které se mají použít místo těchto knihoven Google Maps.

| Mapy Google           | Azure Maps   |
|-----------------------|--------------|
| Knihovna kreslení       | [Modul nástrojů pro kreslení](set-drawing-options.md) |
| Knihovna geometrie      | [Atlas. Math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)   |
| Knihovna vizualizace | [Vrstva tepelné mapy](map-add-heat-map-layer.md) |

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o Azure Maps Web SDK.

> [!div class="nextstepaction"]
> [Jak používat mapový ovládací prvek](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Jak používat modul služeb](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Jak používat modul nástrojů pro kreslení](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Ukázky kódu](https://docs.microsoft.com/samples/browse/?products=azure-maps)


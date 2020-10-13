---
title: 'Kurz: migrace webové aplikace z map Bingu | Mapy Microsoft Azure'
description: Kurz týkající se migrace webové aplikace z map Bing na Microsoft Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 9/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: devx-track-js
ms.openlocfilehash: 42ba92a0134ae1e8da91bbe7513668fa24c4718f
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876511"
---
# <a name="tutorial---migrate-a-web-app-from-bing-maps"></a>Kurz – migrace webové aplikace z map Bing

Webové aplikace, které používají mapy Bing, často používají sadu SDK služby Bing Maps V8 JavaScript. Azure Maps Web SDK je vhodná sada SDK založená na Azure, na kterou se má migrovat. Sada Azure Maps Web SDK umožňuje přizpůsobit interaktivní mapy s vlastním obsahem a pomocí obrázků pro zobrazení ve vašich webových nebo mobilních aplikacích. Tento ovládací prvek využívá WebGL a umožňuje vykreslovat rozsáhlé datové sady s vysokým výkonem. Pomocí JavaScriptu nebo TypeScript se budete vyvíjet pomocí této sady SDK.

Pokud migrujete existující webovou aplikaci, zkontrolujte, zda je použita knihovna Open Source ovládacího prvku mapa, například cesium, leták a OpenLayers. Pokud je to a chcete, aby se tato knihovna dál používala, můžete ji připojit ke službám Azure Maps dlaždice ([cesty](https://docs.microsoft.com/rest/api/maps/render/getmaptile) \| [satelitních](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)bloků). Odkazy níže poskytují podrobné informace o tom, jak používat Azure Maps v některých běžně používaných Open Source knihovnách ovládacích prvků.

-   Cesium – ovládací prvek 3D mapy pro web. [Ukázka kódu](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [Dokumentace](https://cesiumjs.org/)
-   Leták – zjednodušený 2D mapový ovládací prvek pro web. [Ukázka kódu](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [Dokumentace](https://leafletjs.com/)
-   OpenLayers – 2D ovládací prvek mapy pro web, který podporuje projekce. [Ukázka kódu](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [Dokumentace](https://openlayers.org/)

Při vývoji pomocí JavaScriptu rozhraní může být užitečné jeden z následujících open-source projektů:

- [NG-Azure-Maps](https://github.com/arnaudleclerc/ng-azure-maps) -úhlová Obálka kolem Azure Maps.
- [AzureMapsControl. Components](https://github.com/arnaudleclerc/AzureMapsControl.Components) – komponenta Azure Maps Blazor.
- [Azure Maps reagující na komponentu](https://github.com/WiredSolutions/react-azure-maps) – reakce na reakci ovládacího prvku Azure Maps.
- [Vue Azure Maps](https://github.com/rickyruiz/vue-azure-maps) – komponenta Azure Maps pro aplikaci Vue.

## <a name="key-features-support"></a>Podpora klíčových funkcí

V následující tabulce jsou uvedeny klíčové funkce API ve službě Bing Maps V8 JavaScript SDK a podpora podobného rozhraní API v sadě Azure Maps Web SDK.

| Funkce mapy Bing        | Podpora Azure Maps Web SDK                                                             |
|--------------------------|:--------------------------------------------------------------------------------------:|
| Připínáčků                 | ✓                                                                                      |
| Clustering připínáček       | ✓                                                                                      |
| Lomené čáry & mnohoúhelníků     | ✓                                                                                      |
| Základní překryvy          | ✓                                                                                      |
| Heat mapy                | ✓                                                                                      |
| Vrstvy dlaždic              | ✓                                                                                      |
| KML vrstva                | ✓                                                                                      |
| Vrstva obrysu            | [ukázky](https://azuremapscodesamples.azurewebsites.net/?search=contour)              |
| Vrstva binningu dat       | [ukázky](https://azuremapscodesamples.azurewebsites.net/?search=data%20binning)       |
| Animovaná vrstva dlaždice      | Zahrnutý v modulu Open Source Azure Maps [Animation](https://github.com/Azure-Samples/azure-maps-animations) |
| Nástroje pro kreslení            | ✓                                                                                      |
| Služba pro INCODE         | ✓                                                                                      |
| Služba itinerář       | ✓                                                                                      |
| Služba matice Distance  | ✓                                                                                      |
| Prostorová data – služba     | Není k dispozici                                                                                    |
| Satelitní/letecká satelitní obrázek | ✓                                                                                      |
| Ptactvo z ptačích očí         | Plánováno                                                                                |
| Streetsideí snímků       | Plánováno                                                                                |
| Podpora pro injson          | ✓                                                                                      |
| Podpora GeoXML           | ✓                                                                                      |
| Podpora textu Well-Known  | ✓                                                                                      |
| Vlastní styly mapy        | Částečné                                                                                |

Azure Maps také obsahuje mnoho dalších [Open Source modulů pro webovou sadu SDK](open-source-projects.md#open-web-sdk-modules) , které rozšiřuje své možnosti.

## <a name="notable-differences-in-the-web-sdks"></a>Významné rozdíly v sadách web SDK

Níže jsou uvedeny některé z klíčových rozdílů mezi mapami Bing a Azure Maps Web SDK, na kterých je třeba vědět:

-   Kromě poskytování hostovaného koncového bodu pro přístup k Azure Maps webové sadě SDK je k dispozici také balíček NPM pro vložení webové sady SDK do aplikací, pokud jsou preferované. Další informace najdete v této [dokumentaci](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control) , kde najdete další informace. Tento balíček obsahuje také definice TypeScript.
-   Mapy Bing poskytují dvě hostované větve své sady SDK. Vydávat a experimentovat. Experimentální větev může obdržet několik aktualizací za den, kdy dojde k vývoji nového vývoje. Azure Maps pouze hostitelskou větev verze, ale experimentální funkce jsou vytvořeny jako vlastní moduly v projektu ukázek Open-Source Azure Maps kódu. Mapy Bing používaly k dispozici zmrazenou větev, která byla aktualizována méně často, čímž se snížilo riziko zásadních změn z důvodu vydání verze. V Azure Maps můžete použít modul NPM a nasměrovat ho na předchozí verzi dílčí verze.

> [!TIP]
> Azure Maps zveřejňuje minifikovaného i unminified verze sady SDK. Jednoduché odebrání `.min` z názvů souborů Verze unminified je užitečná při ladění problémů, ale nezapomeňte použít verzi minifikovaného v produkčním prostředí, abyste mohli využít menší velikost souboru.

-   Po vytvoření instance třídy mapy v Azure Maps by váš kód měl čekat na to, než se mapy `ready` nebo `load` události aktivují před interakcí s mapou. Tyto události zajišťují, že se načetly všechny prostředky mapy a jsou připravené k jejímu použití.
-   Obě platformy pro základní mapy používají podobný systém dlaždic, ale dlaždice v mapách Bing jsou v dimenzi 256 pixelů, zatímco dlaždice v Azure Maps jsou v dimenzi 512 pixelů. Aby bylo možné získat stejné zobrazení mapy jako v Azure Maps jako mapy Bing, je třeba v Azure Maps použít úroveň přiblížení ve službě Mapy Bing o jednu odchylku.
-   Souřadnice v mapách Bing se označují jako `latitude, longitude` při použití Azure Maps `longitude, latitude` . Tento formát se zarovnává se standardem `[x, y]` , který následuje po většině platforem GIS.

-   Tvary v sadě Azure Maps Web SDK jsou založené na schématu geometrického kódu. Pomocné třídy jsou zpřístupněny prostřednictvím [oboru názvů Atlas. data](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data). Je to také [Atlas. Třída Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape) , která se dá použít ke zalamování objektů a jejich snadné aktualizace a udržování v datové vazbě.
-   Souřadnice v Azure Maps jsou definovány jako objekty pozice, které lze zadat jako jednoduché pole čísel ve formátu `[longitude, latitude]` nebo `new atlas.data.Position(longitude, latitude)` .

> [!TIP]
> Třída Position má statickou pomocnou funkci pro import souřadnic, které jsou ve `latitude, longitude` formátu. Funkce [Atlas. data. Position. fromLatLng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position)může často nahradit `new Microsoft.Maps.Location` funkci v kódu služby mapy Bing.

-   Namísto zadání informací o stylech na každém obrazci, který je přidán k mapě, Azure Maps odděluje styly od dat. Data jsou uložena ve zdrojích dat a jsou připojena k vykreslovacím vrstvám, které Azure Maps kód používá k vykreslování dat. Tento přístup poskytuje vyšší výhody výkonu. Kromě toho mnoho vrstev podporuje styly řízené daty, kde obchodní logika může být přidána do možností stylu vrstvy, které změní způsob, jakým se jednotlivé tvary vykreslují v rámci vrstvy na základě vlastností definovaných v prvku Shape.
-   Azure Maps poskytuje spoustu užitečných matematických funkcí v `atlas.math` oboru názvů, ale liší se od těch, které jsou v prostorovém matematickém modulu mapy Bing. Hlavní rozdíl spočívá v tom, že Azure Maps neposkytuje integrované funkce pro binární operace, jako je například sjednocení a průnik, protože Azure Maps je založena na geografickém JSON, které je otevřený standard, je k dispozici celá řada Open Source knihoven. Jedna oblíbená možnost, která dobře funguje s Azure Maps a poskytuje spoustu prostorových matematických funkcí je [Turf js](http://turfjs.org/).

Viz také [glosář Azure Maps](https://docs.microsoft.com/azure/azure-maps/glossary) pro podrobný seznam terminologie přidružených k Azure Maps.

## <a name="web-sdk-side-by-side-examples"></a>Webové sady SDK vedle sebe – příklady

Následuje kolekce ukázek kódu pro každou platformu, která se vztahuje na běžné případy použití, které vám pomůžou při migraci webové aplikace ze služby Bing Maps V8 JavaScript SDK do Azure Maps webové sady SDK. Ukázky kódu související s webovými aplikacemi jsou k dispozici v JavaScriptu. Azure Maps ale také poskytuje definice TypeScript jako další možnost prostřednictvím [modulu npm](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

**Témata**

- [Načtení mapy](#load-a-map)
- [Lokalizace mapy](#localizing-the-map)
- [Nastavení zobrazení mapy](#setting-the-map-view)
- [Přidání připínáček](#adding-a-pushpin)
- [Přidání vlastního připínáček](#adding-a-custom-pushpin)
- [Přidání lomené čáry](#adding-a-polyline)
- [Přidání mnohoúhelníku](#adding-a-polygon)
- [Zobrazit Infobox](#display-an-infobox)
- [Clustering připínáček](#pushpin-clustering)
- [Přidat Heat mapu](#add-a-heat-map)
- [Překrytí vrstvy dlaždice](#overlay-a-tile-layer)
- [Zobrazení provozních dat](#show-traffic-data)
- [Přidání překrytí základní desky](#add-a-ground-overlay)
- [Přidání dat KML do mapy](#add-kml-data-to-the-map)
- [Přidat nástroje pro kreslení](#add-drawing-tools)


### <a name="load-a-map"></a>Načtení mapy

Načtení mapy v sadě SDK se řídí stejnou sadou kroků.

-   Přidejte odkaz na mapovou sadu SDK.
-   Přidejte `div` značku k textu stránky, která bude sloužit jako zástupný symbol pro mapu.
-   Vytvořte funkci JavaScriptu, která se volá při načtení stránky.
-   Vytvořte instanci příslušné mapové třídy.

**Některé klíčové rozdíly**

-   Služby mapy Bing vyžadují, aby byl klíč účtu zadán v odkazu na skript rozhraní API nebo jako možnost mapy. Pověření ověřování pro Azure Maps jsou zadána jako možnosti třídy map. Může se jednat o klíč předplatného nebo informace Azure Active Directory.
-   Mapy Bing přebírají ve funkci zpětného volání v odkazu na skript rozhraní API, které se používá k volání funkce inicializace pro načtení mapy. V Azure Maps by se měla použít událost při načtení stránky.
-   Při použití ID k odkazování na `div` prvek, ve kterém bude mapa vykreslena, používá mapy Bing SELEKTOR HTML (tj. `#myMap` ), zatímco Azure Maps používá pouze hodnotu ID (tj. `myMap` ).
-   Souřadnice v Azure Maps jsou definovány jako objekty pozice, které lze zadat jako jednoduché pole čísel ve formátu `[longitude, latitude]` .
-   Úroveň přiblížení v Azure Maps je jedna úroveň nižší než příklad mapy Bingu, protože se jedná o rozdíl v velikostech systému dlaždic mezi platformami.
-   Ve výchozím nastavení Azure Maps nepřidá žádné navigační ovládací prvky do plátna mapy, jako jsou tlačítka lupy a tlačítka stylů mapy. Existují však ovládací prvky pro přidání výběru stylu mapy, tlačítek lupy, kompasu nebo ovládacího prvku otáčení a ovládacího prvku pro sklon.
-   Do Azure Maps se přidá obslužná rutina události, která monitoruje `ready` událost instance mapy. Spustí se, až se mapa dokončí načítání WebGL kontextu a všech potřebných prostředků. V této obslužné rutině události lze přidat libovolný kód následného načtení.

Níže uvedené příklady ukazují, jak načíst základní mapu, která je zarovnána na střed v New York v souřadnicích (zeměpisná délka:-73,985, zeměpisná šířka: 40,747) a je na úrovni přiblížení 12 v mapách Bing.

**Před: mapy Bing**

Následující kód představuje příklad zobrazení mapy Bingu a přiblížení na místo.

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
            map = new Microsoft.Maps.Map('#myMap', {
                credentials: '<Your Bing Maps Key>',
          center: new Microsoft.Maps.Location(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Spuštění tohoto kódu v prohlížeči zobrazí mapu, která vypadá jako na následujícím obrázku:

<center>

![Mapa Mapy Bing](media/migrate-bing-maps-web-app/bing-maps-load-map.jpg)</center>

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

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom and map style controls to top right of map.
                map.controls.add([
                    new atlas.control.StyleControl(),
                    new atlas.control.ZoomControl()
                ], {
                    position: 'top-right'
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

![Mapa Azure Maps](media/migrate-bing-maps-web-app/azure-maps-load-map.jpg)</center>

Podrobná dokumentace k nastavení a použití mapového ovládacího prvku Azure Maps ve webové aplikaci najdete [tady](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

> [!TIP]
> Azure Maps zveřejňuje minifikovaného i unminified verze sady SDK. Odeberte `.min` z názvů souborů. Verze unminified je užitečná při ladění problémů, ale nezapomeňte použít verzi minifikovaného v produkčním prostředí, abyste mohli využít menší velikost souboru.

**Další materiály**

-   Azure Maps také poskytuje navigační ovládací prvky pro otočení a rozteč zobrazení mapy, jak je popsáno [zde](https://docs.microsoft.com/azure/azure-maps/map-add-controls).

### <a name="localizing-the-map"></a>Lokalizace mapy

Pokud jsou vaše cílová skupina rozdělená do několika zemí nebo komunikují s různými jazyky, je lokalizace důležitá.

**Před: mapy Bing**

Chcete-li lokalizovat mapy Bing, jazyk a oblast jsou zadány `setLang` pomocí `UR` parametrů a, které jsou přidány do `<script>` označení odkazu na rozhraní API. Některé funkce v mapách Bing jsou dostupné jenom na určitých trzích, protože takový trh uživatele je zadaný pomocí `setMkt` parametru.

```html
<script type="text/javascript" src="https://www.bing.com/api/maps/mapcontrol?callback=initMap&setLang=[language_code]&setMkt=[market]&UR=[region_code]" async defer></script>
```

Tady je příklad map Bingu s jazykem nastaveným na "fr-FR".

<center>

![Lokalizované mapy mapy Bing](media/migrate-bing-maps-web-app/bing-maps-localized-map.jpg)</center>

**Po: Azure Maps**

Azure Maps poskytuje jenom možnosti pro nastavení jazyka a regionálního zobrazení mapy. Parametr trhu se nepoužívá k omezení funkcí. Existují dva různé způsoby nastavení jazyka a regionálního zobrazení mapy. První možností je přidat tyto informace do globálního `atlas` oboru názvů, který způsobí, že se všechny instance mapového ovládacího prvku ve vaší aplikaci budou ve výchozím nastavení nacházet. Následující nastavení jazyk na francouzštinu ("fr-FR") a místní zobrazení `"auto"` :

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
> Pomocí Azure Maps je možné načíst více instancí mapy na stejné stránce s jiným nastavením jazyka a oblasti. Kromě toho je také možné aktualizovat Tato nastavení v mapě po jeho načtení. Podrobný seznam podporovaných jazyků v Azure Maps najdete [tady](https://docs.microsoft.com/azure/azure-maps/supported-languages).

Tady je příklad Azure Maps s jazykem nastaveným na "fr" a oblastí uživatele nastavenou na "fr-FR".

<center>

![Lokalizovaná mapa Azure Maps](media/migrate-bing-maps-web-app/bing-maps-localized-map.jpg)</center>

### <a name="setting-the-map-view"></a>Nastavení zobrazení mapy

Dynamické mapy v Bingu a Azure Maps lze programově přesunout do nových geografických umístění voláním příslušných funkcí v JavaScriptu. V níže uvedených příkladech se dozvíte, jak se má mapa zobrazit v satelitním notebooku, vycentrovat mapu na umístění s souřadnicemi (zeměpisná délka:-111,0225, zeměpisná šířka: 35,0272) a v mapách Bingu změnit úroveň přiblížení na 15.

> [!NOTE]
> Mapy Bing používá v dimenzích dlaždice, které jsou 256 pixelů, zatímco Azure Maps používá větší dlaždici 512-pixel. Tím se sníží počet požadavků na síť, které vyžaduje Azure Maps k načtení stejné oblasti rozvržení jako mapy Bing. Vzhledem k tomu, jak pracují pyramidy dlaždice v mapových ovládacích prvcích, jsou větší dlaždice v Azure Maps znamená, že k dosažení stejné zobrazitelné oblasti jako mapy ve službě Bing Maps potřebujete při použití Azure Maps odečíst úroveň přiblížení používanou v mapách Bingu. 1.

**Před: mapy Bing**

Mapový ovládací prvek mapy Bing se dá programově přesunout pomocí `setView` funkce, která umožňuje určit střed mapy a úroveň přiblížení.

```javascript
map.setView({
    mapTypeId: Microsoft.Maps.MapTypeId.aerial,
    center: new Microsoft.Maps.Location(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![Zobrazení mapy sady map Bing](media/migrate-bing-maps-web-app/bing-maps-set-map-view.jpg)</center>

**Po: Azure Maps**

V Azure Maps lze umístění mapy změnit programově pomocí `setCamera` funkce mapy a styl mapy lze změnit pomocí `setStyle` funkce. Všimněte si, že souřadnice v Azure Maps jsou ve formátu "Zeměpisná délka, zeměpisná šířka" a hodnota úrovně přiblížení je odečtena o 1.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite_with_roads'
});
```

<center>

![Azure Maps nastavit zobrazení mapy](media/migrate-bing-maps-web-app/azure-maps-set-map-view.jpg)</center>

**Další materiály**

-   [Výběr stylu mapy](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
-   [Podporované styly map](https://docs.microsoft.com/azure/azure-maps/supported-map-styles)

### <a name="adding-a-pushpin"></a>Přidání připínáček

V Azure Maps existuje více způsobů, jak lze na mapě vykreslovat data.

-   Značky HTML – vykresluje body pomocí tradičních elementů DOM. Značky HTML podporují přetahování.
-   Vrstva symbolu – vykreslí body s ikonou nebo textem v rámci WebGL kontextu.
-   Bublinová vrstva – vykreslí body jako kružnice na mapě. Poloměry kroužků se dají škálovat na základě vlastností v datech.

Symbol i bublinové vrstvy jsou vykresleny v rámci WebGL kontextu a je možné vykreslovat velmi velké sady bodů na mapě. Tyto vrstvy vyžadují, aby data byla uložená ve zdroji dat. Zdroje dat a vrstvy vykreslování by měly být přidány do mapy po `ready` vyvolání události. Značky HTML jsou vykresleny jako elementy DOM na stránce a nepoužívají zdroj dat. Čím více elementů modelu DOM má stránku, tím pomalejší stránka bude. Pokud je vykreslování více než několik stovek bodů na mapě, doporučuje se místo toho použít jednu z vrstev vykreslování.

Níže uvedené příklady přidávají značku na mapu (zeměpisná délka:-0,2, zeměpisná šířka: 51,5) s číslem 10 překrytím jako popisek.

**Před: mapy Bing**

Pomocí map Bing se značky přidávají do mapy pomocí `Microsoft.Maps.Pushpin` třídy *. Špendlíky se pak na mapu přidají pomocí jedné ze dvou funkcí.

První funkcí je vytvořit vrstvu, vložit připínáček do této a pak přidat vrstvu do `layers` vlastnosti mapy.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    text: '10'
});

var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);
```

Druhým je přidat pomocí `entities` vlastnosti mapy. Tato funkce je označená jako zastaralá v dokumentaci pro mapy Bing V8, ale měla by částečně fungovat pro základní scénáře.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    text: '10'
});

map.entities.add(pushpin);
```

<center>

![Mapy Bingu – přidat puspin](media/migrate-bing-maps-web-app/bing-maps-add-pushpin.jpg)</center>

**Po: Azure Maps použití značek HTML**

V Azure Maps se pomocí značek HTML dá snadno zobrazit bod na mapě a doporučuje se pro jednoduché aplikace, které na mapě stačí zobrazit jenom malý počet bodů. Chcete-li použít značku HTML, vytvořte instanci `atlas.HtmlMarker` třídy, nastavte možnosti text a pozice a přidejte značku na mapu pomocí `map.markers.add` funkce.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![Azure Maps přidat značku](media/migrate-bing-maps-web-app/azure-maps-add-pushpin.jpg)</center>

**Po: Azure Maps používání vrstvy symbolů**

Při použití vrstvy symbolů musí být data přidána ke zdroji dat a zdroji dat připojenému ke vrstvě. Kromě toho by měl být zdroj dat a vrstva po `ready` vyvyvolání události přidány do mapy. Chcete-li vykreslit jedinečnou textovou hodnotu nad symbolem, je nutné, aby textové informace byly uloženy jako vlastnost datového bodu a tato vlastnost odkazovala v `textField` Možnosti vrstvy. Jedná se o trochu větší práci než při použití značek HTML, ale poskytuje mnoho výhod výkonu.

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

![Azure Maps přidat vrstvu symbolů](media/migrate-bing-maps-web-app/azure-maps-add-pushpin.jpg)</center>

**Další materiály**

-   [Vytvoření zdroje dat](https://docs.microsoft.com/azure/azure-maps/create-data-source-web-sdk)
-   [Přidat vrstvu symbolů](https://docs.microsoft.com/azure/azure-maps/map-add-pin)
-   [Přidat bublinovou vrstvu](https://docs.microsoft.com/azure/azure-maps/map-add-bubble-layer)
-   [Data bodů clusteru](https://docs.microsoft.com/azure/azure-maps/clustering-point-data-web-sdk)
-   [Přidat značky HTML](https://docs.microsoft.com/azure/azure-maps/map-add-custom-html)
-   [Použití výrazů pro styly založené na datech](https://docs.microsoft.com/azure/azure-maps/data-driven-style-expressions-web-sdk)
-   [Možnosti ikony vrstvy symbolů](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions)
-   [Možnost textu vrstvy symbolu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions)
-   [Třída značek HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker)
-   [Možnosti značky HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-custom-pushpin"></a>Přidání vlastního připínáček

Vlastní image lze použít k reprezentaci bodů na mapě. Následující obrázek se používá v níže uvedených příkladech a používá vlastní obrázek k zobrazení bodu na mapě (zeměpisná šířka: 51,5, zeměpisná délka:-0,2) a Posune pozici značky tak, aby bod připínáčku byl zarovnán se správnou polohou na mapě.

| ![Azure Maps přidat puspin](media/migrate-bing-maps-web-app/yellow-pushpin.png)|
|:-----------------------------------------------------------------------:|
| yellow-pushpin.png                                                        |


**Před: mapy Bing**

Ve službě Mapy Bing je vlastní značka vytvořena předáním adresy URL obrázku do `icon` možností připínáček. `anchor`Možnost slouží k zarovnání bodu obrázku připínáčku s souřadnicí na mapě. Hodnota kotvy ve službě Bing Maps vzhledem k levému hornímu rohu obrázku.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    icon: 'ylw-pushpin.png',
    anchor: new Microsoft.Maps.Point(5, 30)
});

var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);
```

<center>

![Mapy Bingu – přidat vlastní puspin](media/migrate-bing-maps-web-app/bing-maps-add-custom-pushpin.jpg)</center>

**Po: Azure Maps použití značek HTML**

Chcete-li přizpůsobit značku HTML v Azure Maps HTML `string` nebo `HTMLElement` lze předat do `htmlContent` možnosti značky. V Azure Maps `anchor` možnost slouží k určení relativní pozice značky vzhledem k souřadnici pozice pomocí jednoho z devíti definovaných referenčních bodů. "Center", "Top", "Bottom", "Left", "Right", "Top-left", "top-right", "Bottom-left", "Bottom-Right". Obsah je ukotven a ve výchozím nastavení nastaven na "Bottom", což je spodní střed obsahu HTML. Aby bylo snazší migrovat kód z map Bing, nastavte kotvu na "Top-left" a pak použijte `offset` možnost se stejným posunem použitým v mapách Bing. Posuny v Azure Maps přesunou v opačném směru map Bingu, takže je vynásobte odečtením jedné.

> [!TIP]
> Přidejte `pointer-events:none` jako styl obsahu HTML, abyste zakázali výchozí chování při přetahování v MS Edge, které zobrazí nežádoucí ikonu.

```html
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Azure Maps přidat vlastní značku](media/migrate-bing-maps-web-app/azure-maps-add-custom-marker.jpg)</center>

**Po: Azure Maps používání vrstvy symbolů**

Vrstvy symbolů v Azure Maps podporují i vlastní image, ale image se nejdřív musí načíst do prostředků mapy a přiřadit jim jedinečné ID. Vrstva symbolů pak může odkazovat na toto ID. Symbol může být posunutý tak, aby se zarovnal do správného bodu na obrázku pomocí `offset` Možnosti ikony. V Azure Maps `anchor` slouží možnost zadat relativní pozici symbolu relativně k souřadnici pozice pomocí jednoho z devíti definovaných referenčních bodů. "Center", "Top", "Bottom", "Left", "Right", "Top-left", "top-right", "Bottom-left", "Bottom-Right". Obsah je ukotven a ve výchozím nastavení nastaven na "Bottom", což je spodní střed obsahu HTML. Aby bylo snazší migrovat kód z map Bing, nastavte kotvu na "Top-left" a pak použijte `offset` možnost se stejným posunem použitým v mapách Bing. Posuny v Azure Maps přesunou v opačném směru map Bingu, takže je vynásobte odečtením jedné.

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
                map.imageSprite.add('my-yellow-pin', 'ylw-pushpin.png').then(function () {

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

![Mapy Bingu přidat vlastní vrstvu symbolů](media/migrate-bing-maps-web-app/azure-maps-add-custom-symbol-layer.jpg)</center>

> [!TIP]
> Chcete-li vytvořit rozšířené vlastní vykreslování bodů, použijte více vrstev vykreslování dohromady. Například pokud chcete mít více špendlíků, které mají stejnou ikonu na různých barevných kruhech, místo vytvoření řady obrázků pro každou barvu překrytí vrstvy symbolů nad bublinovou vrstvu a jejich odkazování na stejný zdroj dat. To bude mnohem efektivnější než vytváření a mapa bude udržovat spoustu různých imagí.

**Další materiály**

-   [Vytvoření zdroje dat](https://docs.microsoft.com/azure/azure-maps/create-data-source-web-sdk)
-   [Přidat vrstvu symbolů](https://docs.microsoft.com/azure/azure-maps/map-add-pin)
-   [Přidat značky HTML](https://docs.microsoft.com/azure/azure-maps/map-add-custom-html)
-   [Použití výrazů pro styly založené na datech](https://docs.microsoft.com/azure/azure-maps/data-driven-style-expressions-web-sdk)
-   [Možnosti ikony vrstvy symbolů](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions)
-   [Možnost textu vrstvy symbolu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions)
-   [Třída značek HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker)
-   [Možnosti značky HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-polyline"></a>Přidání lomené čáry

Lomené čáry slouží k reprezentaci čáry nebo cesty na mapě. Níže uvedené příklady ukazují, jak vytvořit přerušovanou lomenou čáru na mapě.

**Před: mapy Bing**

Ve službě Mapy Bing přebírá třída lomené čáry v poli umístění a sadě možností.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create the polyline.
var polyline = new Microsoft.Maps.Polyline([
        center,
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude - 1),
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude + 1)
    ], {
        strokeColor: 'red',
        strokeThickness: 4,
        strokeDashArray: [3, 3]
    });

//Add the polyline to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(polyline);
map.layers.insert(layer);
```

<center>

![Lomená čáry mapy Bing](media/migrate-bing-maps-web-app/bing-maps-line.jpg)</center>

**Po: Azure Maps**

V Azure Maps jsou lomené čáry odkazovány na nejčastěji geoprostorové výrazy `LineString` nebo `MultiLineString` objekty. Tyto objekty lze přidat do zdroje dat a vykresleny pomocí spojnicové vrstvy. Volby Barva tahu, Šířka a přerušované pole jsou téměř identické mezi platformami.

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

![Azure Maps řádek](media/migrate-bing-maps-web-app/azure-maps-line.jpg)</center>

**Další materiály**

-   [Přidat řádky do mapy](https://docs.microsoft.com/azure/azure-maps/map-add-shape#add-lines-to-the-map)
-   [Možnosti vrstvy čáry](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions)
-   [Použití výrazů pro styly založené na datech](https://docs.microsoft.com/azure/azure-maps/data-driven-style-expressions-web-sdk)

### <a name="adding-a-polygon"></a>Přidání mnohoúhelníku

Mnohoúhelníky slouží k reprezentaci oblasti na mapě. Azure Maps a mapy Bing poskytují velmi podobnou podporu pro mnohoúhelníky. Níže uvedené příklady ukazují, jak vytvořit mnohoúhelník, který tvoří trojúhelník založený na souřadnici středu mapy.

**Před: mapy Bing**

Ve službě Mapy Bing převezme třída mnohoúhelník v poli souřadnic nebo souřadnicových kroužků a sady možností.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create the polygon.
var polygon = new Microsoft.Maps.Polygon([
        center,
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude - 1),
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude + 1),
        center
    ], {
        fillColor: 'rgba(0, 255, 0, 0.5)',
        strokeColor: 'red',
        strokeThickness: 2
    });

//Add the polygon to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(polygon);
map.layers.insert(layer);
```

<center>

![Polyogn mapy Bing](media/migrate-bing-maps-web-app/azure-maps-polygon.jpg)</center>

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

![Azure Maps polyogn](media/migrate-bing-maps-web-app/azure-maps-polygon.jpg)</center>

**Další materiály**

-   [Přidat mnohoúhelník k mapě](https://docs.microsoft.com/azure/azure-maps/map-add-shape#add-a-polygon-to-the-map)
-   [Přidání kruhu k mapě](https://docs.microsoft.com/azure/azure-maps/map-add-shape#add-a-circle-to-the-map)
-   [Možnosti vrstvy mnohoúhelníku](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions)
-   [Možnosti vrstvy čáry](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions)
-   [Použití výrazů pro styly založené na datech](https://docs.microsoft.com/azure/azure-maps/data-driven-style-expressions-web-sdk)

### <a name="display-an-infobox"></a>Zobrazit Infobox

Další informace o entitě lze zobrazit v mapě jako `Microsoft.Maps.Infobox` třídu v mapách Bing, v Azure Maps to lze dosáhnout použitím `atlas.Popup` třídy. Níže uvedené příklady přidají k mapě připínáček/marker a při kliknutí se zobrazí Infobox nebo místní nabídka.

**Před: mapy Bing**

Pomocí map Bing se vytvoří Infobox pomocí `Microsoft.Maps.Infobox` konstruktoru.

```javascript
//Add a pushpin where we want to display an infobox.
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(47.6, -122.33));

//Add the pushpin to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);

//Create an infobox and bind it to the map.
var infobox = new Microsoft.Maps.Infobox(new Microsoft.Maps.Location(47.6, -122.33), {
    description: '<div style="padding:5px"><b>Hello World!</b></div>',
    visible: false
});
infobox.setMap(map);

//Add a click event to the pushpin to open the infobox.
Microsoft.Maps.Events.addHandler(pushpin, 'click', function () {
    infobox.setOptions({ visible: true });
});
```

<center>

![Infobox mapy Bing](media/migrate-bing-maps-web-app/bing-maps-infobox.jpg)</center>

**Po: Azure Maps**

V Azure Maps lze pomocí místní nabídky Zobrazit další informace o umístění. HTML `string` nebo `HTMLElement` objekt lze předat do `content` Možnosti automaticky otevírané okno. Automaticky otevíraná okna se dají v případě potřeby zobrazit nezávisle na jakémkoli tvaru, takže se musí `position` zadat hodnota. Chcete-li zobrazit místní nabídku, zavolejte `open` funkci a předejte ji na mapu, na které se má automaticky otevíraná okna zobrazovat.

```javascript
//Add a marker to the map that to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:10px"><b>Hello World!</b></div>',
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

![Místní nabídka Azure Maps](media/migrate-bing-maps-web-app/azure-maps-popup.jpg)</center>

> [!NOTE]
> Chcete-li provést stejnou akci se symbolem, bublinou, čárou nebo mnohoúhelníkovou vrstvou, předejte vrstvu do kódu události Maps místo do značky.

**Další materiály**

-   [Přidání místního okna](https://docs.microsoft.com/azure/azure-maps/map-add-popup)
-   [Automaticky otevírané okno s mediálním obsahem](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
-   [Automaticky otevíraná okna v obrazcích](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
-   [Opakované použití automaticky otevíraného okna s více PIN kódy](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
-   [Automaticky otevíraná třída](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup)
-   [Možnosti místního okna](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions)

### <a name="pushpin-clustering"></a>Clustering připínáček

Když vizualizujete řadu datových bodů na mapě, navzájem se navzájem překrývají mapa, zdá se, že mapa vypadá a že je obtížné ji zobrazit a použít. Clusterová data lze využít ke zlepšení tohoto uživatelského prostředí a také ke zvýšení výkonu. Data bodu clusteringu je proces kombinování bodových dat blízko sebe a jejich reprezentace na mapě jako jeden clusterovaný datový bod. Když se uživatel přiblíží k mapě, rozdělí se clustery do jednotlivých datových bodů.

Níže uvedené příklady načtou informační kanál pro zemětřesení dat z minulého týdne a přidá ho do mapy. Clustery se vykreslují jako škálované a barevné kružnice v závislosti na počtu bodů, které obsahují.

> [!NOTE]
> Pro clustering připínáček se používá několik různých algoritmů. Mapy Bing používají jednoduchou funkci založenou na Gridech, zatímco Azure Maps používá pokročilejší a vizuálně přitažlivější metody clusteringu na základě bodů.

**Před: mapy Bing**

Ve službě Mapy Bing je možné data typu injson načíst pomocí modulu pro použití v angličtině. Špendlíky lze seskupit načtením v modulu clusteringu a pomocí vrstvy clusteringu, kterou obsahuje.

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
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(20, -160),
                zoom: 2
            });

            //Load the GeoJSON and Clustering modules.
            Microsoft.Maps.loadModule(['Microsoft.Maps.GeoJson', 'Microsoft.Maps.Clustering'], function () {

                //Load the GeoJSON data from a URL.
                Microsoft.Maps.GeoJson.readFromUrl(earthquakeFeed, function (pins) {

                    //Create a ClusterLayer with options and add it to the map.
                    clusterLayer = new Microsoft.Maps.ClusterLayer(pins, {
                        clusteredPinCallback: createCustomClusteredPin,
                        gridSize: 100
                    });

                    map.layers.insert(clusterLayer);
                });
            });
        }

        //A function that defines how clustered pins are rendered.
        function createCustomClusteredPin(cluster) {
            //Get the number of pushpins in the cluster
            var clusterSize = cluster.containedPushpins.length;

            var radius = 20;    //Default radius to 20 pixels.
            var fillColor = 'lime';     //Default to lime green.

            if (clusterSize >= 750) {
                radius = 40;   //If point_count >= 750, radius is 40 pixels.
                fillColor = 'red';    //If the point_count >= 750, color is red.
            } else if (clusterSize >= 100) {
                radius = 30;    //If point_count >= 100, radius is 30 pixels.
                fillColor = 'yellow';    //If the point_count >= 100, color is yellow.
            }

            //Create an SVG string of a circle with the specified radius and color.
            var svg = ['<svg xmlns="http://www.w3.org/2000/svg" width="', (radius * 2), '" height="', (radius * 2), '">',
                '<circle cx="', radius, '" cy="', radius, '" r="', radius, '" fill="', fillColor, '"/>',
                '<text x="50%" y="50%" dominant-baseline="middle" text-anchor="middle" style="font-size:12px;font-family:arial;fill:black;" >{text}</text>',
                '</svg>'];

            //Customize the clustered pushpin using the generated SVG and anchor on its center.
            cluster.setOptions({
                icon: svg.join(''),
                anchor: new Microsoft.Maps.Point(radius, radius),
                textOffset: new Microsoft.Maps.Point(0, radius - 8) //Subtract 8 to compensate for height of text.
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Clusterování map Bing](media/migrate-bing-maps-web-app/bing-maps-clustering.jpg)</center>

**Po: Azure Maps**

V Azure Maps se data přidávají a spravují zdrojem dat. Vrstvy se připojují ke zdrojům dat a vykreslují data v nich. `DataSource`Třída v Azure Maps poskytuje několik možností clusteringu.

-   `cluster` – Instruuje zdroj dat na data bodu clusteru. 
-   `clusterRadius` -Mezi protokolem RADIUS a propojenými body clusteru.
-   `clusterMaxZoom` – Maximální úroveň přiblížení, ke které dochází clustering. Pokud přiblížíte více než to, všechny body se vykreslí jako symboly.
-   `clusterProperties` – Definuje vlastní vlastnosti, které se vypočítávají pomocí výrazů pro všechny body v jednotlivých clusterech a přidají se do vlastností každého bodu clusteru.

Když je clustering povolený, bude zdroj dat odesílat clusterované a neseskupené datové body do vrstev pro vykreslování. Zdroj dat je schopný clusterovat stovky tisíc datových bodů. Clusterovaný datový bod má následující vlastnosti:

| Název vlastnosti               | Typ    | Description                                    |
|-----------------------------|---------|------------------------------------------------|
| `cluster`                   | boolean | Indikuje, že funkce představuje cluster.     |
| `cluster_id`                | řetězec  | Jedinečné ID clusteru, které lze použít se `DataSource` třídami `getClusterExpansionZoom` , `getClusterChildren` a `getClusterLeaves` . |
| `point_count`               | číslo  | Počet bodů, které cluster obsahuje.     |
| `point_count_abbreviated`   | řetězec  | Řetězec, který zkracuje hodnotu, `point_count` Pokud je Long. (například 4 000 se bude 4K) |

`DataSource`Třída má následující pomocnou funkci pro přístup k dalším informacím o clusteru pomocí `cluster_id` .

| Funkce       | Návratový typ        | Description     |
|----------------|--------------------|-----------------|
| `getClusterChildren(clusterId: number)`                              | `Promise<Feature<Geometry, any> | Shape>` | Načte podřízené objekty daného clusteru na další úrovni přiblížení. Tyto podřízené objekty můžou být kombinací tvarů a podclusterů. Dílčí clustery budou funkcemi s vlastnostmi, které odpovídají vlastnostem clusteru. |
| `getClusterExpansionZoom(clusterId: number)`                         | `Promise<number>`                            | Vypočítá úroveň přiblížení, kterou cluster začne rozšiřovat nebo se může rozdělovat.    |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | `Promise<Feature<Geometry, any> | Shape>` | Načte všechny body v clusteru. Nastavte, `limit` aby se vracela podmnožina bodů, a použijte `offset` stránku k v bodech.    |

Při vykreslování clusterovaných dat na mapě je často nejjednodušší použít dvě nebo více vrstev. Následující příklad používá tři vrstvy, bublinovou vrstvu pro kreslení barevných paprsků škálované na základě velikosti clusterů, vrstvy symbolů pro vykreslení velikosti clusteru jako textu a druhé vrstvy symbolů pro vykreslování neclusterovaných bodů. Existuje mnoho dalších způsobů, jak vykreslovat data v clusterech v Azure Maps zvýrazněná v dokumentaci k [datům bodu clusteru](https://docs.microsoft.com/azure/azure-maps/clustering-point-data-web-sdk) .

Data o úrovni injson je možné přímo importovat v Azure Maps pomocí `importDataFromUrl` funkce `DataSource` třídy.

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

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
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
                        filter: ['has', 'point_count'] //Only rendered data points that have a point_count property, which clusters do.
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

![Azure Maps clusteringu](media/migrate-bing-maps-web-app/azure-maps-clustering.jpg)</center>

**Další materiály**

-   [Přidat vrstvu symbolů](https://docs.microsoft.com/azure/azure-maps/map-add-pin)
-   [Přidat bublinovou vrstvu](https://docs.microsoft.com/azure/azure-maps/map-add-bubble-layer)
-   [Data bodů clusteru](https://docs.microsoft.com/azure/azure-maps/clustering-point-data-web-sdk)
-   [Použití výrazů pro styly založené na datech](https://docs.microsoft.com/azure/azure-maps/data-driven-style-expressions-web-sdk)

### <a name="add-a-heat-map"></a>Přidat Heat mapu

Heat mapy, označované také jako mapy hustoty bodů, jsou typem vizualizace dat, která představuje hustotu dat pomocí rozsahu barev. Často se používají k zobrazení dat "aktivních bodů" na mapě a představují skvělý způsob, jak vykreslovat sady velkých bodů dat.

Níže uvedené příklady načtou v sadě USGS informační kanál pro zemětřesení za minulý měsíc a vykreslí je jako Heat mapu.

**Před: mapy Bing**

Pokud chcete vytvořit Heat mapu v mapách Bingu, načtěte v modulu heat map. Podobně je načten modul pro injson, který umožňuje přidat podporu pro data o zápisu.

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
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(20, -160),
                zoom: 2,
                mapTypeId: Microsoft.Maps.MapTypeId.aerial
            });

            //Load the GeoJSON and HeatMap modules.
            Microsoft.Maps.loadModule(['Microsoft.Maps.GeoJson', 'Microsoft.Maps.HeatMap'], function () {

                //Load the GeoJSON data from a URL.
                Microsoft.Maps.GeoJson.readFromUrl(earthquakeFeed, function (shapes) {

                    //Create a heat map and add it to the map.
                    var heatMap = new Microsoft.Maps.HeatMapLayer(shapes, {
                        opacity: 0.65,
                        radius: 10
                    });
                    map.layers.insert(heatMap);
                });
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Heatmapu mapy Bing](media/migrate-bing-maps-web-app/bing-maps-heatmap.jpg)</center>

**Po: Azure Maps**

V Azure Maps načtěte data o úrovni injson do zdroje dat a propojte zdroj dat s vrstvou Heat mapy. Data o úrovni injson je možné přímo importovat v Azure Maps pomocí `importDataFromUrl` funkce `DataSource` třídy.

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
                style: 'satellite_with_roads',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
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

![Azure Maps heatmapu](media/migrate-bing-maps-web-app/azure-maps-heatmap.jpg)</center>

**Další materiály**

-   [Přidání vrstvy heat mapy](https://docs.microsoft.com/azure/azure-maps/map-add-heat-map-layer)
-   [Třída vrstvy tepelné mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.heatmaplayer)
-   [Možnosti vrstvy tepelné mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)
-   [Použití výrazů pro styly založené na datech](https://docs.microsoft.com/azure/azure-maps/data-driven-style-expressions-web-sdk)

### <a name="overlay-a-tile-layer"></a>Překrytí vrstvy dlaždice

Vrstvy dlaždic vám umožňují překrýt velké obrázky, které byly rozděleny do menších dlaždic, které jsou v souladu se systémem mapy dláždění. To je běžný způsob, jak překrýt velké obrázky nebo velmi velké datové sady.

Níže uvedené příklady překrývají vrstvu s paprskovou dlaždicí počasí z Iowa prostředí Mesonet na úrovni Iowa, která používá schéma názvů X, Y a lupy.

**Před: mapy Bing**

V mapách Bing lze vrstvy dlaždic vytvořit pomocí `Microsoft.Maps.TileLayer` třídy.

```javascript
var weatherTileLayer = new Microsoft.Maps.TileLayer({
    mercator: new Microsoft.Maps.TileSource({
        uriConstructor: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{zoom}/{x}/{y}.png'
    })
});
map.layers.insert(weatherTileLayer);
```

<center>

![Mapy Bing vážené heatmapu](media/migrate-bing-maps-web-app/bing-maps-weighted-heatmap.jpg)</center>

**Po: Azure Maps**

V Azure Maps lze do mapy přidat vrstvu dlaždice podobným způsobem jako jakákoli jiná vrstva. Naformátovaná adresa URL, která má ve tvaru x, y, zástupné symboly přiblížení; `{x}`, `{y}` , v `{z}` uvedeném pořadí, slouží k oznámení vrstvy, kde má být přístup k dlaždicím. Vrstvy dlaždic Azure Maps také podporují `{quadkey}` `{bbox-epsg-3857}` a `{subdomain}` zástupné symboly.

> [!TIP]
> V Azure Maps vrstev lze snadno vykreslovat pod jinými vrstvami, včetně základních vrstev mapy. Často je žádoucí vykreslovat vrstvy dlaždice pod popisky map, aby byly snadno čitelné. `map.layers.add`Funkce přebírá druhý parametr, který je ID druhé vrstvy pro vložení nové vrstvy níže. Chcete-li vložit vrstvu dlaždice pod popisky map, lze použít následující kód:
> 
> `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

![Azure Maps vážená heatmapu](media/migrate-bing-maps-web-app/azure-maps-weighted-heatmap.jpg)</center>

> [!TIP]
> Žádosti o dlaždici se dají zachytit pomocí `transformRequest` Možnosti mapy. V případě potřeby vám umožní upravit nebo přidat hlavičky do žádosti.

**Další materiály**

-   [Přidání vrstev dlaždic](https://docs.microsoft.com/azure/azure-maps/map-add-tile-layer)
-   [Třída dlaždic vrstev](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer)
-   [Možnosti vrstvy dlaždic](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions)

### <a name="show-traffic-data"></a>Zobrazení provozních dat

Data přenosů se dají překrývají v Bingu i službě Azure Maps.

**Před: mapy Bing**

V mapách Bing je možné data přenosů překrytá pomocí modulu provoz.

```javascript
Microsoft.Maps.loadModule('Microsoft.Maps.Traffic', function () {
    var manager = new Microsoft.Maps.Traffic.TrafficManager(map);
    manager.show();
});
```

<center>

![Přenosy map Bingu](media/migrate-bing-maps-web-app/bing-maps-traffic.jpg)</center>

**Po: Azure Maps**

Azure Maps poskytuje několik různých možností pro zobrazení provozu. Incidenty přenosu, jako jsou například uzávěry a havárie provozu, lze zobrazit jako ikony na mapě. Tok přenosů, barevně rozlišené silnice lze na mapě překrytí a barvy je možné upravit tak, aby se využívaly relativně k zadanému limitu rychlosti, relativně k normálnímu očekávanému zpoždění nebo absolutnímu zpoždění. Data incidentu v Azure Maps se aktualizují každou minutu a data toku každé 2 minuty.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

![Azure Maps provoz](media/migrate-bing-maps-web-app/azure-maps-traffic.jpg)</center>

Pokud kliknete na jednu z ikon přenosů v Azure Maps, zobrazí se další informace v automaticky otevřeném okně.

<center>

![Místní nabídka provozu Azure Maps](media/migrate-bing-maps-web-app/azure-maps-traffic-popup.jpg)</center>

**Další materiály**

-   [Zobrazit provoz na mapě](https://docs.microsoft.com/azure/azure-maps/map-show-traffic)
-   [Možnosti překrytí provozu](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)
-   [Řízení provozu](https://azuremapscodesamples.azurewebsites.net/?sample=Traffic%20controls)

### <a name="add-a-ground-overlay"></a>Přidání překrytí základní desky

Bing i Azure Maps podporují překrytí obrazových obrázků na mapě tak, aby se při posouvání a škálování na mapě přesunuly a přiblížily. V mapách Bing se označují jako základní překrytí v Azure Maps jsou označovány jako vrstvy obrázků. Ty jsou skvělé pro vytváření plánů podlaží, překrývání starých map nebo k použití snímků z pomocí dronů.

**Před: mapy Bing**

Při vytváření překrytí základní desky v mapách Bing musíte zadat adresu URL obrázku, který se má překrýt, a ohraničující rámeček, na který se má vazba navázat na mapě. Tento příklad překrývá obrázek mapy [newyorském New Jersey z 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) na mapě.

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
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.740, -74.18),
                zoom: 12
            });

            var overlay = new Microsoft.Maps.GroundOverlay({
                //Create a LocationRect from the edges of the bounding box; north, west, south, east.
                bounds: Microsoft.Maps.LocationRect.fromEdges(40.773941, -74.22655, 40.712216, -74.12544),
                imageUrl: 'newark_nj_1922.jpg'
            });
            map.layers.insert(overlay);
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Spuštění tohoto kódu v prohlížeči zobrazí mapu, která vypadá jako na následujícím obrázku:

<center>

![Základní překrytí mapy Bing](media/migrate-bing-maps-web-app/bing-maps-ground-overlay.jpg)</center>

**Po: Azure Maps**

V Azure Maps mohou být neodkazované obrázky překryty pomocí `atlas.layer.ImageLayer` třídy. Tato třída vyžaduje adresu URL obrázku a sadu souřadnic pro čtyři rohy obrázku. Bitová kopie musí být hostována buď ve stejné doméně, nebo musí mít povolenou CORs.

> [!TIP]
> Pokud máte jenom informace o Severní, Jižní, východní, západní a rotační oblasti a ne souřadnice pro každý roh obrázku, můžete použít funkci static [Atlas. Layer. ImageLayer. getCoordinatesFromEdges](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer#getcoordinatesfromedges-number--number--number--number--number-) .

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

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
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

![Azure Maps překryvná deska](media/migrate-bing-maps-web-app/azure-maps-ground-overlay.jpg)</center>

**Další materiály**

-   [Překryv obrázku](https://docs.microsoft.com/azure/azure-maps/map-add-image-layer)
-   [Třída image Layer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer)

### <a name="add-kml-data-to-the-map"></a>Přidání dat KML do mapy

Azure i mapy Bing můžou na mapě importovat a vykreslovat data KML, KMZ, GeoRSS, injson a Well-Known text (Well). Azure Maps podporuje také GPX, GML, prostorové soubory CSV, služby Web-Mapping Services (WMS), Web-Mapping (WMTS) a služby webových funkcí (WFS).

**Před: mapy Bing**

Spuštění tohoto kódu v prohlížeči zobrazí mapu, která vypadá jako na následujícím obrázku:

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
            map = new Microsoft.Maps.Map('#myMap', {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.747, -73.985),
                zoom: 12
            });

            Microsoft.Maps.loadModule('Microsoft.Maps.GeoXml', function () {
                var callback = function (dataset) {
                    if (dataset.shapes) {
                        var l = new Microsoft.Maps.Layer();
                        l.add(dataset.shapes);
                        map.layers.insert(l);
                    }
                    if (dataset.layers) {
                        for (var i = 0, len = dataset.layers.length; i < len; i++) {
                            map.layers.insert(dataset.layers[i]);
                        }
                    }
                };
                Microsoft.Maps.GeoXml.readFromUrl('myKMLFile.kml', { error: function (msg) { alert(msg); } }, callback);
            });                
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![KML mapy Bing](media/migrate-bing-maps-web-app/bing-maps-kml.jpg)</center>

**Po: Azure Maps**

V Azure Maps je pro informating data ve webové sadě v angličtině hlavní formát dat, další formáty prostorových dat je možné snadno integrovat pomocí [modulu pro prostorové vstupně-výstupní operace](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/). Tento modul obsahuje funkce pro čtení i zápis prostorových dat a také obsahuje jednoduchou datovou vrstvu, která umožňuje snadno vykreslovat data z libovolného z těchto formátů prostorových dat. Chcete-li číst data v souboru prostorových dat, předejte adresu URL nebo nezpracovaná data jako řetězec nebo objekt blob do  `atlas.io.read`   funkce. Tato akce vrátí všechna Analyzovaná data ze souboru, který lze přidat do mapy. KML je trochu složitější než většina formátů prostorových dat, protože obsahuje mnoho dalších informací o stylu.  `SpatialDataLayer`   Třída podporuje vykreslování většiny těchto stylů, ale před načtením dat funkce musí být obrázky napřed načteny do mapy a základní překrytí musí být přidány jako vrstvy do mapy samostatně. Při načítání dat prostřednictvím adresy URL by se měla hostovat na koncovém bodu s povoleným CORs nebo by měla být proxy služba předána jako možnost funkce Read.

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

    <!-- Add reference to the Azure Maps Spatial IO module. -->
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

    <script type='text/javascript'>
        var map, datasource, layer;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
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
                atlas.io.read('myKMLFile.kml').then(async r => {
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

<center>

![Azure Maps KML](media/migrate-bing-maps-web-app/azure-maps-kml.jpg)</center>

**Další materiály**

-   [Atlas. IO. Read – funkce](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io#read-string---arraybuffer---blob--spatialdatareadoptions-)
-   [SimpleDataLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)
-   [SimpleDataLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

### <a name="add-drawing-tools"></a>Přidat nástroje pro kreslení

Bing i Azure Maps poskytují modul, který uživateli umožňuje vykreslit a upravovat obrazce na mapě pomocí myši nebo jiného vstupního zařízení. Podporují vykreslování špendlíků, čar a mnohoúhelníků. Azure Maps také poskytuje možnosti pro kreslení kruhů a obdélníků.

**Před: mapy Bing**

V mapách Bing se `DrawingTools` modul načítá pomocí `Microsoft.Maps.loadModule` funkce. Po načtení lze instanci třídy DrawingTools vytvořit a `showDrawingManager` funkce se nazývá Přidání panelu nástrojů na mapu.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
    var map, drawingManager;

    function initMap() {
        map = new Microsoft.Maps.Map('#myMap', {
            credentials: '<Your Bing Maps Key>'
        });

        //Load the DrawingTools module
        Microsoft.Maps.loadModule('Microsoft.Maps.DrawingTools', function () {
            //Create an instance of the DrawingTools class and bind it to the map.
            var tools = new Microsoft.Maps.DrawingTools(map);

            //Show the drawing toolbar and enable editting on the map.
            tools.showDrawingManager(function (manager) {
                //Store a reference to the drawing manager as it will be useful later.
                drawingManager = manager;
            });
        });
    }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>

```

<center>

![Nástroje pro kreslení map Bing](media/migrate-bing-maps-web-app/bing-maps-drawing-tools.jpg)</center>

**Po: Azure Maps**

V Azure Maps modul nástrojů pro kreslení musí být načten načtením souborů JavaScriptu a CSS v aplikaci musí být odkazováno. Jakmile je mapa načtena, `DrawingManager` může být vytvořena instance třídy a `DrawingToolbar` připojená instance.

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

    <!-- Add references to the Azure Maps Map Drawing Tools JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
    
    <script type='text/javascript'>
        var map, drawingManager;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',
                
                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an instance of the drawing manager and display the drawing toolbar.
                drawingManager = new atlas.drawing.DrawingManager(map, {
                    toolbar: new atlas.control.DrawingToolbar({ position: 'top-left' })
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

<center>

![Nástroje pro kreslení Azure Maps](media/migrate-bing-maps-web-app/azure-maps-drawing-tools.jpg)</center>

> [!TIP]
> V Azure Maps vrstvy nástrojů pro kreslení poskytují více způsobů, jak mohou uživatelé kreslit obrazce. Například při kreslení mnohoúhelníku může uživatel kliknout na přidat jednotlivé body, nebo podržet levé tlačítko myši a přetažením myši nakreslit cestu. To lze upravit pomocí `interactionType` možnosti `DrawingManager` .

**Další materiály**

-   [Dokumentace](https://docs.microsoft.com/azure/azure-maps/set-drawing-options)
-   [Ukázky kódu](https://azuremapscodesamples.azurewebsites.net/#Drawing-Tools-Module)

## <a name="next-steps"></a>Další kroky

Podívejte se na [Open Source moduly Azure Maps Web SDK](open-source-projects.md#open-web-sdk-modules). Tyto moduly poskytují spoustu dalších funkcí a jsou plně přizpůsobitelné.

Přečtěte si ukázky kódu související s migrací dalších funkcí map Bing:

**Vizualizace dat**

> [!div class="nextstepaction"]
> [Vrstva obrysu](https://azuremapscodesamples.azurewebsites.net/?search=contour)

> [!div class="nextstepaction"]
> [Binningu dat](https://azuremapscodesamples.azurewebsites.net/?search=data%20binning)

**Služby**

> [!div class="nextstepaction"]
> [Použití modulu služby Azure Maps Services](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module)

> [!div class="nextstepaction"]
> [Hledání bodů zájmu](https://docs.microsoft.com/azure/azure-maps/map-search-location)

> [!div class="nextstepaction"]
> [Získat informace ze souřadnice (zpětného zobrazení kódu)](https://docs.microsoft.com/azure/azure-maps/map-get-information-from-coordinate)

> [!div class="nextstepaction"]
> [Zobrazení pokynů pro trasu z A do B](https://docs.microsoft.com/azure/azure-maps/map-route)

> [!div class="nextstepaction"]
> [Vyhledat automatické návrhy pomocí uživatelského rozhraní JQuery](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

Přečtěte si další informace o Azure Maps Web SDK.

> [!div class="nextstepaction"]
> [Jak používat mapový ovládací prvek](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Jak používat modul služeb](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Jak používat modul nástrojů pro kreslení](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Ukázky kódu](https://docs.microsoft.com/samples/browse/?products=azure-maps)

> [!div class="nextstepaction"]
> [Referenční dokumentace k rozhraní API služby Azure Maps Web SDK](https://docs.microsoft.com/javascript/api/azure-maps-control/)

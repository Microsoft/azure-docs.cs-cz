---
title: 'Výuka: Migrace webové aplikace z Map Google | Mapy Microsoft Azure'
description: Jak migrovat webovou aplikaci z Map Google do Map Microsoft Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: bdbf2a975cbdc3d06745b9375c1e6f8e751ddfd6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77914072"
---
# <a name="migrate-a-web-app-from-google-maps"></a>Migrace webové aplikace z Map Google

Většina webových aplikací, které používají Mapy Google, používá soupravu Google Maps V3 JavaScript SDK. Webová sada Azure Maps Web SDK je vhodná sada SDK založená na Azure, do které se migruje. Sada Azure Maps Web SDK umožňuje přizpůsobit interaktivní mapy pomocí vlastního obsahu a obrázků. Aplikaci můžete spouštět ve webových i mobilních aplikacích. Tento ovládací prvek využívá WebGL a umožňuje vykreslovat rozsáhlé datové sady s vysokým výkonem. Vývoj s touto sadou SDK pomocí JavaScriptu nebo TypeScriptu.

Pokud migrujete existující webovou aplikaci, zkontrolujte, zda používá knihovnu ovládacích prvku mapy s otevřeným zdrojovým kódem. Příklady open source knihovny map jsou: Cesium, Leták a OpenLayers. Stále můžete migrovat aplikaci, i když používá knihovnu ovládacích prvku mapy s otevřeným zdrojovým kódem a nechcete používat webovou sadu Azure Maps. V takovém případě připojte aplikaci ke službám dlaždic Azure Maps ( [satelitní](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)[dlaždice](https://docs.microsoft.com/rest/api/maps/render/getmaptile) \| silnic ). Následující body podrobně o tom, jak používat Azure Maps v některých běžně používaných open source map ových ovládacích knihoven.

- Cesium - 3D ovládání mapy pro web. [Dokumentace ukázky](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [Documentation](https://cesiumjs.org/) kódu
- Leták – Lehké ovládání 2D mapy pro web. [Dokumentace ukázky](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [Documentation](https://leafletjs.com/) kódu
- OpenLayers - Ovládací prvek 2D mapy pro web, který podporuje projekce. [Dokumentace ukázky](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [Documentation](https://openlayers.org/) kódu

## <a name="key-features-support"></a>Podpora klíčových funkcí

V tabulce jsou uvedeny klíčové funkce rozhraní API v sada JavaScript SDK v Mapách Google V3 a podporovaná funkce rozhraní API v sada Azure Maps Web SDK.

| Funkce Mapy Google     | Podpora pro Azure Maps Web SDK |
|-------------------------|:--------------------------:|
| Značky                 | ✓                          |
| Clustering značek       | ✓                          |
| Vícelinové & polygony    | ✓                          |
| Datové vrstvy             | ✓                          |
| Pozemní překryvy         | ✓                          |
| Tepelné mapy               | ✓                          |
| Vrstvy dlaždic             | ✓                          |
| Vrstva KML               | ✓                          |
| Nástroje pro kreslení           | ✓                          |
| Geocoder servis        | ✓                          |
| Služba Pokyny      | ✓                          |
| Služba Distance Matrix | ✓                          |
| Služba zvýšení oprávnění       | Plánováno                    |

## <a name="notable-differences-in-the-web-sdks"></a>Významné rozdíly ve webových sadách SDK

Níže jsou uvedeny některé klíčové rozdíly mezi Mapami Google a webovými sadami Azure Maps, které je třeba znát:

- Kromě poskytování hostovaného koncového bodu pro přístup k webové sdk Azure Maps je k dispozici balíček NPM. Vložte balíček Web SDK do aplikací. Další informace naleznete v této [dokumentaci](how-to-use-map-control.md). Tento balíček také obsahuje definice jazyka TypeScript.
- Nejprve musíte vytvořit instanci třídy Map v Azure Maps. Před programovou `ready` `load` interakcí s mapou počkejte, až se mapy nebo událost vystřelí. Toto pořadí zajistí, že všechny mapové prostředky byly načteny a jsou připraveny k přístupu.
- Obě platformy používají podobný systém obkladů pro základní mapy. Dlaždice v Mapách Google mají rozměr 256 pixelů; dlaždice v Azure Maps jsou však 512 pixelů v dimenzi. Chcete-li v Mapách Azure získat stejné zobrazení mapy jako Mapy Google, odečtěte úroveň zvětšení Map Google o číslo jedna v Mapách Azure.
- Souřadnice v Mapách Google se označují jako "zeměpisná šířka, délka", zatímco Azure Maps používá "zeměpisnou délku,zeměpisnou šířku". Formát Azure Maps je zarovnán se standardem `[x, y]`, po kterém následuje většina platforem GIS.
- Obrazce ve webové složce Azure Maps Web SDK jsou založené na schématu GeoJSON. Pomocné třídy jsou vystaveny prostřednictvím oboru názvů [ *atlas.data* ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data?view=azure-iot-typescript-latest). Je tu také [*atlas. Třída tvarů.*](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape) Pomocí této třídy zabalit GeoJSON objekty, aby bylo snadné aktualizovat a udržovat data vazby způsobem.
- Souřadnice v Azure Maps jsou definovány jako objekty polohy. Souřadnice je zadána jako `[longitude,latitude]`číselné pole ve formátu . Nebo je zadán pomocí nového atlas.data.Position(zeměpisná délka, zeměpisná šířka).
    > [!TIP]
    > Position Třída má statickou pomocnou metodu pro import souřadnic, které jsou ve formátu "zeměpisná šířka, délka". Metoda [atlas.data.Position.fromLatLng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest) může být často `new google.maps.LatLng` nahrazena metodou v kódu Map Google.
- Místo určení informací o stylu na každém obrazci, který je přidán do mapy, Azure Maps odděluje styly od dat. Data jsou uložena ve zdrojích dat a jsou připojena k vykreslovacívrstvým vrstvám. Kód Azure Maps používá zdroje dat k vykreslení dat. Tento přístup poskytuje rozšířené výhody výkonu. Mnoho vrstev navíc podporuje styl založený na datech, kde lze obchodní logiku přidat do voleb stylu vrstvy. Tato podpora mění způsob vykreslení jednotlivých obrazců ve vrstvě na základě vlastností definovaných v obrazci.

## <a name="web-sdk-side-by-side-examples"></a>Příklady sady Web SDK vedle sebe

Tato kolekce obsahuje ukázky kódu pro každou platformu a každý vzorek zahrnuje běžný případ použití. Je určen k migraci webové aplikace z Google Maps V3 JavaScript SDK do sady Azure Maps Web SDK. Ukázky kódu související s webovými aplikacemi jsou k dispozici v JavaScriptu. Azure Maps však také poskytuje definice typescriptu jako další možnost prostřednictvím [modulu NPM](how-to-use-map-control.md).

### <a name="load-a-map"></a>Načtení mapy

Obě sady SDK mají stejné kroky k načtení mapy:

- Přidejte odkaz na sadu Map SDK.
- Přidejte `div` do textu stránky značku, která bude sloužit jako zástupný symbol mapy.
- Vytvořte funkci JavaScriptu, která se zavolá při načtení stránky.
- Vytvořte instanci příslušné třídy mapy.

**Některé klíčové rozdíly**

- Mapy Google vyžadují, aby byl v odkazu na skript rozhraní API zadán klíč účtu. Ověřovací pověření pro Azure Maps jsou určeny jako možnosti třídy mapy. Toto pověření může být klíč předplatného nebo informace služby Azure Active Directory.
- Mapy Google přijímají funkci zpětného volání v odkazu na skript rozhraní API, který se používá k volání funkce inicializace k načtení mapy. Pomocí Azure Maps by se měla použít událost onload stránky.
- Při odkazování `div` na prvek, ve kterém se `Map` bude vykresleno mapování, třída v Azure Maps vyžaduje hodnotu `id` jenom zatímco Mapy Google vyžadují `HTMLElement` objekt.
- Souřadnice v Azure Maps jsou definovány jako objekty position, `[longitude, latitude]`které lze zadat jako jednoduché číselné pole ve formátu .
- Úroveň přiblížení v Azure Maps je o jednu úroveň nižší než úroveň přiblížení v Mapách Google. Tento rozdíl je proto, že rozdíl ve velikostech obkladového systému obou platforem.
- Azure Maps nepřidá žádné ovládací prvky navigace na plátno mapy. Takže ve výchozím nastavení mapa nemá tlačítka lupy a tlačítka stylu mapy. Existují však možnosti ovládání pro přidání výběru stylu mapy, tlačítek lupy, ovládání kompasu nebo rotace a ovládání výšky.
- Obslužná rutina události `ready` se přidá v Azure Maps ke sledování události instance mapy. Tato událost se zapálí po dokončení načítání kontextu WebGL a všech potřebných prostředků. Do této obslužné rutiny události přidejte libovolný kód, který chcete spustit po dokončení načítání mapy.

Níže uvedené základní příklady používají Mapy Google k načtení mapy soustředěné nad New Yorkem na souřadnicích. Zeměpisná délka: -73.985, zeměpisná šířka: 40.747, a mapa je na úrovni přiblížení 12.

**Před: Mapy Google**

Zobrazí mapu Google vystředěnou a zvětšenou nad místem.

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

Spuštění tohoto kódu v prohlížeči zobrazí mapu, která vypadá jako následující obrázek:

<center>

![Jednoduché Mapy Google](media/migrate-google-maps-web-app/simple-google-map.png)</center>

**Po: Mapy Azure**

Načtěte mapu se stejným zobrazením v Azure Maps spolu s ovládacím prvkem stylu mapy a tlačítky lupy.

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

Spuštění tohoto kódu v prohlížeči zobrazí mapu, která vypadá jako následující obrázek:

<center>

![Jednoduché mapy Azure](media/migrate-google-maps-web-app/simple-azure-maps.png)</center>

Najdete v podrobné dokumentaci, jak nastavit a používat ovládací prvek mapy Azure Maps ve webové aplikaci, kliknutím [sem](how-to-use-map-control.md).

> [!NOTE]
> Na rozdíl od Map Google mapy Azure mapy nevyžaduje počáteční centrum a úroveň zvětšení k načtení mapy. Pokud tyto informace nejsou k dispozici při načítání mapy, Mapy Azure se pokusí určit město uživatele. To bude centrum a zoom mapy tam.

**Další prostředky:**

- Azure Maps také poskytuje navigační ovládací prvky pro otáčení a pitching zobrazení mapy, jak je popsáno [zde](map-add-controls.md).

### <a name="localizing-the-map"></a>Lokalizace mapy

Pokud je okruh uživatelů rozložen do více zemí nebo mluví různými jazyky, je důležitá lokalizace.

**Před: Mapy Google**

Chcete-li lokalizovat Mapy Google, přidejte parametry jazyka a oblasti.

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

Zde je příklad Map Google s jazykem nastaveným na "fr-FR".

<center>

![Lokalizace Map Google](media/migrate-google-maps-web-app/google-maps-localization.png)</center>

**Po: Mapy Azure**

Azure Maps poskytuje dva různé způsoby nastavení jazyka a místní zobrazení mapy. První možností je přidat tyto informace do globálního oboru názvů *atlasu.* Výsledkem budou všechny instance ovládacího prvku mapy v aplikaci, které se ve výchozím nastavení dostanou do těchto nastavení. Následující nastaví jazyk na francouzštinu ("fr-FR") a regionální zobrazení na "auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

Druhou možností je předat tyto informace do možností mapy při načítání mapy. Nějak tak:

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
> S Azure Maps je možné načíst více instancí mapy na stejné stránce s nastavením jazyka a oblasti. Je také možné aktualizovat tato nastavení v mapě po načtení. 

Podrobný seznam [podporovaných jazyků](supported-languages.md) najdete v Azure Maps.

Tady je příklad Azure Maps s jazykem nastaveným na "fr" a oblastí uživatele nastavenou na "fr-FR".

<center>

![Lokalizace Map Azure](media/migrate-google-maps-web-app/azure-maps-localization.png)</center>

### <a name="setting-the-map-view"></a>Nastavení zobrazení mapy

Dynamické mapy v Azure i V Mapách Google lze programově přesunout do nových geografických umístění. Chcete-li tak učinit, zavolejte příslušné funkce v jazyce JavaScript. Příklady ukazují, jak vytvořit mapu, jak vytvořit satelitní letecké snímky, vystředit mapu nad místem a změnit úroveň přiblížení na 15 v Mapách Google. Používají se následující souřadnice umístění: zeměpisná délka: -111.0225 a zeměpisná šířka: 35.0272.

> [!NOTE]
> Mapy Google používají dlaždice o rozměrech 256 pixelů, zatímco Azure Maps používají větší dlaždici o velikosti 512 pixelů. Azure Maps proto vyžaduje menší počet síťových požadavků k načtení stejné oblasti mapy jako Mapy Google. Vzhledem ke způsobu, jakým dlaždice pyramidy pracovat v mapových ovládacích prvků, musíte odečíst úroveň zvětšení používané v Mapách Google číslo jedna při používání Azure Maps. Tato aritmetická operace zajišťuje, že větší dlaždice v Mapách Azure vykreslují stejnou oblast mapy jako v Mapách Google,

**Před: Mapy Google**

Přesuňte ovládací prvek mapy `setOptions` Google pomocí metody. Tato metoda umožňuje určit střed mapy a úroveň přiblížení.

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![Zobrazení nastavení Map Google](media/migrate-google-maps-web-app/google-maps-set-view.png)</center>

**Po: Mapy Azure**

V Azure Maps změňte polohu mapy pomocí `setCamera` metody `setStyle` a změňte styl mapy pomocí metody. Souřadnice v Azure Maps jsou ve formátu "zeměpisná délka, šířka" a hodnota úrovně zvětšení se odečte o jednu.

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

![Zobrazení nastavení Map Azure](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)</center>

**Další prostředky:**

- [Výběr stylu mapy](choose-map-style.md)
- [Podporované styly map](supported-map-styles.md)

### <a name="adding-a-marker"></a>Přidání značky

V Azure Maps existuje několik způsobů, jak bodová data mohou být vykreslena na mapě:

- **Značky HTML** – Vykresluje body pomocí tradičních prvků DOM. Značky HTML podporují přetahování.
- **Vrstva symbolů** – Vykreslí body s ikonou nebo textem v kontextu WebGL.
- **Bublinová vrstva** – Vykresluje body jako kruhy na mapě. Poloměry kružnic lze škálovat na základě vlastností v datech.

Vykreslovat vrstvy symbolů a bublinové vrstvy v kontextu WebGL. Obě vrstvy mohou vykreslit velké sady bodů na mapě. Tyto vrstvy vyžadují, aby byla data uložena ve zdroji dat. Zdroje dat a vykreslovací `ready` vrstvy by měly být přidány do mapy po události má aktivována. Značky HTML jsou vykresleny jako prvky dom na stránce a nepoužívají zdroj dat. Čím více prvků dom stránka má, tím pomalejší stránka se stane. Pokud vykreslujete více než několik set bodů na mapě, doporučujeme místo toho použít jednu z vrstev vykreslování.

Přidáme značku na mapu na číslo 10 překryté jako štítek. Použijte délku: -0,2 a šířku: 51,5.

**Před: Mapy Google**

V Mapách Google přidejte značky `google.maps.Marker` do mapy pomocí třídy a určete mapu jako jednu z možností.

```javascript
//Create a marker and add it to the map.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    label: '10',
    map: map
});
```

<center>

![Značka Map Google](media/migrate-google-maps-web-app/google-maps-marker.png)</center>

**Po: Mapy Azure pomocí značek HTML**

V Azure Maps použijte značky HTML k zobrazení bodu na mapě. Značky HTML se doporučují pro aplikace, které potřebují zobrazit na mapě pouze malý počet bodů. Chcete-li použít značku HTML, `atlas.HtmlMarker` vytvořte instanci třídy. Nastavte volby textu a polohy a přidejte `map.markers.add` značku do mapy pomocí metody.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![Značka HTML azure maps](media/migrate-google-maps-web-app/azure-maps-html-marker.png)</center>

**Po: Mapy Azure pomocí vrstvy symbolů**

Pro vrstvu symbolů přidejte data do zdroje dat. Připojte zdroj dat k vrstvě. Kromě toho zdroj dat a vrstva by měla `ready` být přidána do mapy po událost byla aktivována. Chcete-li vykreslit jedinečnou textovou hodnotu nad symbolem, musí být textové informace uloženy jako vlastnost datového bodu. Vlastnost musí být odkazována ve volbě `textField` vrstvy. Tento přístup je o něco více práce než pomocí značek HTML, ale je lepší výkon.

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

![Vrstva symbolů Azure Maps](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)</center>

**Další prostředky:**

- [Vytvoření zdroje dat](create-data-source-web-sdk.md)
- [Přidání vrstvy symbolu](map-add-pin.md)
- [Přidání bublinové vrstvy](map-add-bubble-layer.md)
- [Data bodů clusteru](clustering-point-data-web-sdk.md)
- [Přidání značek HTML](map-add-custom-html.md)
- [Použití výrazů pro styly založené na datech](data-driven-style-expressions-web-sdk.md)
- [Volby ikony vrstvy symbolu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Text vrstvy symbolu, volba](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [Třída značky HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Volby značky HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-custom-marker"></a>Přidání vlastní značky

Vlastní obrázky můžete použít k reprezentaci bodů na mapě. Níže uvedená mapa používá vlastní obrázek k zobrazení bodu na mapě. Bod je zobrazen v zeměpisné šířce: 51.5 a zeměpisné délky: -0.2. Kotva odsadí polohu značky tak, aby se bod ikony připínáčku zarovnal se správnou polohou na mapě.

<center>

![žlutý připínáček](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_pushpin.png</center>

**Před: Mapy Google**

Vytvořte vlastní značku zadáním objektu, `Icon` který obsahuje `url` obraz. Určete `anchor` bod, který zarovná bod obrázku připínáčku s souřadnicí na mapě. Hodnota ukotvení v Mapách Google je relativní vzhledem k levému hornímu rohu obrázku.

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

![Vlastní značka Map Google](media/migrate-google-maps-web-app/google-maps-custom-marker.png)</center>

**Po: Mapy Azure pomocí značek HTML**

Chcete-li přizpůsobit značku HTML, přejděte html `string` nebo `HTMLElement` `htmlContent` na volbu značky. Pomocí `anchor` této volby můžete určit relativní polohu značky vzhledem ke souřadnici polohy. Přiřaďte k možnosti `anchor` jeden z devíti definovaných referenčních bodů. Tyto definované body jsou: "střed", "nahoře", "dole", "vlevo", "vpravo", "vlevo nahoře", "vpravo nahoře", "vlevo dole", "vpravo dole". Obsah je ve výchozím nastavení ukotven do dolního středu obsahu html. Chcete-li usnadnit migraci kódu z `anchor` Map Google, nastavte možnost "vlevo nahoře" a pak použijte `pixelOffset` možnost se stejným posunem, který se používá v Mapách Google. Posuny v Azure Maps se pohybují v opačném směru posunů v Mapách Google. Takže vynásobte posuny mínus jedna.

> [!TIP]
> Chcete-li `pointer-events:none` zakázat výchozí chování přetahování v aplikaci Microsoft Edge, které zobrazí nežádoucí ikonu, přidat jako styl obsahu html.

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Vlastní značka HTML Azure Maps](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)</center>

**Po: Mapy Azure pomocí vrstvy symbolů**

Vrstvy symbolů v Azure Maps podporují také vlastní image. Nejprve načtěte obrázek do mapových zdrojů a přiřaďte mu jedinečné ID. Odkazna obraz ve vrstvě symbolů. Pomocí `offset` volby můžete zarovnat obraz ke správnému bodu na mapě. Pomocí `anchor` této volby určete relativní polohu symbolu vzhledem ke souřadnicím polohy. Použijte jeden z devíti definovaných referenčních bodů. Tyto body jsou: "střed", "nahoře", "dole", "vlevo", "vpravo", "vlevo nahoře", "vpravo nahoře", "vlevo dole", "vpravo dole". Obsah je ve výchozím nastavení ukotven do dolního středu obsahu html. Chcete-li usnadnit migraci kódu z `anchor` Map Google, nastavte možnost "vlevo nahoře" a pak použijte `offset` možnost se stejným posunem, který se používá v Mapách Google. Posuny v Azure Maps se pohybují v opačném směru posunů v Mapách Google. Takže vynásobte posuny mínus jedna.

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

![Vrstva symbolů vlastních ikon Azure Maps](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</center>

> [!TIP]
> Chcete-li vykreslit pokročilé vlastní body, použijte dohromady více vrstev vykreslování. Řekněme například, že chcete mít více připínáčků, které mají stejnou ikonu na různých barevných kruzích. Místo vytváření hromady obrazů pro každé překrytí barev přidejte vrstvu symbolu nad bublinovou vrstvu. Aby připínáčky odkazovaly na stejný zdroj dat. Tento přístup bude efektivnější než vytváření a udržování spoustu různých obrázků.

**Další prostředky:**

- [Vytvoření zdroje dat](create-data-source-web-sdk.md)
- [Přidání vrstvy symbolu](map-add-pin.md)
- [Přidání značek HTML](map-add-custom-html.md)
- [Použití výrazů pro styly založené na datech](data-driven-style-expressions-web-sdk.md)
- [Volby ikony vrstvy symbolu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Text vrstvy symbolu, volba](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [Třída značky HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Volby značky HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-polyline"></a>Přidání čáry

Pomocí čar reprezentujte čáru nebo cestu na mapě. Na mapě vytvoříme přerušovanou polyčáru.

**Před: Mapy Google**

Třída Polyline přijímá sadu možností. Předá pole souřadnic ve volbě `path` mely.

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

![Pointa Map Google](media/migrate-google-maps-web-app/google-maps-polyline.png)</center>

**Po: Mapy Azure**

Předtextových linií `LineString` `MultiLineString` jsou volány nebo objekty. Tyto objekty lze přidat do zdroje dat a vykreslit pomocí čárové vrstvy. Přidejte `LineString` do zdroje dat a pak `LineLayer` přidejte zdroj dat do zdroje, který jej vykreslí.

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

![Předlavená čára Azure Maps](media/migrate-google-maps-web-app/azure-maps-polyline.png)</center>

**Další prostředky:**

- [Přidání čar do mapy](map-add-line-layer.md)
- [Volby vrstvy čáry](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Použití výrazů pro styly založené na datech](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Přidání polygonu

Azure Maps a Mapy Google poskytují podobnou podporu pro polygony. Polygony se používají k reprezentaci oblasti na mapě. Následující příklady ukazují, jak vytvořit polygon, který tvoří trojúhelník založený na středové souřadnici mapy.

**Před: Mapy Google**

Třída Polygon přijímá sadu možností. Předá pole souřadnic `paths` na možnost polygonu.

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

![Polygon map Google](media/migrate-google-maps-web-app/google-maps-polygon.png)</center>

**Po: Mapy Azure**

Přidejte `Polygon` nebo `MultiPolygon` objekty do zdroje dat. Vykreslete objekt na mapě pomocí vrstev. Vykreslete oblast polyga pomocí vrstvy polygonu. A vykreslete obrys polygonu pomocí čárové vrstvy.

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

![Polygon map Azure](media/migrate-google-maps-web-app/azure-maps-polygon.png)</center>

**Další prostředky:**

- [Přidání polygonu do mapy](map-add-shape.md)
- [Přidání kruhu do mapy](map-add-shape.md#add-a-circle-to-the-map)
- [Volby vrstvy vícepanelů](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)
- [Volby vrstvy čáry](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Použití výrazů pro styly založené na datech](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>Zobrazení informačního okna

Další informace o entitě lze na `google.maps.InfoWindow` mapě zobrazit jako kurz v Mapách Google. V Azure Maps lze tuto funkci `atlas.Popup` dosáhnout pomocí třídy. Další příklady přidávají do mapy značku. Po klepnutí na značku se zobrazí informační okno nebo vyskakovací okno.

**Před: Mapy Google**

Vytvořte instanci informačního `google.maps.InfoWindow` okna pomocí konstruktoru.

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

![Vyskakovací okno Mapy Google](media/migrate-google-maps-web-app/google-maps-popup.png)</center>

**Po: Mapy Azure**

Pomocí místního vyskakovacího panelu zobrazíme další informace o umístění. Předaj `string` `HTMLElement` te-li `content` html nebo objekt, tak se zobrazí možnost vyskakovacího okno. Pokud chcete, vyskakovací okno lze zobrazit nezávisle na libovolném tvaru. Místní líná ňady tedy vyžadují `position` zadání hodnoty. Zadejte `position` hodnotu. Chcete-li zobrazit vyskakovací okno, zavolejte metodu `open` a předejte, `map` ve kterém má být vyskakovací okno zobrazeno.

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

![Místní okno S Azure Maps](media/migrate-google-maps-web-app/azure-maps-popup.png)</center>

> [!NOTE]
> Totéž můžete udělat se symbolem, bublinou, čárou nebo vrstvou polygulu předáním vybrané vrstvy do kódu události mapy namísto značky.

**Další prostředky:**

- [Přidání místního okna](map-add-popup.md)
- [Místní okno s mediálním obsahem](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [Místní líací okno u obrazců](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [Opakované použití místních přiskupovacích možností s více piny](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [Třída místního přiskonovacího](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
- [Možnosti místního blokování](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

### <a name="import-a-geojson-file"></a>Import souboru GeoJSON

Mapy Google podporují načítání a dynamicky styling `google.maps.Data` geojson dat prostřednictvím třídy. Funkce této třídy je mnohem více v souladu s daty řízený styl Azure Maps. Ale je tu klíčový rozdíl. V Mapách Google zadáte funkci zpětného volání. Obchodní logika pro styling každé funkce, které zpracovává jednotlivě ve vlákně uživatelského rozhraní. Ale v Azure Maps vrstvy podporují určení výrazy řízené daty jako možnosti stylingu. Tyto výrazy jsou zpracovány v době vykreslení v samostatném vlákně. Přístup Azure Maps zlepšuje výkon vykreslování. Tato výhoda je zaznamenána, když větší sady dat je třeba vykreslit rychle.

Následující příklady načíst GeoJSON krmivo všech zemětřesení za posledních sedm dní z USGS. Údaje o zemětřeseních se vykreslují jako kruhy v měřítku na mapě. Barva a měřítko každého kruhu je založena na velikosti každého `"mag"` zemětřesení, které je uloženo ve vlastnostech každé funkce v datové sadě. Pokud je velikost větší nebo rovna pěti, kruh bude červený. Pokud je větší nebo rovna třem, ale méně než pěti, kruh bude oranžový. Pokud je to méně než tři, kruh bude zelený. Poloměr každého kružnice bude exponenciální velikosti vynásobené 0,1.

**Před: Mapy Google**

Zadejte jednu funkci `map.data.setStyle` zpětného volání v metodě. Uvnitř funkce zpětného volání použijte obchodní logiku pro každou funkci. Vložte posuv GeoJSON `map.data.loadGeoJson` metodou.

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

![Google Maps GeoJSON](media/migrate-google-maps-web-app/google-maps-geojson.png)</center>

**Po: Mapy Azure**

GeoJSON je základní datový typ v Azure Maps. Importujte jej do zdroje `datasource.importFromUrl` dat pomocí metody. Použijte bublinovou vrstvu. Bublinová vrstva poskytuje funkce pro vykreslování kruhů s měřítkem na základě vlastností prvků ve zdroji dat. Místo funkce zpětného volání je obchodní logika převedena na výraz a předána do možností stylu. Výrazy definují, jak funguje obchodní logika. Výrazy mohou být předány do jiného vlákna a vyhodnoceny podle dat funkce. Do Azure Maps se dá přidat víc zdrojů a vrstev dat, každý s jinou obchodní logikou. Tato funkce umožňuje vykreslení více datových sad na mapě různými způsoby.

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

![Azure Maps GeoJSON](media/migrate-google-maps-web-app/azure-maps-geojson.png)</center>

**Další prostředky:**

- [Přidání vrstvy symbolu](map-add-pin.md)
- [Přidání bublinové vrstvy](map-add-bubble-layer.md)
- [Data bodů clusteru](clustering-point-data-web-sdk.md)
- [Použití výrazů pro styly založené na datech](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>Clustering značek

Při vizualizaci mnoha datových bodů na mapě se body mohou navzájem překrývat. Díky překrytí bude mapa přeplněná a je obtížné ji přečíst a používat. Data bodů clusteru je proces kombinování datových bodů, které jsou blízko sebe a představují je na mapě jako jeden clusterovaný datový bod. Jak se uživatel přiblíží k mapě, clustery se rozpadají na své jednotlivé datové body. Datové body clusteru pro zlepšení uživatelského prostředí a výkonu mapování.

V následujících příkladech kód načte zdroj GeoJSON dat zemětřesení z minulého týdne a přidá je do mapy. Klastry jsou vykresleny jako barevné kruhy se změnami a barevné kruhy. Měřítko a barva kruhů závisí na počtu bodů, které obsahují.

> [!NOTE]
> Mapy Google a Mapy Azure používají mírně odlišné algoritmy clusteringu. Jako takové někdy rozložení bodu v clusterech se liší.

**Před: Mapy Google**

Ke značkám clusteru použijte knihovnu MarkerCluster. Ikony clusteru jsou omezeny na obrázky, které mají čísla jedna až pět jako jejich název. Jsou hostovány ve stejném adresáři.

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

![Clustering Map Google](media/migrate-google-maps-web-app/google-maps-clustering.png)</center>

**Po: Mapy Azure**

Přidejte a spravujte data ve zdroji dat. Připojte zdroje dat a vrstvy a pak je vykreslete. Třída `DataSource` v Azure Maps poskytuje několik možností clusteringu.

- `cluster`– Sděluje zdroj dat datům bodů clusteru.
- `clusterRadius`- Poloměr v pixelech pro shlukbodů dohromady.
- `clusterMaxZoom`- Maximální úroveň zvětšení, ve kterém dochází k clusteringu. Pokud přiblížíte více než tuto úroveň, všechny body se vykreslí jako symboly.
- `clusterProperties`- Definuje vlastní vlastnosti, které jsou vypočteny pomocí výrazů proti všem bodům v rámci každého clusteru a přidány do vlastností každého bodu clusteru.

Je-li clustering povolen, bude zdroj dat odesílat clusterované a neclusterované datové body do vrstev pro vykreslování. Zdroj dat je schopen clustering stovky tisíc datových bodů. Clusterovaný datový bod má následující vlastnosti:

| Název vlastnosti             | Typ    | Popis   |
|---------------------------|---------|---------------|
| `cluster`                 | Boolean | Označuje, zda funkce představuje cluster. |
| `cluster_id`              | řetězec  | Jedinečné ID clusteru, které lze použít `getClusterExpansionZoom`se `getClusterChildren`zdrojem dat , a `getClusterLeaves` metodami. |
| `point_count`             | číslo  | Počet bodů, které cluster obsahuje.  |
| `point_count_abbreviated` | řetězec  | Řetězec, který zkracuje `point_count` hodnotu, pokud je dlouhá. (například 4 000 se změní na 4K)  |

Třída `DataSource` má následující pomocnou funkci pro přístup k dalším `cluster_id`informacím o clusteru pomocí .

| Metoda | Návratový typ | Popis |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | Geometrie&lt;prvku pole&gt; \| &lt;&lt;Slib, libovolný tvar&gt;&gt; | Načte podřízené děti daného clusteru na další úrovni přiblížení. Tyto podřízené položky mohou být kombinací tvarů a podclusterů. Podclustery budou funkce s vlastnostmi odpovídající clusteredProperties. |
| `getClusterExpansionZoom(clusterId: number)` | Číslo&lt;příslibu&gt; | Vypočítá úroveň zvětšení, při které se cluster začne rozšiřovat nebo rozpadat. |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | Geometrie&lt;prvku pole&gt; \| &lt;&lt;Slib, libovolný tvar&gt;&gt; | Načte všechny body v clusteru. Nastavte `limit` pro vrácení podmnožiny bodů a `offset` použijte stránku procházet body. |

Při vykreslování seskupených dat na mapě je často nejlepší použít dvě nebo více vrstev. Následující příklad používá tři vrstvy. Bublinová vrstva pro kreslení barevných kruhů s měřítkem na základě velikosti clusterů. Vrstva symbolů, která vykreslí velikost clusteru jako text. A používá druhou vrstvu symbolu pro vykreslení neseskupených bodů. Existuje mnoho dalších způsobů, jak vykreslit clusterovaná data. Další informace naleznete v dokumentaci k [datům bodů clusteru.](clustering-point-data-web-sdk.md)

Přímo importujte data GeoJSON pomocí `importDataFromUrl` funkce ve `DataSource` třídě v mapě Azure Maps.

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

![Clustering Map Azure](media/migrate-google-maps-web-app/azure-maps-clustering.png)</center>

**Další prostředky:**

- [Přidání vrstvy symbolu](map-add-pin.md)
- [Přidání bublinové vrstvy](map-add-bubble-layer.md)
- [Data bodů clusteru](clustering-point-data-web-sdk.md)
- [Použití výrazů pro styly založené na datech](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Přidání tepelné mapy

Tepelné mapy, známé také jako mapy hustoty bodů, jsou typem vizualizace dat. Používají se k reprezentaci hustoty dat pomocí rozsahu barev. A často se používají k zobrazení dat "hot spots" na mapě. Tepelné mapy jsou skvělý způsob, jak vykreslit velké sady dat bodů.

Následující příklady načíst GeoJSON krmivo všech zemětřesení za poslední měsíc, z USGS, a vykresluje je jako vážené tepelné mapy. Vlastnost `"mag"` se používá jako hmotnost.

**Před: Mapy Google**

Chcete-li vytvořit tepelnou mapu, načtěte `&libraries=visualization` knihovnu "vizualizace" přidáním adresy URL skriptu rozhraní API. Vrstva tepelné mapy v Mapách Google nepodporuje data GeoJSON přímo. Nejprve stáhněte data a převeďte je na pole vážených datových bodů:

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

![Tepelná mapa Map Google](media/migrate-google-maps-web-app/google-maps-heatmap.png)</center>

**Po: Mapy Azure**

Načtěte data GeoJSON do zdroje dat a připojte zdroj dat k vrstvě tepelné mapy. Vlastnost, která bude použita pro hmotnost `weight` může být předána do možnosti pomocí výrazu. Přímo importujte data GeoJSON `importDataFromUrl` do Azure `DataSource` Maps pomocí funkce ve třídě.

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

![Tepelná mapa Azure Maps](media/migrate-google-maps-web-app/azure-maps-heatmap.png)</center>

**Další prostředky:**

- [Přidání vrstvy heat mapy](map-add-heat-map-layer.md)
- [Třída vrstvy tepelné mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.heatmaplayer?view=azure-iot-typescript-latest)
- [Volby vrstvy tepelné mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)
- [Použití výrazů pro styly založené na datech](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Překrytí vrstvy dlaždic

Vrstvy dlaždic v Mapách Azure se v Mapách Google označují jako překryvné obrázky. Vrstvy dlaždic umožňují překrýt velké obrazy, které byly rozděleny na menší dlaždicové obrazy, které odpovídají systému dlaždic map. Tento přístup se běžně používá k překrytí velkých obrázků nebo velkých datových sad.

Následující příklady překrýt počasí radar dlaždice vrstva z Iowa životního prostředí Mesonet z Iowa State University.

**Před: Mapy Google**

V Mapách Google lze vrstvy `google.maps.ImageMapType` dlaždic vytvářet pomocí třídy.

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

![Vrstva dlaždic V Mapách Google](media/migrate-google-maps-web-app/google-maps-tile-layer.png)</center>

**Po: Mapy Azure**

Přidejte do mapy vrstvu dlaždic podobně jako jakákoli jiná vrstva. Použijte formátovanou adresu URL, která má zástupné symboly pro přiblížení x, y; `{x}`, `{y}` `{z}` , chcete-li vrstvě sdělit, kam má přístup k dlaždicím. Vrstvy dlaždic Azure `{quadkey}` `{bbox-epsg-3857}`Maps `{subdomain}` také podporují , a zástupné symboly.

> [!TIP]
> V Azure Maps vrstvy lze snadno vykreslit pod jinými vrstvami, včetně základních mapvrstvy vrstvy. Často je žádoucí vykreslit vrstvy dlaždic pod popisky mapy tak, aby byly snadno čitelné. Metoda `map.layers.add` přebírá druhý parametr, který je id vrstvy, do které chcete vložit novou vrstvu níže. Chcete-li vložit vrstvu dlaždice pod popisky mapy, použijte tento kód:`map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

![Vrstva dlaždic Azure Maps](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)</center>

> [!TIP]
> Požadavky na dlaždice lze zachytit `transformRequest` pomocí možnosti mapy. To vám umožní upravit nebo přidat záhlaví požadavku v případě potřeby.

**Další prostředky:**

- [Přidání vrstev dlaždic](map-add-tile-layer.md)
- [Třída vrstvy dlaždice](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)
- [Volby vrstvy dlaždic](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

### <a name="show-traffic"></a>Zobrazení provozu

Dopravní data mohou překryté mapy Azure i Google.

**Před: Mapy Google**

Překryvte dopravní údaje na mapě pomocí dopravní vrstvy.

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```

<center>

![Návštěvnost Map Google](media/migrate-google-maps-web-app/google-maps-traffic.png)</center>

**Po: Mapy Azure**

Azure Maps poskytuje několik různých možností pro zobrazení provozu. Zobrazit dopravní události, jako jsou uzavírky silnic a nehody jako ikony na mapě. Překryvný dopravní tok a barevně odlišené silnice na mapě. Barvy lze upravit na základě zaúčtovaného rychlostního limitu vzhledem k normálnímu očekávanému zpoždění nebo absolutnímu zpoždění. Data incidentů v Azure Maps se aktualizují každou minutu a aktualizují se každé dvě minuty.

Přiřaďte `setTraffic` požadované hodnoty pro možnosti.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

![Provoz na Azure Maps](media/migrate-google-maps-web-app/azure-maps-traffic.png)</center>

Pokud kliknete na jednu z ikon provozu v Azure Maps, další informace se zobrazí ve vyskakovacím centru.

<center>

![Dopravní nehoda v Azure Maps](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)</center>

**Další prostředky:**

- [Zobrazit provoz na mapě](map-show-traffic.md)
- [Možnosti překrytí provozu](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>Přidání překrytí terénu

Mapy Azure i Google podporují překrytí georeferencovaných obrázků na mapě. Georeferenční obrazy se při posouvání a zvětšování mapy přesouvají a zvětšují. V Mapách Google se georeferenční obrázky označují jako pozemní překryvy, zatímco v Mapách Azure se označují jako vrstvy obrázků. Jsou skvělé pro vytváření půdorysů, překrytí starých map nebo snímků z dronu.

**Před: Mapy Google**

Zadejte adresu URL obrázku, který chcete překrýt, a ohraničovací rámeček, který obraz na mapě sváže. Tento příklad překresluje obraz mapy [Newark New Jersey z roku 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) na mapě.

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

Spuštění tohoto kódu v prohlížeči zobrazí mapu, která vypadá jako následující obrázek:

<center>

![Překrytí obrázků v Mapách Google](media/migrate-google-maps-web-app/google-maps-image-overlay.png)</center>

**Po: Mapy Azure**

Třídu `atlas.layer.ImageLayer` použijte k překrytí georeferencovaných obrazů. Tato třída vyžaduje adresu URL obrázku a sadu souřadnic pro čtyři rohy obrázku. Obrázek musí být hostován buď ve stejné doméně, nebo musí mít povoleno u rychlo omezení.

> [!TIP]
> Pokud máte pouze informace o severu, jihu, východu, západu a otočení a nemáte souřadnice [`atlas.layer.ImageLayer.getCoordinatesFromEdges`](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest#getcoordinatesfromedges-number--number--number--number--number-) pro každý roh obrázku, můžete použít statickou metodu.

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

![Překrytí image Azure Maps](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)</center>

**Další prostředky:**

- [Překryv obrázku](map-add-image-layer.md)
- [Třída vrstvy obrazu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

## <a name="add-kml-to-the-map"></a>Přidat KML do mapy

Mapy Azure i Google mohou importovat a vykreslovat data KML, KMZ a GeoRSS na mapě. Azure Maps také podporuje GPX, GML, prostorové CSV soubory, GeoJSON, dobře známý text (WKT), web mapping ové služby (WMS), web mapping tile services (WMTS) a služby webových funkcí (WFS). Azure Maps čte soubory místně do paměti a ve většině případů může zpracovávat mnohem větší soubory KML. 

**Před: Mapy Google**


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

Spuštění tohoto kódu v prohlížeči zobrazí mapu, která vypadá jako následující obrázek:

<center>

![Překrytí obrázků v Mapách Google](media/migrate-google-maps-web-app/google-maps-kml.png)</center>

**Po: Mapy Azure**

V Azure Maps je GeoJSON hlavním datovým formátem používaným ve webové sdk, další formáty prostorových dat lze snadno integrovat pomocí [prostorového vomodulu](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/). Tento modul má funkce pro čtení i zápis prostorových dat a také obsahuje jednoduchou datovou vrstvu, která může snadno vykreslit data z některého z těchto formátů prostorových dat. Chcete-li číst data v souboru s prostorovými daty, jednoduše předavěte adresu URL nebo nezpracovaná data jako řetězec nebo objekt blob do `atlas.io.read` funkce. Tím se vrátí všechna analyzovaná data ze souboru, které pak mohou být přidány do mapy. KML je o něco složitější než většina formátů prostorových dat, protože obsahuje mnohem více informací o stylu. Třída `SpatialDataLayer` podporuje vykreslování většiny těchto stylů, ale ikony obrázky musí být načteny do mapy před načtením dat funkce a pozemní překryvy musí být přidány jako vrstvy do mapy samostatně. Při načítání dat prostřednictvím adresy URL by měla být hostována na koncovém bodu s povolenou funkcí CORs nebo by měla být jako možnost do funkce čtení předána služba proxy. 

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

<center>

![Překrytí image Azure Maps](media/migrate-google-maps-web-app/azure-maps-kml.png)</center>

**Další prostředky:**

- [funkce atlas.io.read](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io?view=azure-maps-typescript-latest#read-string---arraybuffer---blob--spatialdatareadoptions-)
- [SimpleDataLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)
- [SimpleDataLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

## <a name="additional-code-samples"></a>Další ukázky kódu

Níže jsou uvedeny některé další ukázky kódu související s migrací Map Google:

- [Nástroje pro kreslení](map-add-drawing-toolbar.md)
- [Omezit mapu na posouvání dvou prstů](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
- [Omezit zvětšení rolovacího kolečka](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
- [Vytvoření ovládacího prvku na celou obrazovku](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**Služby:**

- [Použití modulu služeb Azure Maps](how-to-use-services-module.md)
- [Hledání bodů zájmu](map-search-location.md)
- [Získání informací z souřadnice (reverzní geokód)](map-get-information-from-coordinate.md)
- [Zobrazení pokynů pro trasu z A do B](map-route.md)
- [Hledat autosuggest s jquery ui](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Mapování Map Google V3 do mapování webových sad SDK azure mapování

Následující dodatek obsahuje křížový odkaz na běžně používané třídy v Mapách Google V3 a ekvivalentní webovou sadu Azure Maps.

### <a name="core-classes"></a>Základní třídy

| Mapy Google   | Azure Maps  |
|---------------|-------------|
| `google.maps.Map` | [Atlas. Mapu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindow` | [Atlas. Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindowOptions` | [Atlas. Možnosti místního nabídky](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [atlas.data.Pozice](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest)  |
| `google.maps.LatLngBounds` | [atlas.data.Ohraničující box](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) |
| `google.maps.MapOptions`  | [Atlas. Možnosti fotoaparátu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions?view=azure-iot-typescript-latest)<br/>[Atlas. Možnosti camerabounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions?view=azure-iot-typescript-latest)<br/>[Atlas. Možnosti služby](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions?view=azure-iot-typescript-latest)<br/>[Atlas. Možnosti stylu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions?view=azure-iot-typescript-latest)<br/>[Atlas. Možnosti interakce uživatele](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions?view=azure-iot-typescript-latest) |
| `google.maps.Point`  | [Atlas. Pixel](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.pixel?view=azure-iot-typescript-latest)   |

## <a name="overlay-classes"></a>Překryvné třídy

| Mapy Google  | Azure Maps  |
|--------------|-------------|
| `google.maps.Marker` | [Atlas. Značka Html](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)<br/>[atlas.data.Bod](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)  |
| `google.maps.MarkerOptions`  | [Atlas. Možnosti htmlznaček](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)<br/>[atlas.layer.SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)<br/>[Atlas. SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)<br/>[Atlas. IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)<br/>[Atlas. TextMožnosti](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)<br/>[atlas.layer.BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)<br/>[Atlas. Možnosti bublinové vrstvy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.Polygon`  | [atlas.data.Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)               |
| `google.maps.PolygonOptions` |[atlas.layer.PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)<br/> [Atlas. Možnosti vícevrstvé vrstvy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)<br/> [atlas.layer.LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)<br/> [Atlas. Možnosti linelayeru](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)|
| `google.maps.Polyline` | [atlas.data.LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest)         |
| `google.maps.PolylineOptions` | [atlas.layer.LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-maps-typescript-latest)<br/>[Atlas. Možnosti linelayeru](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-maps-typescript-latest) |
| `google.maps.Circle`  | Viz [Přidání kruhu do mapy](map-add-shape.md#add-a-circle-to-the-map)                                     |
| `google.maps.ImageMapType`  | [Atlas. Vrstva dlaždic](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)         |
| `google.maps.ImageMapTypeOptions` | [Atlas. Možnosti vrstvy tile](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.GroundOverlay`  | [atlas.layer.ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)<br/>[Atlas. ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest) |

## <a name="service-classes"></a>Třídy služeb

Webová sada Azure Maps Web SDK obsahuje modul služeb, který lze načíst samostatně. Tento modul zabalí služby Azure Maps REST pomocí webového rozhraní API a dá se použít v aplikacích JavaScript, TypeScript a Node.js.

| Mapy Google | Azure Maps  |
|-------------|-------------|
| `google.maps.Geocoder` | [atlas.service.SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |
| `google.maps.GeocoderRequest`  | [Atlas. SearchAddressOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchAddressRevrseOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchAddressReverseCrossStreetOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchAddressStructuredOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchAlongRouteOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions?view=azure-iot-typescript-latest)<br/>[Atlas. HledatFuzzyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchInsideGeometryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchNearbyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchPOIOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoioptions?view=azure-iot-typescript-latest)<br/>[Atlas. SearchPOICategoryOptionsOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions?view=azure-iot-typescript-latest) |
| `google.maps.DirectionsService`  | [atlas.service.RouteUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)  |
| `google.maps.DirectionsRequest`  | [Atlas. CalculateRouteDirectionsOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions?view=azure-iot-typescript-latest) |
| `google.maps.places.PlacesService` | [atlas.service.SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |

## <a name="libraries"></a>Knihovny

Knihovny přidat další funkce do mapy. Mnoho z těchto knihoven je v základní sdk Azure Maps. Zde jsou některé ekvivalentní třídy, které lze použít místo těchto knihoven Map Google

| Mapy Google           | Azure Maps   |
|-----------------------|--------------|
| Knihovna výkresů       | [Modul nástrojů pro kreslení](set-drawing-options.md) |
| Knihovna geometrie      | [atlas.matematika](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)   |
| Knihovna vizualizací | [Vrstva tepelné mapy](map-add-heat-map-layer.md) |

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o webové sdk Azure Maps.

> [!div class="nextstepaction"]
> [Jak používat ovládací prvek mapy](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Jak používat modul služeb](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Jak používat modul kreslicích nástrojů](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Ukázky kódu](https://docs.microsoft.com/samples/browse/?products=azure-maps)


---
title: Čtení a zápis prostorových dat | Mapy Microsoft Azure
description: Naučte se číst a zapisovat data pomocí modulu pro prostorové v/v, který poskytuje Azure Maps Web SDK.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 458b307cf1158c467100e032e3f789462e8fdcca
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370905"
---
# <a name="read-and-write-spatial-data"></a>Čtení a zápis prostorových dat

Následující tabulka uvádí formáty prostorových souborů, které jsou podporované pro operace čtení a zápisu pomocí modulu prostorového vstupu/výstupu.

| Formát dat       | Čtení | Zápis |
|-------------------|------|-------|
| GeoJSON           | ✓  |  ✓  |
| GeoRSS            | ✓  |  ✓  |
| GML               | ✓  |  ✓  |
| GPX               | ✓  |  ✓  |
| KML               | ✓  |  ✓  |
| KMZ               | ✓  |  ✓  |
| Prostorový CSV       | ✓  |  ✓  |
| Dobře známý text   | ✓  |  ✓  |

Tyto další části obsahují přehled všech různých nástrojů pro čtení a zápis prostorových dat pomocí modulu prostorového v/v.

## <a name="read-spatial-data"></a>Čtení prostorových dat

Funkce `atlas.io.read` je hlavní funkcí, která slouží ke čtení běžných formátů prostorových dat, jako jsou KML, GPX, GeoRSS, injson a CSV, s prostorovými daty. Tato funkce může také číst komprimované verze těchto formátů, jako soubor ZIP nebo soubor KMZ. Formát souboru KMZ je komprimovaná verze KML, která může také zahrnovat prostředky, jako jsou obrázky. Alternativně může funkce Read převzít adresu URL, která odkazuje na soubor v libovolném z těchto formátů. Adresy URL by se měly hostovat na koncovém bodu s povoleným CORs nebo by měla být v možnostech čtení uvedená proxy služba. Služba proxy se používá k načtení prostředků v doménách, které nejsou povolené CORs. Funkce Read vrací příslib pro přidání ikon obrázku na mapu a zpracovává asynchronně data pro minimalizaci dopadu na vlákno uživatelského rozhraní.

Při čtení komprimovaného souboru buď jako zip, nebo jako KMZ, bude vyhledán a prohledán první platný soubor. Například DOC. KML nebo soubor s jiným platným rozšířením, například:. KML,. XML, injson,. JSON,. csv,. TSV nebo. txt. Pak jsou image, na které se odkazuje v souborech KML a GeoRSS, předem načetly, aby byly dostupné. Nepřístupná data obrázku mohou načíst alternativní záložní bitovou kopii nebo bude odebrána ze stylů. Obrázky extrahované ze souborů KMZ budou převedeny na identifikátory URI dat.

Výsledek z funkce Read je objekt `SpatialDataSet`. Tento objekt rozšiřuje třídu rozhraní injson pro funkci. Dá se snadno předat `DataSource`, jako je vykreslení jeho funkcí na mapě. `SpatialDataSet` neobsahují jenom informace o funkcích, ale může zahrnovat i překryvy KML, zpracovávat metriky a další podrobnosti, jak je uvedeno v následující tabulce.

| Název vlastnosti | Typ | Popis | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | Ohraničující rámeček všech dat v datové sadě. |
| `features` | `Feature[]` | Funkce pro injson v rámci datové sady. |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | Pole KML GroundOverlays. |
| `icons` | Záznam&lt;řetězec, řetězec&gt; | Sada adres URL ikony. Klíč = název ikony, hodnota = adresa URL. |
| properties | jakýmikoli | Informace o vlastnostech poskytované na úrovni dokumentu sady prostorových dat |
| `stats` | `SpatialDataSetStats` | Statistika týkající se obsahu a času zpracování sady prostorových dat |
| `type` | `'FeatureCollection'` | Hodnota pouze pro čtení typu injson. |

## <a name="examples-of-reading-spatial-data"></a>Příklady čtení prostorových dat

Následující kód ukazuje, jak načíst jednoduchou prostorovou datovou sadu a vykreslit ji na mapě pomocí třídy `SimpleDataLayer`. Kód používá soubor GPX, na který odkazuje adresa URL.

<br/>

<iframe height='500' scrolling='no' title='Jednoduché načítání prostorových dat' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se, jak se v CodePen (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'></a> <a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>načtou Azure Maps data prostorového</a> pera.
</iframe>

Další ukázka kódu ukazuje, jak číst a načíst KML nebo KMZ na mapě. KML může obsahovat základní překryvy, které budou ve formě `ImageLyaer` nebo `OgcMapLayer`. Tyto překryvy je nutné přidat na mapě odděleně od funkcí. Kromě toho, pokud má datová sada vlastní ikony, musí být tyto ikony načteny do prostředků map předtím, než jsou funkce načteny.

<br/>

<iframe height='500' scrolling='no' title='Načíst KML na mapě' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>KML (@azuremaps) na <a href='https://codepen.io'>CodePen</a>se zobrazí v části <a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>načítat</a> pero na mapu Azure Maps (<a href='https://codepen.io/azuremaps'> </a>).
</iframe>

Volitelně můžete poskytnout proxy službu pro přístup k prostředkům mezi doménami, u kterých pravděpodobně není povolená CORs. Tento fragment kódu ukazuje, že můžete zadat proxy službu:

```javascript

//Set the location of your proxyServiceUrl file 
var proxyServiceUrl = window.location.origin + '/CorsEnabledProxyService.ashx?url=';

//Read a KML file from a URL or pass in a raw KML string.
atlas.io.read('https://s3-us-west-2.amazonaws.com/s.cdpn.io/1717245/2007SanDiegoCountyFires.kml', {
    //Provide a proxy service
    proxyService: proxyServiceUrl
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

Poslední ukázka níže ukazuje, jak číst soubor s oddělovači a jak ho vykreslit na mapě. V tomto případě kód používá soubor CSV, který obsahuje sloupce prostorových dat.

<br/>

<iframe height='500' scrolling='no' title='Přidat soubor s oddělovači' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se, jak pero <a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>přidá soubor s oddělovači</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="write-spatial-data"></a>Zápis prostorových dat

V modulu pro prostorové vstupně-výstupní operace jsou dvě hlavní funkce zápisu. Funkce `atlas.io.write` generuje řetězec, zatímco funkce `atlas.io.writeCompressed` generuje komprimovaný soubor zip. Komprimovaný soubor zip by obsahoval textový soubor s prostorovými daty. Obě tyto funkce vrací příslib pro přidání dat do souboru. Oba můžou zapisovat libovolné z následujících dat: `SpatialDataSet`, `DataSource`, `ImageLayer`, `OgcMapLayer`, kolekce funkcí, funkce, geometrie nebo pole libovolné kombinace těchto datových typů. Při psaní pomocí obou funkcí můžete zadat požadovaný formát souboru. Pokud formát souboru není zadán, budou data zapsána jako KML.

Následující nástroj ukazuje většinu možností zápisu, které lze použít s funkcí `atlas.io.write`.

<br/>

<iframe height='700' scrolling='no' title='Možnosti zápisu prostorových dat' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>V CodePen (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'></a>si prohlédněte Azure Maps <a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>Možnosti psaní prostorových dat</a> pro pero.
</iframe>

## <a name="example-of-writing-spatial-data"></a>Příklad psaní prostorových dat

Následující ukázka umožňuje přetáhnout a pak načíst prostorové soubory na mapě. Data typu injson můžete z mapy exportovat a zapsat v jednom z podporovaných formátů prostorových dat jako řetězec nebo jako komprimovaný soubor.

<br/>

<iframe height='700' scrolling='no' title='Přetažení prostorových souborů na mapě' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>přetahování prostorových souborů na mapu</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Volitelně můžete poskytnout proxy službu pro přístup k prostředkům mezi doménami, u kterých pravděpodobně není povolená CORs. Tento fragment kódu ukazuje, že můžete začlenit proxy službu:

```javascript

//Set the location of your proxyServiceUrl file 
var proxyServiceUrl = window.location.origin + '/CorsEnabledProxyService.ashx?url=';

function readData(data, fileName) {
    loadingIcon.style.display = '';
    fileCount++;
    //Attempt to parse the file and add the shapes to the map.
    atlas.io.read(data, {
        //Provide a proxy service
        proxyService: proxyServiceUrl
    }).then(
        //Success
        function(r) {
            //some code goes here ...
        }
    );
}
```

## <a name="read-and-write-well-known-text-wkt"></a>Přečíst a zapsat dobře známý text (Well)

[Dobře známý text](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) (Well) je standard pro Open GEOSPATIAL CONSORTIUM (OGC), který představuje prostorové geometrií jako text. Mnohé geoprostorové systémy podporují Well, jako je Azure SQL a Azure PostgreSQL, pomocí modulu plug-in PostGIS. Podobně jako u většiny standardů OGC se souřadnice naformátují jako zeměpisná šířka, aby se zarovnaly konvenci x y. Příkladem je, že bod v délce-110 a zeměpisná šířka 45 lze zapsat jako `POINT(-110 45)` pomocí formátu Well.

Známý text se dá přečíst pomocí funkce `atlas.io.ogc.WKT.read` a napsaný pomocí funkce `atlas.io.ogc.WKT.write`.

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>Příklady čtení a zápisu dobře známého textu (Well)

Následující kód ukazuje, jak přečíst známý textový řetězec `POINT(-122.34009 47.60995)` a vykreslit jej na mapě pomocí bublinové vrstvy.

<br/>

<iframe height='500' scrolling='no' title='Dobře známý text pro čtení' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/XWbabLd/'>dobře známý text</a> na pero pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Následující kód ukazuje čtení a zápis dobře známého textu zpátky a zpátky.

<br/>

<iframe height='700' scrolling='no' title='Přečíst a zapsat dobře známý text' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>V <a href='https://codepen.io'>CodePen</a>(<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na se podívejte na <a href='https://codepen.io/azuremaps/pen/JjdyYav/'>dobře známý Azure Maps text s psaním</a> perem.
</iframe>

## <a name="read-and-write-gml"></a>Čtení a zápis GML

GML je specifikace prostorového souboru XML, která se často používá jako rozšíření jiných specifikací XML. Data o injson můžete zapsat jako XML pomocí značek GML pomocí funkce `atlas.io.core.GmlWriter.write`. XML obsahující GML lze číst pomocí funkce `atlas.io.core.GmlReader.read`. Funkce Read má dvě možnosti:

- Možnost `isAxisOrderLonLat` – pořadí osy souřadnic "Zeměpisná šířka, délka" nebo "Zeměpisná šířka" se může v datových sadách lišit a není vždy přesně definováno. Ve výchozím nastavení čtecí modul GML čte data souřadnic jako zeměpisná šířka, zeměpisná délka, ale při nastavení této možnosti na hodnotu true se tato možnost načtou jako Zeměpisná délka, zeměpisná šířka.
- Možnost `propertyTypes` – Tato možnost je vyhledávací tabulka hodnot klíče, kde klíč je název vlastnosti v datové sadě. Hodnota je typ objektu, na který se má přetypování hodnota při analýze. Podporované hodnoty typu jsou: `string`, `number`, `boolean`a `date`. Pokud vlastnost není v tabulce vyhledávání nebo není definován typ, vlastnost bude analyzována jako řetězec.

Funkce `atlas.io.read` bude ve výchozím nastavení funkce `atlas.io.core.GmlReader.read`, když zjistí, že vstupní data jsou XML, ale data nejsou jedním z formátů XML prostorové podpory.

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [statické funkce atlas.io](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io)

> [!div class="nextstepaction"]
> [SpatialDataSet](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

> [!div class="nextstepaction"]
> [SpatialDataSetStats](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

> [!div class="nextstepaction"]
> [GmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [GmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [Atlas. IO. ogc. well – funkce](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

Další ukázky kódu pro přidání do vašich map najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Přidat vrstvu mapy OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Připojení ke službě WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Využití základních operací](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Podrobnosti o podporovaném formátu dat](spatial-io-supported-data-format-details.md)

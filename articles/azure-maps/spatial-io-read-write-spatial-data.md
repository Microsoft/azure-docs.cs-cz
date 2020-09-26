---
title: Čtení a zápis prostorových dat | Mapy Microsoft Azure
description: Naučte se číst a zapisovat data pomocí modulu pro prostorové v/v, který poskytuje Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: dd4a02ffdc062ed1940d35ca64e02a5e0a88a248
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91333320"
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

`atlas.io.read`Funkce je hlavní funkcí, která slouží ke čtení běžných formátů prostorových dat, jako jsou KML, GPX, GeoRSS, injson a CSV, s prostorovými daty. Tato funkce může také číst komprimované verze těchto formátů, jako soubor ZIP nebo soubor KMZ. Formát souboru KMZ je komprimovaná verze KML, která může také zahrnovat prostředky, jako jsou obrázky. Alternativně může funkce Read převzít adresu URL, která odkazuje na soubor v libovolném z těchto formátů. Adresy URL by se měly hostovat na koncovém bodu s povoleným CORS nebo by měla být v možnostech čtení uvedená proxy služba. Služba proxy se používá k načtení prostředků v doménách, které nejsou povolené CORS. Funkce Read vrací příslib pro přidání ikon obrázku na mapu a zpracovává asynchronně data pro minimalizaci dopadu na vlákno uživatelského rozhraní.

Při čtení komprimovaného souboru buď jako zip, nebo jako KMZ, bude vyhledán a prohledán první platný soubor. Například DOC. KML nebo soubor s jiným platným rozšířením, například:. KML,. XML, injson,. JSON,. csv,. TSV nebo. txt. Pak jsou image, na které se odkazuje v souborech KML a GeoRSS, předem načetly, aby byly dostupné. Nepřístupná data obrázku mohou načíst alternativní záložní bitovou kopii nebo bude odebrána ze stylů. Obrázky extrahované ze souborů KMZ budou převedeny na identifikátory URI dat.

Výsledek z funkce Read je `SpatialDataSet` objekt. Tento objekt rozšiřuje třídu rozhraní injson pro funkci. Dá se snadno předat `DataSource` jako, aby se vykreslily jeho funkce na mapě. `SpatialDataSet`Obsahuje nejen informace o funkci, ale může zahrnovat i překrytí KML, zpracování metrik a další podrobnosti, jak je uvedeno v následující tabulce.

| Název vlastnosti | Typ | Popis | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | Ohraničující rámeček všech dat v datové sadě. |
| `features` | `Feature[]` | Funkce pro injson v rámci datové sady. |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | Pole KML GroundOverlays. |
| `icons` | &lt;Řetězec záznamu, řetězec&gt; | Sada adres URL ikony. Klíč = název ikony, hodnota = adresa URL. |
| properties | Libovolný | Informace o vlastnostech poskytované na úrovni dokumentu sady prostorových dat |
| `stats` | `SpatialDataSetStats` | Statistika týkající se obsahu a času zpracování sady prostorových dat |
| `type` | `'FeatureCollection'` | Hodnota typu geografického zápisu jen pro čtení. |

## <a name="examples-of-reading-spatial-data"></a>Příklady čtení prostorových dat

Následující kód ukazuje, jak načíst prostorovou datovou sadu a vykreslit ji na mapě pomocí `SimpleDataLayer` třídy. Kód používá soubor GPX, na který odkazuje adresa URL.

<br/>

<iframe height='500' scrolling='no' title='Jednoduché načítání prostorových dat' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se, jak se v CodePen () na <a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>načítají data prostorů</a> pro pero Azure Maps jednoduché <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'>CodePen</a>
</iframe>

Další ukázka kódu ukazuje, jak číst a načíst KML nebo KMZ na mapě. KML může obsahovat základní překryvy, které budou ve formě `ImageLyaer` nebo `OgcMapLayer` . Tyto překryvy je nutné přidat na mapě odděleně od funkcí. Kromě toho, pokud má datová sada vlastní ikony, musí být tyto ikony načteny do prostředků map předtím, než jsou funkce načteny.

<br/>

<iframe height='500' scrolling='no' title='Načíst KML na mapě' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se, jak se <a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>KML pero načítají na mapu</a> podle Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Volitelně můžete poskytnout proxy službu pro přístup k prostředkům mezi doménami, u kterých pravděpodobně není povolená CORS. Funkce čtení se pokusí získat přístup k souborům v jiné doméně, a to nejprve pomocí CORS. Po prvním pokusu o přístup k jakémukoli prostředku v jiné doméně pomocí CORS bude vyžadován pouze další soubor, pokud byla poskytnuta proxy služba. Funkce Read připojí adresu URL souboru ke konci poskytnuté adresy URL proxy serveru. Tento fragment kódu ukazuje, jak předat proxy službu do funkce Read:

```javascript
//Read a file from a URL or pass in a raw data as a string.
atlas.io.read('https://nonCorsDomain.example.com/mySuperCoolData.xml', {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

Níže uvedená ukázka ukazuje, jak si přečíst soubor s oddělovači a jak ho vykreslit na mapě. V tomto případě kód používá soubor CSV, který obsahuje sloupce prostorových dat.

<br/>

<iframe height='500' scrolling='no' title='Přidat soubor s oddělovači' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se, jak pero <a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>přidá soubor s oddělovači</a> pomocí Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="write-spatial-data"></a>Zápis prostorových dat

V modulu pro prostorové vstupně-výstupní operace jsou dvě hlavní funkce zápisu. `atlas.io.write`Funkce vygeneruje řetězec, zatímco `atlas.io.writeCompressed` funkce vygeneruje komprimovaný soubor zip. Komprimovaný soubor zip by obsahoval textový soubor s prostorovými daty. Obě tyto funkce vrací příslib pro přidání dat do souboru. A oba můžou zapisovat následující data: `SpatialDataSet` , `DataSource` , `ImageLayer` , `OgcMapLayer` , kolekce funkcí, funkce, geometrie nebo pole libovolné kombinace těchto datových typů. Při psaní pomocí obou funkcí můžete zadat požadovaný formát souboru. Pokud formát souboru není zadán, budou data zapsána jako KML.

Následující nástroj ukazuje většinu možností zápisu, které lze použít s `atlas.io.write` funkcí.

<br/>

<iframe height='700' scrolling='no' title='Možnosti zápisu prostorových dat' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>Možnosti psaní prostorových dat</a> pro pero pomocí Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="example-of-writing-spatial-data"></a>Příklad psaní prostorových dat

Následující ukázka umožňuje přetáhnout a pak načíst prostorové soubory na mapě. Data typu injson můžete z mapy exportovat a zapsat v jednom z podporovaných formátů prostorových dat jako řetězec nebo jako komprimovaný soubor.

<br/>

<iframe height='700' scrolling='no' title='Přetažení prostorových souborů na mapě' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>přetahování prostorových souborů na mapu</a> pomocí Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Volitelně můžete poskytnout proxy službu pro přístup k prostředkům mezi doménami, u kterých pravděpodobně není povolená CORS. Tento fragment kódu ukazuje, že můžete začlenit proxy službu:

```javascript
atlas.io.read(data, {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(
    //Success
    function(r) {
        //some code goes here ...
    }
);
```

## <a name="read-and-write-well-known-text-wkt"></a>Přečíst a zapsat dobře známý text (Well)

[Dobře známý text](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) (Well) je standard pro Open GEOSPATIAL CONSORTIUM (OGC), který představuje prostorové geometrií jako text. Mnohé geoprostorové systémy podporují Well, jako je Azure SQL a Azure PostgreSQL, pomocí modulu plug-in PostGIS. Podobně jako u většiny standardů OGC se souřadnice naformátují jako zeměpisná šířka, aby se zarovnaly konvenci x y. Příkladem je, že bod v délce-110 a zeměpisná šířka 45 lze zapsat jako `POINT(-110 45)` použití formátu Well.

Známý text lze číst pomocí `atlas.io.ogc.WKT.read` funkce a napsaný pomocí `atlas.io.ogc.WKT.write` funkce.

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>Příklady pro čtení a zápis dobře známého textu (Well)

Následující kód ukazuje, jak přečíst známý textový řetězec `POINT(-122.34009 47.60995)` a vykreslit jej na mapě pomocí bublinové vrstvy.

<br/>

<iframe height='500' scrolling='no' title='Dobře známý text pro čtení' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/XWbabLd/'>dobře známý text</a> pomocí pera Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Následující kód ukazuje čtení a zápis dobře známého textu zpátky a zpátky.

<br/>

<iframe height='700' scrolling='no' title='Přečíst a zapsat dobře známý text' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/JjdyYav/'>text</a> , který je ve správném Azure Maps textu, a <a href='https://codepen.io/azuremaps'>@azuremaps</a> na <a href='https://codepen.io'>CodePen</a>() na.
</iframe>

## <a name="read-and-write-gml"></a>Čtení a zápis GML

GML je specifikace prostorového souboru XML, která se často používá jako rozšíření jiných specifikací XML. Data o injson se dají zapsat jako XML pomocí značek GML pomocí `atlas.io.core.GmlWriter.write` funkce. XML obsahující GML lze číst pomocí `atlas.io.core.GmlReader.read` funkce. Funkce Read má dvě možnosti:

- `isAxisOrderLonLat`Možnost – pořadí osy souřadnic "Zeměpisná šířka, délka" nebo "Zeměpisná šířka" se může v datových sadách lišit a není vždy správně definovaná. Ve výchozím nastavení čtecí modul GML čte data souřadnic jako zeměpisná šířka, zeměpisná délka, ale při nastavení této možnosti na hodnotu true se tato možnost načtou jako Zeměpisná délka, zeměpisná šířka.
- `propertyTypes`Možnost – tato možnost je vyhledávací tabulka hodnot klíče, kde klíč je název vlastnosti v datové sadě. Hodnota je typ objektu, na který se má přetypování hodnota při analýze. Podporované hodnoty typu jsou: `string` , `number` , a `boolean`  `date` . Pokud vlastnost není ve vyhledávací tabulce nebo není definován typ, bude vlastnost analyzována jako řetězec.

`atlas.io.read`Funkce bude ve výchozím nastavení `atlas.io.core.GmlReader.read` fungovat, když zjistí, že vstupní data jsou XML, ale data nejsou jedním z formátů prostorového formátu XML.

`GmlReader`Provede analýzu souřadnic, které mají jednu z následujících identifikátory SRID:

- EPSG: 4326 (upřednostňovaný)
- EPSG: 4269, EPSG: 4283, EPSG: 4258, EPSG: 4308, EPSG: 4230, EPSG: 4272, EPSG: 4271, EPSG: 4267, EPSG: 4608, EPSG: 4674, případně s malým okrajem chyby.
- EPSG: 3857, EPSG: 102100, EPSG: 3785, EPSG: 900913, EPSG: 102113, EPSG: 41001, EPSG: 54004

## <a name="more-resources"></a>Další zdroje informací

Další informace o třídách a metodách, které se používají v tomto článku:

[statické funkce atlas.io](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io)

[SpatialDataSet](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

[SpatialDataSetStats](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

[GmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader)

[GmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter)

[Atlas. IO. ogc. well – funkce](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

[Připojení ke službě WFS](spatial-io-connect-wfs-service.md)

[Využití základních operací](spatial-io-core-operations.md)

[Podrobnosti o podporovaných formátech dat](spatial-io-supported-data-format-details.md)


## <a name="next-steps"></a>Další kroky

Další ukázky kódu pro přidání do vašich map najdete v následujících článcích:

[Přidání vrstvy mapy OGC](spatial-io-add-ogc-map-layer.md)
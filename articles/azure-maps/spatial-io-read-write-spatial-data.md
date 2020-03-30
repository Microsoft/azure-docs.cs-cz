---
title: Čtení a zápis prostorových dat | Mapy Microsoft Azure
description: Naučte se číst a zapisovat data pomocí modulu Prostorové VI poskytované azure maps web sdk.
author: philmea
ms.author: philmea
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4c47335689401ebce98224992c74c3396821a1dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334155"
---
# <a name="read-and-write-spatial-data"></a>Čtení a zápis prostorových dat

V následující tabulce jsou uvedeny formáty prostorových souborů, které jsou podporovány pro operace čtení a zápisu pomocí modulu Prostorové vi.

| Formát dat       | Čtení | Zápis |
|-------------------|------|-------|
| GeoJSON           | ✓  |  ✓  |
| GeoRSS            | ✓  |  ✓  |
| Gml               | ✓  |  ✓  |
| Gpx               | ✓  |  ✓  |
| Kml               | ✓  |  ✓  |
| Kmz               | ✓  |  ✓  |
| Prostorový CSV       | ✓  |  ✓  |
| Známý text   | ✓  |  ✓  |

Tyto následující části popisují všechny různé nástroje pro čtení a zápis prostorových dat pomocí modulu Prostorové VO.

## <a name="read-spatial-data"></a>Čtení prostorových dat

Tato `atlas.io.read` funkce je hlavní funkcí používanou ke čtení běžných formátů prostorových dat, jako jsou soubory KML, GPX, GeoRSS, GeoJSON a CSV s prostorovými daty. Tato funkce může také číst komprimované verze těchto formátů, jako soubor zip nebo soubor KMZ. Formát souboru KMZ je komprimovaná verze KML, která může také obsahovat datové zdroje, jako jsou obrázky. Funkce čtení může také převzít adresu URL, která odkazuje na soubor v některém z těchto formátů. Adresy URL by měly být hostovány na koncovém bodu s povoleným CORS nebo by měla být v možnostech čtení poskytována služba proxy. Služba proxy se používá k načtení prostředků v doménách, které nejsou povoleny CORS. Funkce čtení vrátí slib přidat ikony obrázků do mapy a zpracovává data asynchronně minimalizovat dopad na vlákno uživatelského rozhraní.

Při čtení komprimovaného souboru, buď jako zip nebo KMZ, bude rozbalen a naskenován pro první platný soubor. Například doc.kml nebo soubor s jinou platnou příponou, například: .kml, XML, geojson, .json, .csv, .tsv nebo .txt. Obrázky odkazované v souborech KML a GeoRSS jsou pak předinstalovány, aby byly přístupné. Nepřístupná obrazová data mohou načíst alternativní záložní obrázek nebo budou odebrána ze stylů. Obrázky extrahované ze souborů KMZ budou převedeny na identifikátory URI dat.

Výsledek funkce čtení je `SpatialDataSet` objekt. Tento objekt rozšiřuje třídu GeoJSON FeatureCollection. To může být snadno `DataSource` předándo as-je vykreslit jeho funkce na mapě. Obsahuje `SpatialDataSet` nejen informace o funkcích, ale může také obsahovat překryvy terénu KML, metriky zpracování a další podrobnosti, jak je uvedeno v následující tabulce.

| Název vlastnosti | Typ | Popis | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | Ohraničovací rámeček všech dat v sadě dat. |
| `features` | `Feature[]` | Funkce GeoJSON v rámci datové sady. |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | Pole KML GroundOverlays. |
| `icons` | Řetězec&lt;záznamu, řetězec&gt; | Sada adres URL ikon. Klíč = název ikony, Hodnota = URL. |
| properties | jakékoli | Informace o vlastnostech poskytnuté na úrovni dokumentu sady prostorových dat. |
| `stats` | `SpatialDataSetStats` | Statistiky o obsahu a době zpracování souboru prostorových dat. |
| `type` | `'FeatureCollection'` | Hodnota typu GeoJSON jen pro čtení. |

## <a name="examples-of-reading-spatial-data"></a>Příklady čtení prostorových dat

Následující kód ukazuje, jak číst sadu prostorových dat a `SimpleDataLayer` vykreslit ji na mapě pomocí třídy. Kód používá soubor GPX, na který odkazuje adresa URL.

<br/>

<iframe height='500' scrolling='no' title='Snadné načítání prostorových dat' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>zatížení prostorová data jednoduché</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Další ukázka kódu ukazuje, jak číst a načítat KML, nebo KMZ, na mapu. KML může obsahovat pozemní překryvy, které `ImageLyaer` `OgcMapLayer`budou ve formě nebo . Tyto překryvy musí být přidány na mapě odděleně od funkcí. Navíc pokud sada dat obsahuje vlastní ikony, tyto ikony je třeba načíst do prostředků mapy před načtením funkcí.

<br/>

<iframe height='500' scrolling='no' title='Načíst KML na mapu' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>Zatížení KML na mapě</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Volitelně můžete poskytnout službu proxy pro přístup k prostředkům mezi doménami, které nemusí mít povoleno cors. Funkce čtení se pokusí nejprve získat přístup k souborům v jiné doméně pomocí corsu. Po prvním selhání přístupu k libovolnému prostředku v jiné doméně pomocí CORS bude požadovat další soubory pouze v případě, že byla poskytnuta služba proxy. Funkce čtení připojí adresu URL souboru na konec adresy URL proxy. Tento úryvek kódu ukazuje, jak předat službu proxy do funkce čtení:

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

Ukázka níže ukazuje, jak číst oddělený soubor a vykreslit jej na mapě. V tomto případě kód používá soubor CSV, který má sloupce prostorových dat.

<br/>

<iframe height='500' scrolling='no' title='Přidání souboru s oddělovačem' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero Přidat soubor<a href='https://codepen.io/azuremaps'>@azuremaps</a>s <a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>oddělovačem</a> podle Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="write-spatial-data"></a>Zápis prostorových dat

V prostorovém iO modulu jsou dvě hlavní funkce zápisu. Funkce `atlas.io.write` generuje řetězec, zatímco `atlas.io.writeCompressed` funkce generuje komprimovaný soubor ZIP. Komprimovaný soubor zip by obsahoval textový soubor s prostorovými daty v něm. Obě tyto funkce vrátí příslib přidání dat do souboru. A oba mohou zapsat libovolný z `SpatialDataSet` `DataSource`následujících dat: , , `ImageLayer`, `OgcMapLayer`kolekce prvků, prvek, geometrie nebo pole libovolné kombinace těchto datových typů. Při psaní pomocí obou funkcí můžete určit hledaný formát souboru. Pokud není zadán formát souboru, budou data zapsána jako KML.

Níže uvedený nástroj ukazuje většinu možností zápisu, které `atlas.io.write` lze použít s funkcí.

<br/>

<iframe height='700' scrolling='no' title='Možnosti zápisu prostorových dat' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>možnosti zápisu prostorových dat</a> pera podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="example-of-writing-spatial-data"></a>Příklad zápisu prostorových dat

Následující ukázka umožňuje přetahovat a potom načíst prostorové soubory na mapě. Data GeoJSON můžete exportovat z mapy a zapsat je do jednoho z podporovaných formátů prostorových dat jako řetězec nebo jako komprimovaný soubor.

<br/>

<iframe height='700' scrolling='no' title='Přetažení prostorových souborů na mapu' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>přetáhnout prostorové</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>soubory na mapě pomocí Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Volitelně můžete poskytnout službu proxy pro přístup k prostředkům mezi doménami, které nemusí mít povoleno cors. Tento úryvek kódu ukazuje, že byste mohli začlenit proxy službu:

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

## <a name="read-and-write-well-known-text-wkt"></a>Čtení a psaní známého textu (WKT)

[Známý text](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) (WKT) je standard Open Geospatial Consortium (OGC) pro reprezentaci prostorových geometrií jako textu. Mnoho geoprostorových systémů podporuje WKT, jako je Azure SQL a Azure PostgreSQL pomocí modulu plug-in PostGIS. Stejně jako většina standardů OGC jsou souřadnice formátovány jako "zeměpisná šířka", aby byly v souladu s konvencí "x y". Jako příklad lze bod v zeměpisné šířce -110 a zeměpisné `POINT(-110 45)` šířky 45 zapsat jako pomocí formátu WKT.

Známý text lze číst pomocí `atlas.io.ogc.WKT.read` funkce a psát `atlas.io.ogc.WKT.write` pomocí funkce.

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>Příklady čtení a psaní dobře známého textu (WKT)

Následující kód ukazuje, jak číst známý `POINT(-122.34009 47.60995)` textový řetězec a vykreslit jej na mapě pomocí bublinové vrstvy.

<br/>

<iframe height='500' scrolling='no' title='Přečtěte si známý text' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/XWbabLd/'>Přečtěte</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>si známý text podle Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Následující kód ukazuje čtení a zápis známý text tam a zpět.

<br/>

<iframe height='700' scrolling='no' title='Čtení a psaní známého textu' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero číst a psát<a href='https://codepen.io/azuremaps'>@azuremaps</a>známý <a href='https://codepen.io/azuremaps/pen/JjdyYav/'>text</a> podle Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="read-and-write-gml"></a>Čtení a zápis GML

GML je specifikace souboru prostorového XML, která se často používá jako přípona k jiným specifikacím XML. GeoJSON data mohou být zapsána jako `atlas.io.core.GmlWriter.write` XML s GML tagy pomocí funkce. Xml, který obsahuje GML lze `atlas.io.core.GmlReader.read` číst pomocí funkce. Funkce čtení má dvě možnosti:

- Volba `isAxisOrderLonLat` - Pořadí os souřadnic "zeměpisná šířka, zeměpisná délka" nebo "zeměpisná délka, zeměpisná šířka" se může v jednotlivých datových sadách lišit a není vždy dobře definováno. Ve výchozím nastavení čte čtečka GML data souřadnic jako "zeměpisná šířka, délka", ale nastavení této možnosti na true bude znít jako "zeměpisná délka, zeměpisná šířka".
- Možnost `propertyTypes` - Tato možnost je vyhledávací tabulka hodnoty klíče, kde klíč je název vlastnosti v sadě dat. Hodnota je typ objektu přetypovat hodnotu při analýzě. Podporované hodnoty typu `string`jsou: `boolean`, `date` `number`, a . Pokud vlastnost není ve vyhledávací tabulce nebo typ není definován, vlastnost bude analyzována jako řetězec.

Funkce `atlas.io.read` bude ve `atlas.io.core.GmlReader.read` výchozím nastavení funkce, když zjistí, že vstupní data jsou XML, ale data nejsou jedním z dalších podpůrných formátů prostorového XML.

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách použitých v tomto článku:

> [!div class="nextstepaction"]
> [atlas.io statické funkce](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io)

> [!div class="nextstepaction"]
> [Sada prostorových dat](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

> [!div class="nextstepaction"]
> [SpatialDataSetStats](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

> [!div class="nextstepaction"]
> [GmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [GmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [funkce atlas.io.ogc.WKT](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

Další ukázky kódu, které chcete přidat do map, naleznete v následujících článcích:

> [!div class="nextstepaction"]
> [Přidání mapové vrstvy OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Připojení ke službě WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Využití základních operací](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Podrobnosti o podporovaném formátu dat](spatial-io-supported-data-format-details.md)

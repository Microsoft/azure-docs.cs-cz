---
title: Vytvoření zdroje dat pro mapu | Mapy Microsoft Azure
description: 'Zjistěte, jak vytvořit zdroj dat pro mapu. Přečtěte si o zdrojích dat, které používá Azure Maps Web SDK: mikrojsonické zdroje a vektorové dlaždice.'
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 75d2833a5b270fcfdcffa668ec0e308399edab8a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91311446"
---
# <a name="create-a-data-source"></a>Vytvoření zdroje dat

Sada Azure Maps Web SDK ukládá data do zdrojů dat. Použití zdrojů dat optimalizuje datové operace pro dotazování a vykreslování. V současné době existují dva typy zdrojů dat:

- Geografická **zdrojová**data: Správa nezpracovaných dat umístění ve formátu geografických JSON místně. Vhodný pro malé až střední datové sady (nahoru z stovek tisíců tvarů).
- **Zdroj vektorové dlaždice**: načte data formátovaná jako vektorové dlaždice pro aktuální zobrazení mapy na základě systému mapy dláždění. Ideální pro velké až obrovský datové sady (miliony nebo miliardy tvarů).

## <a name="geojson-data-source"></a>Zdroj dat pro injson

Načtení zdroje dat založeného na bázi standardu JSON a uložení dat místně pomocí `DataSource` třídy. Data typu injson lze ručně vytvořit nebo vytvořit pomocí tříd pomocníka v oboru názvů [Atlas. data](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) . `DataSource`Třída poskytuje funkce pro import místních nebo vzdálených souborů injson. Vzdálené soubory typu injson musí být hostované na koncovém bodu s povoleným CORs. `DataSource`Třída poskytuje funkce pro data bodu clusteringu. A data lze snadno přidat, odebrat a aktualizovat pomocí `DataSource` třídy. Následující kód ukazuje, jak lze vytvořit data v Azure Maps.

```javascript
//Create raw GeoJSON object.
var rawGeoJson = {
     "type": "Feature",
     "geometry": {
         "type": "Point",
         "coordinates": [-100, 45]
     },
     "properties": {
         "custom-property": "value"
     }
};

//Create GeoJSON using helper classes (less error prone and less typing).
var geoJsonClass = new atlas.data.Feature(new atlas.data.Point([-100, 45]), {
    "custom-property": "value"
}); 
```

Po vytvoření se zdroje dat dají přidat do mapy prostřednictvím `map.sources` vlastnosti, která je [SourceManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager). Následující kód ukazuje, jak vytvořit `DataSource` a přidat ho do mapy.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

Následující kód ukazuje různé způsoby, jak mohou být data typu injson přidána do `DataSource` .

```javascript
//GeoJsonData in the following code can be a single or array of GeoJSON features or geometries, a GeoJSON feature colleciton, or a single or array of atlas.Shape objects.

//Add geoJSON object to data source. 
dataSource.add(geoJsonData);

//Load geoJSON data from URL. URL should be on a CORs enabled endpoint.
dataSource.importDataFromUrl(geoJsonUrl);

//Overwrite all data in data source.
dataSource.setShapes(geoJsonData);
```

> [!TIP]
> Řekněme, že chcete přepsat všechna data v `DataSource` . Pokud provedete volání `clear` funkcí then `add` , mapa může znovu vykreslovat dvakrát, což může způsobit trochu zpoždění. Místo toho použijte `setShapes` funkci, která odebere a nahradí všechna data ve zdroji dat a aktivuje pouze jedno opakované vykreslování mapy.

## <a name="vector-tile-source"></a>Zdroj vektorové dlaždice

Zdroj vektorové dlaždice popisuje, jak přistupovat k vrstvě vektorové dlaždice. Použijte třídu [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) k vytvoření instance zdroje vektorové dlaždice. Vrstvy vektorové dlaždice jsou podobné vrstvám dlaždic, ale nejsou stejné. Vrstva dlaždice je rastrový obrázek. Vrstvy vektorové dlaždice jsou komprimovaný soubor ve formátu **PBF** . Tento komprimovaný soubor obsahuje data vektorové mapy a jednu nebo více vrstev. Soubor lze vykreslit a stylovat na straně klienta na základě stylu jednotlivých vrstev. Data ve vektorové dlaždici obsahují geografické funkce ve formě bodů, čar a mnohoúhelníků. Je několik výhod používání vrstev vektorových dlaždic namísto vrstev rastrových dlaždic:

 - Velikost souboru vektorové dlaždice je obvykle mnohem menší než ekvivalentní rastrová dlaždice. V takovém případě se používá menší šířka pásma. Znamená nižší latenci, rychlejší mapu a lepší uživatelské prostředí.
 - Vzhledem k tomu, že se na klientovi vykreslují vektorové dlaždice, přizpůsobuje se rozlišení zařízení, ve kterém se zobrazují. Výsledkem je, že vykreslené mapy jsou lépe definovány a s jasnými popisky.
 - Změna stylu dat ve vektorových mapách nepotřebuje stahovat data znovu, protože nový styl lze použít na straně klienta. Naopak změna stylu vrstvy rastrového dlaždice obvykle vyžaduje načtení dlaždic ze serveru a následné použití nového stylu.
 - Vzhledem k tomu, že data jsou doručena v vektorovém formátu, je pro přípravu dat vyžadováno méně zpracování na straně serveru. V důsledku toho je možné novější data zpřístupnit rychleji.

Azure Maps dodržuje [specifikaci vektorové dlaždice Mapbox](https://github.com/mapbox/vector-tile-spec), což je otevřený standard. Azure Maps poskytuje následující služby vektorových dlaždic jako součást platformy:

- [documentation](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview)  |  [Podrobnosti o formátu dat](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile) v dokumentaci k částem silničních bloků
- [documentation](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficincidenttile)  |  [Podrobnosti o formátu dat](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles) v dokumentaci k incidentům přenosu
- [documentation](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficflowtile)  |  [Podrobnosti o formátu dat](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles) v dokumentaci toku provozu
- Azure Maps Creator taky umožňuje vytvořit vlastní vektorové dlaždice a získat přístup přes [vykreslování dlaždice získat 2](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview) .

> [!TIP]
> Když použijete dlaždice vektorového nebo rastrového obrázku ze služby Azure Maps Renderer s webovou sadou SDK, můžete nahradit `atlas.microsoft.com` zástupným symbolem `{azMapsDomain}` . Tento zástupný symbol bude nahrazen stejnou doménou, kterou používá mapa, a automaticky bude automaticky připojovat stejné podrobnosti ověřování. To významně zjednodušuje ověřování pomocí služby vykreslování při použití Azure Active Directory ověřování.

Chcete-li zobrazit data ze zdroje vektorové dlaždice na mapě, připojte zdroj k jedné z vrstev vykreslování dat. Všechny vrstvy, které používají zdroj vektorů, musí `sourceLayer` v možnostech určovat hodnotu. Následující kód načte službu Azure Maps Vector Flow Vector dlaždice jako zdroj vektorové dlaždice a pak ji zobrazí na mapě pomocí spojnicové vrstvy. Tento zdroj dlaždice vektoru má jednu sadu dat ve zdrojové vrstvě s názvem "přenosový tok". Řádková data v této datové sadě obsahují vlastnost s názvem `traffic_level` , která se používá v tomto kódu k výběru barvy a škálování velikosti řádků.

```javascript
//Create a vector tile source and add it to the map.
var datasource = new atlas.source.VectorTileSource(null, {
    tiles: ['https://{azMapsDomain}/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}'],
    maxZoom: 22
});
map.sources.add(datasource);

//Create a layer for traffic flow lines.
var flowLayer = new atlas.layer.LineLayer(datasource, null, {
    //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer: 'Traffic flow',

    //Color the roads based on the traffic_level property. 
    strokeColor: [
        'interpolate',
        ['linear'],
        ['get', 'traffic_level'],
        0, 'red',
        0.33, 'orange',
        0.66, 'green'
    ],

    //Scale the width of roads based on the traffic_level property. 
    strokeWidth: [
        'interpolate',
        ['linear'],
        ['get', 'traffic_level'],
        0, 6,
        1, 1
    ]
});

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(flowLayer, 'labels');
```

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vrstva čáry vektorové dlaždice" src="https://codepen.io/azuremaps/embed/wvMXJYJ?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
V CodePen () na se podívejte na <a href='https://codepen.io/azuremaps/pen/wvMXJYJ'>dlaždici Azure Maps čáry dlaždice pera Vector</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="connecting-a-data-source-to-a-layer"></a>Připojení zdroje dat ke vrstvě

Data se vykreslují na mapě pomocí vrstev vykreslování. Na jeden zdroj dat může odkazovat jedna nebo více vrstev vykreslování. Následující vrstvy vykreslování vyžadují zdroj dat:

- [Bublinová vrstva](map-add-bubble-layer.md) – vykreslí data bodu jako kružnice škálované na mapě.
- [Symbolová vrstva](map-add-pin.md) – vykreslí data bodu jako ikony nebo text.
- [Vrstva Heat mapy](map-add-heat-map-layer.md) – vykreslí data bodu jako Heat mapu hustoty.
- [Spojnicová vrstva](map-add-shape.md) – vykreslí čáru a nebo vykreslí obrys mnohoúhelníků. 
- [Mnohoúhelníková vrstva](map-add-shape.md) – vyplní oblast mnohoúhelníku plnou barvou nebo vzorkem obrázku.

Následující kód ukazuje, jak vytvořit zdroj dat, přidat ho do mapy a připojit ho k bublinové vrstvě. A pak importujte data bodu geografického JSON ze vzdáleného umístění do zdroje dat. 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Existují další vrstvy vykreslování, které se k těmto zdrojům dat nepřipojují, ale přímo načítají data pro vykreslování. 

- [Vrstva obrázku](map-add-image-layer.md) – překrývá jeden obrázek nad mapou a vytvoří vazby jeho rohů k sadě zadaných souřadnic.
- [Vrstva dlaždice](map-add-tile-layer.md) – nadmnožinou vrstvu rastrového dlaždice nad mapou.

## <a name="one-data-source-with-multiple-layers"></a>Jeden zdroj dat s více vrstvami

K jednomu zdroji dat lze připojit více vrstev. Existuje mnoho různých scénářů, ve kterých je tato možnost užitečná. Zvažte například scénář, ve kterém uživatel nakreslí mnohoúhelník. Měli byste vykreslit a vyplnit mnohoúhelníkovou oblast, protože uživatel přidá body na mapu. Přidáním čáry se styly pro obrys mnohoúhelníku se usnadní zobrazení hran mnohoúhelníku, když uživatel kreslí. Abychom mohli pohodlně upravovat jednotlivé pozice v mnohoúhelníku, můžeme přidat popisovač, jako je kód PIN nebo značka, nad každou pozici.

![Mapování znázorňující více vrstev vykreslování dat z jednoho zdroje dat](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

Ve většině platforem mapování byste potřebovali objekt mnohoúhelníku, objekt čáry a kód PIN pro každou pozici v mnohoúhelníku. Když se mnohoúhelník změní, budete muset ručně aktualizovat čáru a PIN kódy, které se můžou rychle stát složitou.

V Azure Maps je vše, co potřebujete, jediným mnohoúhelníkem ve zdroji dat, jak je znázorněno v následujícím kódu.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a polygon and add it to the data source.
dataSource.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(dataSource, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(dataSource, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(dataSource, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     outlineColor: 'white',
     outlineWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [Datového](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource)

> [!div class="nextstepaction"]
> [DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions)

> [!div class="nextstepaction"]
> [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions)

Další ukázky kódu pro přidání do vašich map najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Přidání místního okna](map-add-popup.md)

> [!div class="nextstepaction"]
> [Použití výrazů pro styly založené na datech](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy symbolů](map-add-pin.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy bublin](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Přidání řádkové vrstvy](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Přidání mnohoúhelníkové vrstvy](map-add-shape.md)

> [!div class="nextstepaction"]
> [Přidat Heat mapu](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Ukázky kódu](https://docs.microsoft.com/samples/browse/?products=azure-maps)
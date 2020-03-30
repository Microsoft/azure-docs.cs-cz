---
title: Vytvoření zdroje dat pro mapu | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak vytvořit zdroj dat a přidat ho do mapy pomocí sady Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: 1675d63fd3a65beda46042f4a78535bb4e066e62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190234"
---
# <a name="create-a-data-source"></a>Vytvoření zdroje dat

Sada Azure Maps Web SDK ukládá data do zdrojů dat. Použití zdrojů dat optimalizuje operace dat pro dotazování a vykreslování. V současné době existují dva typy zdrojů dat:

**Zdroj dat GeoJSON**

Zdroj dat založený na GeoJSON načítají `DataSource` a ukládají data místně pomocí třídy. Data GeoJSON lze vytvořit ručně nebo vytvořit pomocí pomocné třídy v oboru názvů [atlas.data.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) Třída `DataSource` poskytuje funkce pro import místních nebo vzdálených souborů GeoJSON. Vzdálené soubory GeoJSON musí být hostovány v koncovém bodě s povolenými funkcemi CORs. Třída `DataSource` poskytuje funkce pro data bodů clustering. A data lze snadno přidat, odebrat `DataSource` a aktualizovat s třídou.


> [!TIP]
> Řekněme, že chcete přepsat všechna `DataSource`data v . Pokud budete volat `clear` pak `add` funkce, mapa může re-render dvakrát, což může způsobit trochu zpoždění. Místo toho `setShapes` použijte funkci, která odstraní a nahradí všechna data ve zdroji dat a spustí pouze jedno opětovné vykreslení mapy.

**Zdroj vektorové dlaždice**

Zdroj vektorové dlaždice popisuje, jak získat přístup k vrstvě vektorové dlaždice. Pomocí třídy [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) můžete vytvořit konkretizovat zdroj vektorové dlaždice. Vrstvy vektorových dlaždic jsou podobné vrstvám dlaždic, ale nejsou stejné. Vrstva dlaždic je rastrový obraz. Vrstvy vektorových dlaždic jsou komprimovaný soubor ve formátu PBF. Tento komprimovaný soubor obsahuje vektorová mapová data a jednu nebo více vrstev. Soubor může být vykreslen a stylizován na straně klienta na základě stylu každé vrstvy. Data ve vektorové dlaždici obsahují geografické prvky ve formě bodů, čar a polygonů. Použití vrstv y vektorových dlaždic namísto vrstev rastrových dlaždic má několik výhod:

 - Velikost souboru vektorové dlaždice je obvykle mnohem menší než ekvivalentní rastrová dlaždice. Jako takové se používá menší šířka pásma. To znamená nižší latenci, rychlejší mapu a lepší uživatelské prostředí.
 - Vzhledem k tomu, že vektorové dlaždice jsou vykresleny na straně klienta, přizpůsobí se rozlišení zařízení, na které se zobrazují. Výsledkem je, že vykreslené mapy vypadají přesněji, s křišťálově čistými popisky.
 - Změna stylu dat ve vektorových mapách nevyžaduje opětovné stažení dat, protože nový styl lze použít na klienta. Naproti tomu změna stylu vrstvy rastrových dlaždic obvykle vyžaduje načtení dlaždic ze serveru a následné použití nového stylu.
 - Vzhledem k tomu, že data jsou doručována ve vektorové podobě, je k přípravě dat nutné méně zpracování na straně serveru. V důsledku toho mohou být novější data k dispozici rychleji.

Všechny hladiny, které používají `sourceLayer` vektorový zdroj, musí určit hodnotu.

Po vytvoření zdroje dat lze přidat do `map.sources` mapy prostřednictvím vlastnosti, která je [SourceManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager). Následující kód ukazuje, jak `DataSource` vytvořit a přidat do mapy.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

Azure Maps dodržuje [specifikace Mapbox vektorové dlaždice](https://github.com/mapbox/vector-tile-spec), otevřený standard.

## <a name="connecting-a-data-source-to-a-layer"></a>Připojení zdroje dat k vrstvě

Data jsou vykreslena na mapě pomocí vykreslovacích vrstev. Jeden zdroj dat může být odkazován jednou nebo více vrstvami vykreslování. Následující vrstvy vykreslování vyžadují zdroj dat:

- [Bublinová vrstva](map-add-bubble-layer.md) - vykresluje bodová data jako kruhy s měřítkem na mapě.
- [Vrstva symbolů](map-add-pin.md) - vykresluje data bodů jako ikony nebo text.
- [Vrstva tepelné mapy](map-add-heat-map-layer.md) - vykresluje bodová data jako teplotní mapu hustoty.
- [Řádková vrstva](map-add-shape.md) - vykreslování čáry a nebo vykreslení obrysu polygonů. 
- [Vrstva polygonu](map-add-shape.md) - vyplní oblast polyga plnou barvou nebo vzorem obrazu.

Následující kód ukazuje, jak vytvořit zdroj dat, přidat ho do mapy a připojit ho k bublinové vrstvě. A pak importujte data bodů GeoJSON ze vzdáleného umístění do zdroje dat. 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Existují další vrstvy vykreslování, které se nepřipojují k těmto zdrojům dat, ale přímo načítají data pro vykreslování. 

- [Vrstva obrazu](map-add-image-layer.md) - překryje jeden obraz v horní části mapy a sváže jeho rohy se sadou určených souřadnic.
- [Vrstva dlaždic](map-add-tile-layer.md) - překrývá rastrovou vrstvu dlaždic v horní části mapy.

## <a name="one-data-source-with-multiple-layers"></a>Jeden zdroj dat s více vrstvami

K jednomu zdroji dat lze připojit více vrstev. Existuje mnoho různých scénářů, ve kterých je tato možnost užitečná. Zvažte například scénář, ve kterém uživatel nakreslí mnohonožce. Měli bychom vykreslit a vyplnit oblast polygu, jak uživatel přidá body do mapy. Přidání stylizované čáry k obrysu mnohonožku usnadňuje zobrazení okrajů mnohonožku při natahování uživatele. Chcete-li pohodlně upravit jednotlivou pozici v polygu, můžeme nad každou pozici přidat rukojeť, jako je kolík nebo značka.

![Mapa zobrazující více vrstev vykreslování dat z jednoho zdroje dat](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

Ve většině mapovacích platforem byste potřebovali objekt polygonu, objekt čáry a špendlík pro každou pozici v polygu. Vzhledem k tomu, že polygon je upraven, budete muset ručně aktualizovat linku a kolíky, které se mohou rychle stát složitými.

S Azure Maps, vše, co potřebujete, je jeden polygon ve zdroji dat, jak je znázorněno v kódu níže.

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

Další informace o třídách a metodách použitých v tomto článku:

> [!div class="nextstepaction"]
> [Datasource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [Možnosti zdroje dat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [Zdroj vektoru](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [Možnosti VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions?view=azure-maps-typescript-latest)

Další ukázky kódu, které chcete přidat do map, naleznete v následujících článcích:

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
> [Přidání tepelné mapy](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Ukázky kódu](https://docs.microsoft.com/samples/browse/?products=azure-maps)
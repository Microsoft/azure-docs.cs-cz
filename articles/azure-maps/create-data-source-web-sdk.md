---
title: Vytvoření zdroje dat pro mapu | Mapy Microsoft Azure
description: V tomto článku se naučíte, jak vytvořit zdroj dat a přidat ho k mapě pomocí webové sady SDK Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: 74b45d3f7fa7d0e13b8767d4a887d8a22cad3a30
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911719"
---
# <a name="create-a-data-source"></a>Vytvoření zdroje dat

Sada Azure Maps Web SDK ukládá data do zdrojů dat, která optimalizují data pro dotazování a vykreslování. V současné době existují dva typy zdrojů dat:

**Zdroj dat pro injson**

Zdroj dat založený na bázi JSON může načítat a ukládat data místně pomocí `DataSource` třídy. Data typu injson lze ručně vytvořit nebo vytvořit pomocí tříd pomocníka v oboru názvů [Atlas. data](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) . Třída `DataSource` poskytuje funkce pro import místních nebo vzdálených souborů injson. Vzdálené soubory typu injson musí být hostované na koncovém bodu s povoleným CORs. Třída `DataSource` poskytuje funkce pro data bodu clusteringu. Data je možné snadno přidat, odebrat a aktualizovat pomocí `DataSource` třídy.


> [!TIP]
> Pokud chcete přepsat všechna data v `DataSource`, pokud provedete volání `clear` potom `add` funkce, mapa se pokusí znovu vykreslit dvakrát, což by mohlo způsobit zpoždění. Místo toho použijte funkci `setShapes`, která odebere a nahradí všechna data ve zdroji dat a aktivuje pouze jedno opakované vykreslování mapy.

**Zdroj vektorové dlaždice**

Zdroj vektorové dlaždice popisuje, jak přistupovat k vrstvě vektorové dlaždice a lze ji vytvořit pomocí třídy [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) . Azure Maps zarovnává se [specifikací vektorové dlaždice Mapbox](https://github.com/mapbox/vector-tile-spec), což je otevřený standard. Vrstvy vektorové dlaždice jsou však podobné vrstvám dlaždic, ale místo každé dlaždice rastrového obrázku jsou komprimované soubory (formát PBF), které obsahují data vektorové mapy a jednu nebo více vrstev, které lze vykreslit a na základě stylu jednotlivých vrstev. Data ve vektorové dlaždici obsahují geografické funkce ve formě bodů, čar a mnohoúhelníků. Z vrstev rastrových dlaždic je několik výhod vrstev vektorových dlaždic;

 - Velikost souboru vektorové dlaždice je obvykle mnohem menší než ekvivalentní rastrová dlaždice. V takovém případě se používá menší šířka pásma, což znamená nižší latenci a rychlejší mapu. Tím se vytvoří lepší uživatelské prostředí.
 - Vzhledem k tomu, že se na klientovi vykreslují vektorové dlaždice, můžou se přizpůsobit rozlišení zařízení, na kterém se zobrazují. To umožňuje, aby vykreslené mapy vypadaly mnohem přesněji a pomocí jasných popisků. 
 - Změna stylu dat ve vektorových mapách nepotřebuje stahovat data znovu, protože na klienta lze použít nový styl. Naopak změna stylu vrstvy rastrových dlaždic obvykle vyžaduje načítání dlaždic ze serveru, na kterém je použit nový styl.
 - Vzhledem k tomu, že data jsou doručena v vektorovém formátu, je nutné pro přípravu dat použít méně zpracování na straně serveru, což znamená, že je možné rychleji zpřístupnit novější data.

Všechny vrstvy, které používají zdroj vektoru musí určovat `sourceLayer` hodnotu. 

Po vytvoření lze zdroje dat přidat do mapy prostřednictvím vlastnosti `map.sources`, což je [SourceManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager). Následující kód ukazuje, jak vytvořit `DataSource` a jak ho přidat do mapy.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

## <a name="connecting-a-data-source-to-a-layer"></a>Připojení zdroje dat ke vrstvě

Data se vykreslují na mapě pomocí vrstev vykreslování. Na jeden zdroj dat může odkazovat jedna nebo více vrstev vykreslování. Následující vrstvy vykreslování vyžadují, aby zdroj dat mohl být napájen:

- [Bublinová vrstva](map-add-bubble-layer.md) – vykreslí data bodu jako kružnice škálované na mapě.
- [Symbolová vrstva](map-add-pin.md) – vykreslí data bodů jako ikony nebo text.
- [Vrstva Heat mapy](map-add-heat-map-layer.md) – vykreslí data bodu jako Heat mapu hustoty.
- [Spojnicová vrstva](map-add-shape.md) – lze použít pro vykreslení čáry a nebo obrysu mnohoúhelníků. 
- [Mnohoúhelníková vrstva](map-add-shape.md) – vyplní oblast mnohoúhelníku plnou barvou nebo vzorkem obrázku.

Následující kód ukazuje, jak vytvořit zdroj dat, přidat ho do mapy a připojit ho k bublinové vrstvě a pak z něj importovat data bodu geografického JSON ze vzdáleného umístění. 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Existují další vrstvy vykreslování, které se k těmto zdrojům dat nepřipojují, ale místo toho se načítají data, která vykreslují přímo. 

- [Vrstva obrázku](map-add-image-layer.md) – překrývá jeden obrázek nad mapou a vytvoří vazby jeho rohů k sadě zadaných souřadnic.
- [Vrstva dlaždice](map-add-tile-layer.md) – nadmnožinou vrstvu rastrového dlaždice nad mapou.

## <a name="one-data-source-with-multiple-layers"></a>Jeden zdroj dat s více vrstvami

K jednomu zdroji dat lze připojit více vrstev. To může být trochu odlišné, ale existuje mnoho různých scénářů, kde se to hodí. Vezměte v úvahu například scénář vytváření mnohoúhelníkového kreslicího prostředí. Když uživatel nechá nakreslit mnohoúhelník, měla by se vykreslovat oblast mnohoúhelníku výplně, když uživatel přidává body na mapu. Přidání čáry se stylem, která popisuje mnohoúhelník, usnadní zobrazení hran mnohoúhelníku při jejich vykreslování. Nakonec přidání určitého druhu popisovače, jako je například PIN nebo značka, je nad každou pozicí v mnohoúhelníku jednodušší upravit každou jednotlivou polohu. Tady je obrázek, který demonstruje tento scénář.

![Mapování znázorňující více vrstev vykreslování dat z jednoho zdroje dat](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

Chcete-li dosáhnout tohoto scénáře na většině platforem mapování, je třeba vytvořit objekt mnohoúhelníku, objekt čáry a kód PIN pro každou pozici v mnohoúhelníku. Když se mnohoúhelník upraví, budete muset ručně aktualizovat čáru a PIN kódy, které se dají složitě rychle.

U Azure Maps potřebujete pouze jeden mnohoúhelník ve zdroji dat, jak je znázorněno v následujícím kódu.

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
> [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions?view=azure-maps-typescript-latest)

Další ukázky kódu pro přidání do vašich map najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Přidat automaticky otevírané okno](map-add-popup.md)

> [!div class="nextstepaction"]
> [Použití výrazů stylu založených na datech](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Přidat vrstvu symbolů](map-add-pin.md)

> [!div class="nextstepaction"]
> [Přidat bublinovou vrstvu](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Přidat řádkovou vrstvu](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Přidat mnohoúhelníkovou vrstvu](map-add-shape.md)

> [!div class="nextstepaction"]
> [Přidat Heat mapu](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Ukázky kódu](https://docs.microsoft.com/samples/browse/?products=azure-maps)
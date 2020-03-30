---
title: Přidání řádkové vrstvy do mapy | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak přidat vrstvu čar do mapy pomocí sady Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 8503b12be628fe7d5651221c9d0379bee3e292bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76933466"
---
# <a name="add-a-line-layer-to-the-map"></a>Přidání čárové vrstvy do mapy

Liniovou vrstvu lze `LineString` použít `MultiLineString` k vykreslení a prvky jako cesty nebo trasy na mapě. Liniovou vrstvu lze také použít `Polygon` k `MultiPolygon` vykreslení obrysu a prvků. Zdroj dat je připojen k liniové vrstvě, aby mu poskytla data k vykreslení. 

> [!TIP]
> Vrstvy čar ve výchozím nastavení vykreslí souřadnice polygonů i čáry ve zdroji dat. Chcete-li omezit vrstvu tak, že pouze `filter` vykreslí `['==', ['geometry-type'], 'LineString']` Funkce `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` LineString nastavit vlastnost vrstvy nebo pokud chcete zahrnout MultiLineString funkce také.

Následující kód ukazuje, jak vytvořit řádek. Přidejte čáru do zdroje dat a pak ji vykreslete čárovou vrstvou pomocí třídy [LineLayer.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a line and add it to the data source.
dataSource.add(new atlas.data.LineString([[-73.972340, 40.743270], [-74.004420, 40.756800]]));
  
//Create a line layer to render the line to the map.
map.layers.add(new atlas.layer.LineLayer(dataSource, null, {
    strokeColor: 'blue',
    strokeWidth: 5
}));
```

Níže je kompletní ukázka spuštěného kódu výše uvedených funkcí.

<br/>

<iframe height='500' scrolling='no' title='Přidání čáry do mapy' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero Přidat řádek<a href='https://codepen.io/azuremaps'>@azuremaps</a>na <a href='https://codepen.io/azuremaps/pen/qomaKv/'>mapu</a> pomocí Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Vrstvy čar lze stylovat pomocí [linelayeroptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) a [použít výrazy stylu řízené daty](data-driven-style-expressions-web-sdk.md).

## <a name="add-symbols-along-a-line"></a>Přidání symbolů podél čáry

Tato ukázka ukazuje, jak přidat ikony šipek podél čáry na mapě. Při použití vrstvy symbolů nastavte volbu "umístění" na "čáru". Tato volba vykreslí symboly podél čáry a otočí ikony (0 stupňů = vpravo).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Zobrazit šipku podél čáry" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/drBJwX/'>šipku</a> Pero<a href='https://codepen.io/azuremaps'>@azuremaps</a>zobrazit podél řádku podle Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Webová sada Azure Maps SDK poskytuje několik přizpůsobitelných šablon obrázků, které můžete použít s vrstvou symbolů. Další informace naleznete v dokumentu [Jak používat šablony obrázků.](how-to-use-image-templates-web-sdk.md)

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>Přidání přechodu tahu k čáře

Na čáru můžete aplikovat jednu barvu tahu. Můžete také vyplnit čáru s přechodem barev a zobrazit tak přechod z jednoho segmentu čáry na další segment čáry. Například přechody čar lze použít k reprezentaci změn v čase a vzdálenosti nebo různých teplot v rámci připojené čáry objektů. Chcete-li použít tuto funkci na řádek, musí `lineMetrics` mít zdroj dat nastavenou možnost na hodnotu `strokeColor` true a poté může být výraz barevného přechodu předán možnosti řádku. Výraz přechodu tahu musí `['line-progress']` odkazovat na datový výraz, který výrazu zpřístupňuje vypočtené čárové metriky.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Čára s přechodem tahu" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na řádek pera <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>s přechodem tahu podle</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-line-layer"></a>Přizpůsobení vrstvy čáry

Vrstva Čára má několik možností stylingu. Zde je nástroj, jak je vyzkoušet.

<br/>

<iframe height='700' scrolling='no' title='Volby vrstvy čáry' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>možnosti vrstvy linky</a> pera podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách použitých v tomto článku:

> [!div class="nextstepaction"]
> [Vrstva čáry](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 

> [!div class="nextstepaction"]
> [Možnosti linelayeru](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

Další ukázky kódu, které chcete přidat do map, naleznete v následujících článcích:

> [!div class="nextstepaction"]
> [Vytvoření zdroje dat](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Přidání místního okna](map-add-popup.md)

> [!div class="nextstepaction"]
> [Použití výrazů pro styly založené na datech](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Používání šablon obrázků](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Přidání mnohoúhelníkové vrstvy](map-add-shape.md)

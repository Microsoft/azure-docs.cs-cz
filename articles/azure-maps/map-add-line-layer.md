---
title: Přidat řádkovou vrstvu do Azure Maps | Microsoft Docs
description: Postup přidání řádkové vrstvy do Azure Maps webové sady SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f07e36d82c9044a212cda8173df9fe0a9544393a
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68977323"
---
# <a name="add-a-line-layer-to-the-map"></a>Přidat řádkovou vrstvu do mapy

Spojnicová vrstva se dá použít k vykreslování `LineString` a `MultiLineString` zobrazování funkcí jako cest nebo tras na mapě. Řádkovou vrstvu lze také použít k vykreslení osnovy `Polygon` a `MultiPolygon` funkcí. Zdroj dat je připojen k vrstvě čáry, aby bylo možné data vykreslit. 

> [!TIP]
> Vrstvy čar ve výchozím nastavení vykreslí souřadnice mnohoúhelníků a také čáry ve zdroji dat. Chcete-li omezit vrstvu tak, aby vykresluje pouze funkce LineString, `filter` nastavte vlastnost vrstvy na `['==', ['geometry-type'], 'LineString']` nebo `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` , pokud chcete zahrnout také funkce MultiLineString.

Následující kód ukazuje, jak vytvořit řádek, přidat ho do zdroje dat a vykreslit ho s vrstvou čáry pomocí třídy [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) .

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

Níže je uvedená ukázka kompletního spuštění kódu výše uvedené funkce.

<br/>

<iframe height='500' scrolling='no' title='Přidat čáru k mapě' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero a <a href='https://codepen.io/azuremaps/pen/qomaKv/'>přidejte čáru na mapu</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Čáry vrstev lze stylovat pomocí [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) a [používat výrazy stylu řízené daty](data-driven-style-expressions-web-sdk.md).

## <a name="add-symbols-along-a-line"></a>Přidat symboly podél čáry

Tento příklad ukazuje, jak přidat ikony šipek podél čáry na mapě. Při použití vrstvy symbolů nastavte možnost umístění na "line", vykreslí se symboly podél čáry a nasadí se ikony (0 stupňů = vpravo).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Zobrazit šipku podél čáry" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/drBJwX/'>šipku zobrazení pera podél čáry</a> po Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Sada Azure Maps Web SDK poskytuje několik přizpůsobitelných šablon obrázků, které lze použít s vrstvou symbolů. Další informace najdete v dokumentu [použití šablon obrázků](how-to-use-image-templates-web-sdk.md) .

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>Přidání přechodu mezi tahy na čáru

Kromě toho, že je možné použít jednu barvu tahu na čáru, můžete také vyplnit čáru barevným přechodem, aby se zobrazil přechod z jednoho segmentu čáry na další. Například barevné přechody lze použít k reprezentaci změn v průběhu času a vzdálenosti nebo různých teplot v rámci připojeného řádku objektů. Aby bylo možné tuto funkci použít na řádek, musí mít `lineMetrics` zdroj dat možnost nastavenou na hodnotu true a následně může být výraz barevného přechodu předána `strokeColor` možnosti řádku. Výraz přechodu na tah musí odkazovat na `['line-progress']` datový výraz, který zpřístupňuje metriku počítaného řádku k výrazu.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Čára s přechodem mezi tahy" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>čáru pera s přechodem</a> mezi Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-line-layer"></a>Přizpůsobení vrstvy čáry

Vrstva čáry: několik možností stylu. Tady je nástroj pro jejich vyzkoušení.

<br/>

<iframe height='700' scrolling='no' title='Možnosti vrstvy čáry' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>Možnosti vrstvy čáry</a> pera od Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 

> [!div class="nextstepaction"]
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

Další ukázky kódu pro přidání do vašich map najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Vytvoření zdroje dat](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Přidat automaticky otevírané okno](map-add-popup.md)

> [!div class="nextstepaction"]
> [Použití výrazů stylu založených na datech](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Používání šablon obrázků](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Přidat mnohoúhelníkovou vrstvu](map-add-shape.md)
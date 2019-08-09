---
title: Přidat obrazec s Azure Maps | Microsoft Docs
description: Postup přidání tvaru na mapu JavaScriptu
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 377cfbf93e45872e34242f9cd2432195b6ba6f92
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68849121"
---
# <a name="add-a-shape-to-a-map"></a>Přidání tvaru do mapy

V tomto článku se dozvíte, jak vykreslit geometrií na mapě pomocí vrstev line a mnohoúhelníku. Sada Azure Maps Web SDK také podporuje vytváření geometrií kroužků, jak je definováno v [rozšířeném schématu](extend-geojson.md#circle)pro stanovení biojson. Všechny funkce geometrií lze také snadno aktualizovat, pokud jsou zabaleny do třídy [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) .

<a id="addALine"></a>

## <a name="add-lines-to-the-map"></a>Přidat řádky do mapy

`LineString`funkce `MultiLineString` a slouží k reprezentaci cest a osnov na mapě.

### <a name="add-a-line"></a>Přidat čáru

<iframe height='500' scrolling='no' title='Přidat čáru k mapě' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero a <a href='https://codepen.io/azuremaps/pen/qomaKv/'>přidejte čáru na mapu</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

První blok kódu v kódu výše sestaví objekt mapy. Pokyny najdete v tématu [vytvoření mapy](./map-create.md) .

Ve druhém bloku kódu je objekt zdroje dat vytvořen pomocí třídy [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) . Vytvoří se objekt [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest) a přidá se do zdroje dat.

[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) vykreslí řádkové objekty zabalené ve [zdroji dat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Poslední blok kódu vytvoří a přidá na mapu řádkovou vrstvu. Viz Vlastnosti vrstvy čáry na [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). Zdroj dat a vrstva čáry jsou vytvořeny a přidány do mapy v rámci [obslužné rutiny události](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) , aby se zajistilo, že se řádek zobrazí po úplném načtení mapy.

### <a name="add-symbols-along-a-line"></a>Přidat symboly podél čáry

Tento příklad ukazuje, jak přidat ikony šipek podél čáry na mapě. Při použití vrstvy symbolů nastavte možnost umístění na "line", vykreslí se symboly podél čáry a nasadí se ikony (0 stupňů = vpravo).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Zobrazit šipku podél čáry" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/drBJwX/'>šipku zobrazení pera podél čáry</a> po Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Sada Azure Maps Web SDK poskytuje několik přizpůsobitelných šablon obrázků, které lze použít s vrstvou symbolů. Další informace najdete v dokumentu [použití šablon obrázků](how-to-use-image-templates-web-sdk.md) .

### <a name="line-stroke-gradient"></a>Přidání přechodu mezi tahy na čáru

Kromě toho, že je možné použít jednu barvu tahu na čáru, můžete také vyplnit čáru barevným přechodem, aby se zobrazil přechod z jednoho segmentu čáry na další. Například barevné přechody lze použít k reprezentaci změn v průběhu času a vzdálenosti nebo různých teplot v rámci připojeného řádku objektů. Aby bylo možné tuto funkci použít na řádek, musí mít `lineMetrics` zdroj dat možnost nastavenou na hodnotu true a následně může být výraz barevného přechodu předána `strokeColor` možnosti řádku. Výraz přechodu na tah musí odkazovat na `['line-progress']` datový výraz, který zpřístupňuje metriku počítaného řádku k výrazu.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Čára s přechodem mezi tahy" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>čáru pera s přechodem</a> mezi Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="customize-a-line-layer"></a>Přizpůsobení vrstvy čáry

Vrstva čáry: několik možností stylu. Tady je nástroj pro jejich vyzkoušení.

<br/>

<iframe height='700' scrolling='no' title='Možnosti vrstvy čáry' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>Možnosti vrstvy čáry</a> pera od Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addAPolygon"></a>

## <a name="add-a-polygon-to-the-map"></a>Přidat mnohoúhelník k mapě

`Polygon`funkce `MultiPolygon` a se často používají k vyjádření oblasti na mapě. 

### <a name="use-a-polygon-layer"></a>Použít mnohoúhelníkovou vrstvu 

Mnohoúhelníková vrstva vykreslí oblast mnohoúhelníku. 

<iframe height='500' scrolling='no' title='Přidání mnohoúhelníku k mapě ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero a <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>přidejte mnohoúhelník na mapu</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt mapy. Pokyny najdete v tématu [vytvoření mapy](./map-create.md) .

Ve druhém bloku kódu je objekt zdroje dat vytvořen pomocí třídy [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) . [Mnohoúhelník](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) je vytvořen z pole souřadnic a přidán ke zdroji dat. 

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) vykreslí data zabalené ve [zdroji dat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) na mapě. Poslední blok kódu vytvoří a přidá mnohoúhelníkovou vrstvu na mapu. Zobrazení vlastností mnohoúhelníkové vrstvy na [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Zdroj dat a vrstva mnohoúhelníku jsou vytvořeny a přidány do mapy v rámci [obslužné rutiny události](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) , aby se zajistilo, že se mnohoúhelník zobrazí po úplném načtení mapy.

### <a name="use-a-polygon-and-line-layer-together"></a>Použít mnohoúhelník a čáru vrstev společně

Spojnicová vrstva se dá použít k vykreslení obrysu mnohoúhelníku. 

<iframe height='500' scrolling='no' title='Mnohoúhelníková a Spojnicová vrstva pro přidání mnohoúhelníku' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Pokud chcete přidat mnohoúhelník podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>, podívejte se na <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>mnohoúhelník a vrstvu čáry</a> .
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt mapy. Pokyny najdete v tématu [vytvoření mapy](./map-create.md) .

Ve druhém bloku kódu je objekt zdroje dat vytvořen pomocí třídy [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) . [Mnohoúhelník](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) je vytvořen z pole souřadnic a přidán ke zdroji dat. 

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) vykreslí data zabalené ve [zdroji dat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) na mapě. Zobrazení vlastností mnohoúhelníkové vrstvy na [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) je pole řádků. Viz Vlastnosti vrstvy čáry na [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). Třetí blok kódu vytvoří mnohoúhelníkové a spojnicové vrstvy.

Poslední blok kódu přidá do mapy vrstvy mnohoúhelníků a čar. Zdroj dat a vrstvy jsou vytvořeny a přidány do mapy v rámci [obslužné rutiny události](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) , aby se zajistilo, že se mnohoúhelník zobrazí po úplném načtení mapy.

> [!TIP]
> Vrstvy čar ve výchozím nastavení vykreslí souřadnice mnohoúhelníků a také čáry ve zdroji dat. Chcete-li omezit vrstvu tak, aby vykresluje pouze funkce LineString, `filter` nastavte vlastnost vrstvy na `['==', ['geometry-type'], 'LineString']` nebo `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` , pokud chcete zahrnout také funkce MultiLineString.

### <a name="fill-a-polygon-with-a-pattern"></a>Naplnění mnohoúhelníku vzorem

Kromě naplnění mnohoúhelníku barvou lze také použít vzorek obrázku. Načtěte vzorek obrázku do prostředků mapy Sprite obrázku a pak na tento obrázek odkázat pomocí `fillPattern` vlastnosti mnohoúhelníkové vrstvy.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vzorek výplně mnohoúhelníku" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>se podívejte na <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>tvar výplně mnohoúhelníku</a> pera.
</iframe>


> [!TIP]
> Sada Azure Maps Web SDK nabízí několik přizpůsobitelných šablon obrázků, které můžete použít jako vzory výplní. Další informace najdete v dokumentu [použití šablon obrázků](how-to-use-image-templates-web-sdk.md) .

### <a name="customize-a-polygon-layer"></a>Přizpůsobení vrstvy mnohoúhelníku

Vrstva mnohoúhelníku má pouze několik možností stylů. Tady je nástroj pro jejich vyzkoušení.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/LXvxpg/'></a> Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>LXvxpg pera na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Přidání kruhu k mapě

Azure Maps používá rozšířenou verzi schématu injson, která poskytuje definici pro kruhy, jak je uvedeno [zde](extend-geojson.md#circle). Kruh lze na mapě `Point` vykreslit vytvořením funkce, která `subType` má `"Circle"` vlastnost s hodnotou a `radius` vlastností, která má číslo, které představuje poloměr v metrech. Příklad:

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}  
```

Azure Maps webová sada SDK tyto `Point` `Polygon` funkce převede na funkce v rámci pokrývání a je možné ji vykreslit na mapě pomocí mnohoúhelníku a vrstev čáry, jak je znázorněno zde.

<iframe height='500' scrolling='no' title='Přidání kruhu k mapě' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na pero a <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>přidejte do mapy</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

První blok kódu v kódu výše sestaví objekt mapy. Pokyny najdete v tématu [vytvoření mapy](./map-create.md) .

Ve druhém bloku kódu je objekt zdroje dat vytvořen pomocí třídy [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) . Kruh je [funkce](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) `subType` a má vlastnost nastavenou na `"Circle"` a `radius` hodnotu vlastnosti v měřičích. Když `"Circle"` je funkce bodu s a `subType` přidána ke zdroji dat, převedena na kruhový mnohoúhelník v rámci mapy.

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) vykreslí data zabalené ve [zdroji dat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) na mapě. Poslední blok kódu vytvoří a přidá mnohoúhelníkovou vrstvu na mapu. Zobrazení vlastností mnohoúhelníkové vrstvy na [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Zdroj dat a vrstva mnohoúhelníku jsou vytvořeny a přidány do mapy v rámci [obslužné rutiny události](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) , aby se zajistilo, že se kroužek zobrazí po úplném načtení mapy.

## <a name="make-a-geometry-easy-to-update"></a>Usnadnění aktualizace geometrie

Třída obaluje geometrii nebo [funkci](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) a usnadňuje jejich aktualizaci a údržbu. [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) `Shape`
`new Shape(data: Feature<data.Geometry, any>)`Vytvoří objekt Shape a inicializuje jej pomocí zadané funkce.

<br/>

<iframe height='500' scrolling='no' title='Aktualizovat vlastnosti obrazce' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Prohlédněte si <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>vlastnosti obrazce aktualizace</a> pera podle Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

První blok kódu výše sestaví objekt mapy. Pokyny najdete v tématu [vytvoření mapy](./map-create.md) .

Bod je [funkcí](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) [bodu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) třídy. Druhý blok kódu inicializuje hodnotu poloměru pro prvek posuvníku HTML a potom vytvoří a zabalí objekt Point v objektu třídy [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) .

Třetí blok kódu vytvoří funkci, která přebírá hodnotu z prvku posuvníku rozsahu HTML a mění hodnotu poloměr pomocí metody Shape třídy [AddProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) .

Ve čtvrtém bloku kódu je objekt zdroje dat vytvořen pomocí třídy [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) . Bod se pak přidá do zdroje dat.

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) vykreslí data zabalené ve [zdroji dat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) na mapě. Třetí blok kódu vytvoří mnohoúhelníkovou vrstvu. Zobrazení vlastností mnohoúhelníkové vrstvy na [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Zdroj dat, obslužná rutina události Click a vrstva mnohoúhelníku jsou vytvořeny a přidány do mapy v rámci [obslužné rutiny události](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) , aby se zajistilo, že se bod zobrazí po úplném načtení mapy.

## <a name="next-steps"></a>Další postup

Další příklady kódu pro přidání do map najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Použití výrazů stylu založených na datech](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Používání šablon obrázků](how-to-use-image-templates-web-sdk.md)
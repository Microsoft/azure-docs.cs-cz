---
title: Přidání vrstvy symbolu do mapy | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak pomocí vrstvy Symbol přizpůsobit symbol a přidat symboly na mapě pomocí sady Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b8d131dcc798fb2fe1d4bb650cd5b0a68903381b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209694"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Přidání vrstvy symbolu do mapy

Připojte symbol ke zdroji dat a použijte ho k vykreslení ikony nebo textu v daném bodě. 

Vrstvy symbolů jsou vykresleny pomocí WebGL. Použijte vrstvu symbolů k vykreslení velkých sbírek bodů na mapě. Ve srovnání se značkou HTML vykreslí vrstva symbolů velký počet bodových dat na mapě s lepším výkonem. Vrstva symbolů však nepodporuje tradiční prvky CSS a HTML pro styling.  

> [!TIP]
> Vrstvy symbolů ve výchozím nastavení vykreslí souřadnice všech geometrií ve zdroji dat. Chcete-li vrstvu omezit tak, aby vykreslovala pouze prvky geometrie bodů, nastavte `filter` vlastnost hladiny nebo `['==', ['geometry-type'], 'Point']` `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` pokud chcete, můžete zahrnout také prvky Vícebodového bodu.

Správce pohyblivých symbolů map načte vlastní obrázky používané vrstvou symbolů. Podporuje následující formáty obrázků:

- JPEG
- PNG
- Svg
- BMP
- GIF (bez animací)

## <a name="add-a-symbol-layer"></a>Přidání vrstvy symbolů

Než budete moci přidat vrstvu symbolů do mapy, musíte udělat pár kroků. Nejprve vytvořte zdroj dat a přidejte jej do mapy. Vytvořte vrstvu symbolů. Potom předaj zdroj dat do vrstvy symbolů, aby se načetla data ze zdroje dat. Nakonec přidejte data do zdroje dat, aby bylo něco, co má být vykresleno. 

Níže uvedený kód ukazuje, co by měly být přidány do mapy po načtení. Tato ukázka vykreslí jeden bod na mapě pomocí vrstvy symbolů. 

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a symbol layer to render icons and/or text at points on the map.
var layer = new atlas.layer.SymbolLayer(dataSource);

//Add the layer to the map.
map.layers.add(layer);

//Create a point and add it to the data source.
dataSource.add(new atlas.data.Point([0, 0]));
```

Existují čtyři různé typy bodových dat, které lze přidat do mapy:

- Geometrie bodu GeoJSON - Tento objekt obsahuje pouze souřadnici bodu a nic jiného. Pomocnou `atlas.data.Point` třídu lze snadno vytvořit tyto objekty.
- Geometrie GeoJSON MultiPoint - Tento objekt obsahuje souřadnice více bodů a nic jiného. Pomocnou `atlas.data.MultiPoint` třídu lze snadno vytvořit tyto objekty.
- Funkce GeoJSON - Tento objekt se skládá z libovolné geometrie GeoJSON a sady vlastností, které obsahují metadata přidružená ke geometrii. Pomocnou `atlas.data.Feature` třídu lze snadno vytvořit tyto objekty.
- `atlas.Shape`je podobná funkci GeoJSON. Oba se skládají z geometrie GeoJSON a sady vlastností, které obsahují metadata přidružená ke geometrii. Pokud je objekt GeoJSON přidán do zdroje dat, lze jej snadno vykreslit ve vrstvě. Pokud je však aktualizována vlastnost souřadnice tohoto objektu GeoJSON, zdroj dat a mapa se nezmění. Je to proto, že neexistuje žádný mechanismus v objektu JSON pro aktivaci aktualizace. Třída shape poskytuje funkce pro aktualizaci dat, která obsahuje. Po navádění změny jsou zdroj dat a mapa automaticky upozorňovány a aktualizovány. 

Následující ukázka kódu vytvoří geometrii Bodu GeoJSON a předá ji do `atlas.Shape` třídy, aby bylo snadné ji aktualizovat. Střed mapy se zpočátku používá k vykreslení symbolu. Událost kliknutí je přidána do mapy tak, že při jeho vyvolání `setCoordinates` se souřadnice myši používají s funkcí obrazců. Souřadnice myši jsou zaznamenány v době události kliknutí. Potom aktualizuje `setCoordinates` umístění symbolu na mapě.

<br/>

<iframe height='500' scrolling='no' title='Přepnout polohu kolíku' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>umístění pinu</a> pen switch podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Ve výchozím nastavení vrstvy symbolů optimalizují vykreslování symbolů skrytím symbolů, které se překrývají. Při přiblížení se skryté symboly stanou viditelnými. Chcete-li tuto funkci zakázat a vykreslit `iconOptions` všechny `true`symboly za všech okolností, nastavte `allowOverlap` vlastnost možností na .

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Přidání vlastní ikony do vrstvy symbolů

Vrstvy symbolů jsou vykresleny pomocí WebGL. Jako takové všechny prostředky, jako jsou obrázky ikon, musí být načteny do kontextu WebGL. Tato ukázka ukazuje, jak přidat vlastní ikonu do mapových prostředků. Tato ikona se pak používá k vykreslení dat bodů s vlastním symbolem na mapě. Vlastnost `textField` vrstvy symbolů vyžaduje, aby byl určen výraz. V tomto případě chceme vykreslit vlastnost teploty. Vzhledem k tomu, že teplota je číslo, musí být převedena na řetězec. Kromě toho chceme připojit "° F" k němu. Výraz lze použít k tomuto zřetězení; `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Ikona obrázku vlastního symbolu' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na ikonu vlastní symbol<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>u</a> pera podle Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Webová sada Azure Maps SDK poskytuje několik přizpůsobitelných šablon obrázků, které můžete použít s vrstvou symbolů. Další informace naleznete v dokumentu [Jak používat šablony obrázků.](how-to-use-image-templates-web-sdk.md)

## <a name="customize-a-symbol-layer"></a>Přizpůsobení vrstvy symbolů 

Vrstva symbolů má k dispozici mnoho možností stylingu. Zde je nástroj pro testování těchto různých možností stylingu.

<br/>

<iframe height='700' scrolling='no' title='Volby vrstvy symbolu' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/PxVXje/'>možnosti vrstvy symbolu</a> pera podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Chcete-li vykreslit pouze text s vrstvou symbolu, `image` můžete ikonu skrýt `'none'`nastavením vlastnosti voleb ikon na .

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách použitých v tomto článku:

> [!div class="nextstepaction"]
> [Vrstva symbolů](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TextMožnosti](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

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
> [Přidání řádkové vrstvy](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Přidání mnohoúhelníkové vrstvy](map-add-shape.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy bublin](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Přidat html tvůrci](map-add-bubble-layer.md)

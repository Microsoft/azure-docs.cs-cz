---
title: Přidat vrstvu symbolů do mapy | Mapy Microsoft Azure
description: Naučte se, jak přidat přizpůsobené symboly, jako je text nebo ikony, do map. Pro tento účel se podívejte, jak používat zdroje dat a vrstvy symbolů v sadě Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 5b042bc12202de5fc0fe30aac62e065538abf3a0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91310488"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Přidání vrstvy symbolů do mapy

Umožňuje připojit symbol ke zdroji dat a použít ho k vykreslení ikony nebo textu v daném bodě. 

Vrstvy symbolů se vykreslují pomocí WebGL. Použijte vrstvu symbolů pro vykreslení velkých kolekcí bodů na mapě. Ve srovnání s HTML značkou vrstva symbolů vykreslí na mapě velký počet bodů dat s lepším výkonem. Nicméně vrstva symbolů nepodporuje tradiční prvky CSS a HTML pro stylování.  

> [!TIP]
> Vrstvy symbolů ve výchozím nastavení vykreslí souřadnice všech geometrií ve zdroji dat. Chcete-li omezit vrstvu tak, aby vykreslí pouze funkce geometrie bodu nastavte `filter` vlastnost vrstvy na `['==', ['geometry-type'], 'Point']` nebo `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` , pokud chcete, můžete také zahrnout funkce systému MultiPoint.

Správce mapy Sprite obrázku načte vlastní image používané vrstvou symbolů. Podporuje následující formáty obrázků:

- JPEG
- PNG
- SVG
- BMP
- GIF (žádné animace)

## <a name="add-a-symbol-layer"></a>Přidání vrstvy symbolů

Předtím, než můžete na mapu přidat vrstvu symbolů, je nutné provést několik kroků. Nejprve vytvořte zdroj dat a přidejte jej do mapy. Vytvoří symbolovou vrstvu. Potom předejte zdroj dat do vrstvy symbolů, aby bylo možné načíst data ze zdroje dat. Nakonec přidejte data do zdroje dat, aby bylo vygenerováno něco. 

Následující kód ukazuje, co by mělo být přidáno do mapy poté, co bylo načteno. Tato ukázka vykreslí jeden bod na mapě pomocí vrstvy symbolů. 

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

K mapě je možné přidat čtyři různé typy dat bodu:

- Geometrická geometrie bodu JSON – tento objekt obsahuje pouze souřadnici bodu a nic jiného. `atlas.data.Point`Pomocná třída se dá použít ke snadnému vytváření těchto objektů.
- Geometrická geometrie systému pro data JSON – tento objekt obsahuje souřadnice více bodů a nic jiného. `atlas.data.MultiPoint`Pomocná třída se dá použít ke snadnému vytváření těchto objektů.
- Geografická funkce JSON – tento objekt se skládá z libovolného geometrického geometrie a sady vlastností, které obsahují metadata přidružená k geometrii. `atlas.data.Feature`Pomocná třída se dá použít ke snadnému vytváření těchto objektů.
- `atlas.Shape` Třída je podobná funkci s funkcí injson. Oba se skládají z geometrické geometrie a sady vlastností, které obsahují metadata přidružená k geometrii. Pokud je objekt typu "injson" přidán do zdroje dat, lze jej snadno vykreslit ve vrstvě. Nicméně, pokud je vlastnost souřadnice tohoto objektu. JSON aktualizována, zdroj dat a mapování se nemění. Důvodem je, že v objektu JSON není žádný mechanismus pro aktivaci aktualizace. Třída Shape poskytuje funkce pro aktualizaci dat, která obsahuje. Když je provedena změna, zdroj dat a mapa budou automaticky upozorňovány a aktualizovány. 

Následující ukázka kódu vytvoří geometrii geometrického bodu JSON a předá ji do `atlas.Shape` třídy, aby se usnadnila její aktualizace. Uprostřed mapy se zpočátku používá k vykreslení symbolu. Událost Click je přidána na mapu, například v případě, že je aktivována, jsou souřadnice myši použity spolu s funkcí Shapes `setCoordinates` . Souřadnice myši se zaznamenávají v době události Click. Pak `setCoordinates` aktualizuje umístění symbolu na mapě.

<br/>

<iframe height='500' scrolling='no' title='Přepnout umístění PIN kódu' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>adresu PIN pro přepínač</a> pera Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Ve výchozím nastavení vrstvy symbolů optimalizují vykreslování symbolů skrytím symbolů, které se překrývají. Při přiblížení se budou skryté symboly zobrazovat. Chcete-li tuto funkci zakázat a vykreslit všechny symboly za všech okolností, nastavte `allowOverlap` vlastnost `iconOptions` možností na `true` .

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Přidání vlastní ikony do vrstvy symbolů

Vrstvy symbolů se vykreslují pomocí WebGL. Jako takové všechny prostředky, například obrázky ikon, je nutné načíst do kontextu WebGL. Tento příklad ukazuje, jak přidat vlastní ikonu k prostředkům mapy. Tato ikona se pak použije k vykreslení dat bodů s vlastním symbolem na mapě. `textField`Vlastnost vrstvy symbolů vyžaduje, aby byl zadán výraz. V tomto případě chceme vykreslit vlastnost teploty. Vzhledem k tomu, že teplota je číslo, je nutné ji převést na řetězec. Navíc chceme k němu připojit "°F". Výraz lze použít k provedení tohoto zřetězení; `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Ikona obrázku vlastního symbolu' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Azure Maps <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>Ikona obrázku vlastního symbolu</a> pera <a href='https://codepen.io/azuremaps'>@azuremaps</a> na <a href='https://codepen.io'>CodePen</a>() se zobrazí na obrázku.
</iframe>

> [!TIP]
> Sada Azure Maps Web SDK poskytuje několik přizpůsobitelných šablon obrázků, které lze použít s vrstvou symbolů. Další informace najdete v dokumentu [použití šablon obrázků](how-to-use-image-templates-web-sdk.md) .

## <a name="customize-a-symbol-layer"></a>Přizpůsobení vrstvy symbolů 

Vrstva symbolů má k dispozici mnoho možností stylů. Tady je nástroj, který slouží k otestování těchto různých možností stylů.

<br/>

<iframe height='700' scrolling='no' title='Možnosti vrstvy symbolů' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/PxVXje/'>Možnosti vrstvy symbolů</a> pera od Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Pokud chcete vykreslit pouze text s vrstvou symbolů, můžete ikonu skrýt nastavením `image` Vlastnosti možnosti ikony na `'none'` .

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions)

> [!div class="nextstepaction"]
> [TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions)

Další ukázky kódu pro přidání do vašich map najdete v následujících článcích:

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
> [Přidání tvůrců HTML](map-add-bubble-layer.md)

---
title: Přidat vrstvu symbolů do Azure Maps | Microsoft Docs
description: Postup přidání symbolů do Azure Maps webové sady SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: fff73801d20333a6df5e7952d02ed664c17fe40b
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480609"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Přidání vrstvy symbolů do mapy

Symbol může být připojen ke zdroji dat a používá se k vykreslení ikony nebo textu v daném bodě. Vrstvy symbolů se vykreslují pomocí WebGL a dají se použít k vykreslování velkých kolekcí bodů na mapě. Tato vrstva může na mapě vykreslovat mnohem více bodů s dobrým výkonem, než kolik je možné pomocí značek HTML. Nicméně vrstva symbolů nepodporuje tradiční prvky CSS a HTML pro stylování.  

> [!TIP]
> Vrstvy symbolů ve výchozím nastavení vykreslí souřadnice všech geometrií ve zdroji dat. Chcete-li omezit vrstvu tak, aby vykreslí pouze funkce geometrie bodu, nastavte vlastnost `filter` vrstvy na `['==', ['geometry-type'], 'Point']` nebo `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`, pokud chcete zahrnout i funkce systému MultiPoint.

Správce mapy Sprite obrázku, který se používá k načtení vlastních imagí používaných vrstvou symbolů, podporuje následující formáty obrázků:

- JPEG
- PNG
- SVG
- BMP
- GIF (žádné animace)

## <a name="add-a-symbol-layer"></a>Přidání vrstvy symbolů

Chcete-li přidat vrstvu symbolů do mapy a vykreslovat data, je třeba nejprve vytvořit zdroj dat a přidat mapu. Vrstvu symbolů lze poté vytvořit a předat zdroji dat a načíst data z. Nakonec je třeba přidat data do zdroje dat, aby bylo vygenerováno něco. Následující kód ukazuje kód, který by měl být přidán do mapy poté, co byl načten pro vykreslení jediného bodu na mapě pomocí vrstvy symbolů. 

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

Existují čtyři různé typy datových bodů, které je možné přidat do mapy:

- Geometrická geometrie bodu JSON – tento objekt obsahuje pouze souřadnici bodu a nic jiného. Pomocnou třídu `atlas.data.Point` lze použít ke snadnému vytvoření těchto objektů.
- Geometrická geometrie systému pro data JSON – tento objekt obsahuje souřadnice více bodů, ale nic jiného. Pomocnou třídu `atlas.data.MultiPoint` lze použít ke snadnému vytvoření těchto objektů.
- Geografická funkce JSON – tento objekt se skládá z libovolného geometrického geometrie a sady vlastností, které obsahují metadata přidružená k geometrii. Pomocnou třídu `atlas.data.Feature` lze použít ke snadnému vytvoření těchto objektů.
- Třída `atlas.Shape` je podobná funkci Geometricke v tom, že se skládá z geometrické geometrie JSON a sady vlastností, které obsahují metadata přidružená k geometrii. Pokud je objekt. JSON přidaný do zdroje dat, může být snadno vykreslen ve vrstvě, ale pokud je vlastnost souřadnice tohoto objektu. JSON aktualizována, zdroj dat a mapování se nemění, protože v objektu JSON není žádný mechanismus pro aktivaci aktualizace. Třída Shape poskytuje funkce pro aktualizaci dat, která obsahuje, a když je provedena změna, zdroj dat a mapa budou automaticky upozorňovány a aktualizovány. 

Následující ukázka kódu vytvoří geometrii geometrického bodu JSON a předá ji do třídy `atlas.Shape`, aby se usnadnila její aktualizace. Uprostřed mapy se zpočátku používá k vykreslení symbolu. Událost Click je přidána do mapy tak, že když je aktivována, budou použity souřadnice místa, kde byla kliknuto myší použita s tvary `setCoordinates` funkce, která aktualizuje umístění symbolu na mapě.

<br/>

<iframe height='500' scrolling='no' title='Přepnout umístění PIN kódu' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na Azure Maps (@azuremaps) na <a href='https://codepen.io'>CodePen</a>(<a href='https://codepen.io/azuremaps'> </a>) na <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>pozici pro přepnutí</a> pera.
</iframe>

> [!TIP]
> Ve výchozím nastavení pro výkon, vrstvy symbolů optimalizují vykreslování symbolů skrytím symbolů, které se překrývají. Při přiblížení se budou skryté symboly zobrazovat. Chcete-li tuto funkci zakázat a vykreslit všechny symboly ve všech časech, nastavte vlastnost `allowOverlap` možností `iconOptions` na `true`.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Přidání vlastní ikony do vrstvy symbolů

Vrstvy symbolů se vykreslují pomocí WebGL. Jako takové všechny prostředky, například obrázky ikon, je nutné načíst do kontextu WebGL. Tento příklad ukazuje, jak přidat vlastní ikonu do prostředků mapy a pak ji použít k vykreslení dat bodů s vlastním symbolem na mapě. Vlastnost `textField` vrstvy symbolů vyžaduje, aby byl zadán výraz. V tomto případě chceme vykreslit vlastnost teploty, ale vzhledem k tomu, že se jedná o číslo, je nutné ji převést na řetězec. Navíc chceme k němu připojit "°F". K tomu lze použít výraz. `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Ikona obrázku vlastního symbolu' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>Ikona obrázku vlastního symbolu</a> pera se zobrazí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Sada Azure Maps Web SDK poskytuje několik přizpůsobitelných šablon obrázků, které lze použít s vrstvou symbolů. Další informace získáte v dokumentu [použití šablon obrázků](how-to-use-image-templates-web-sdk.md) .

## <a name="customize-a-symbol-layer"></a>Přizpůsobení vrstvy symbolů 

Vrstva symbolů má k dispozici mnoho možností stylů. Tady je nástroj, který slouží k otestování těchto různých možností stylů.

<br/>

<iframe height='700' scrolling='no' title='Možnosti vrstvy symbolů' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/PxVXje/'>Možnosti vrstvy symbolů</a> pera Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Pokud chcete pouze vykreslit text s vrstvou symbolů, můžete ikonu skrýt nastavením vlastnosti `image` možností ikony na `'none'`.

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

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
> [Přidat řádkovou vrstvu](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Přidat mnohoúhelníkovou vrstvu](map-add-shape.md)

> [!div class="nextstepaction"]
> [Přidat bublinovou vrstvu](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Přidání tvůrců HTML](map-add-bubble-layer.md)

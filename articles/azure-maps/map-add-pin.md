---
title: Přidat vrstvu symbolů do Azure Maps | Microsoft Docs
description: Postup přidání symbolů do mapy JavaScriptu
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ba5d5d3aaa6a83dbcc5e5072872bca0fcd22bbf9
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638723"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Přidání vrstvy symbolů do mapy

V tomto článku se dozvíte, jak můžete vykreslit data bodů ze zdroje dat jako vrstvu symbolů na mapě. Vrstvy symbolů se vykreslují pomocí WebGL a podporují mnohem větší sady bodů než značky HTML, ale nepodporují tradiční prvky CSS a HTML pro stylování.  

> [!TIP]
> Vrstvy symbolů ve výchozím nastavení vykreslí souřadnice všech geometrií ve zdroji dat. Chcete-li omezit vrstvu tak, aby vykreslí pouze funkce geometrie bodu `filter` , nastavte vlastnost vrstvy na `['==', ['geometry-type'], 'Point']` nebo `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` , pokud chcete zahrnout i funkce systému MultiPoint.

## <a name="add-a-symbol-layer"></a>Přidání vrstvy symbolů

<iframe height='500' scrolling='no' title='Přepnout umístění PIN kódu' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>adresu PIN pro přepínač</a> pera Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

První blok kódu výše sestaví objekt mapy. Pokyny najdete v tématu [vytvoření mapy](./map-create.md) .

Ve druhém bloku kódu je objekt zdroje dat vytvořen pomocí třídy [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) . [Funkce] obsahující geometrii [bodu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) je zabalena třídou [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) , aby bylo snazší je aktualizovat a poté vytvořena a přidána do zdroje dat.

Třetí blok kódu vytvoří [naslouchací proces události](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) a aktualizuje souřadnice bodu při kliknutí myší pomocí metody [setCoordinates](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) třídy Shape.

[Symbolová vrstva](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) používá text nebo ikony pro vykreslení dat na základě bodu zabalených ve [zdroji dat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako symboly na mapě.  Zdroj dat, naslouchací proces kliknutí a vrstva symbolu jsou vytvořeny a přidány do mapy v rámci `ready` funkce [naslouchacího procesu události](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) , aby se zajistilo, že se bod zobrazí po načtení mapy a jeho přípravě na použití.

> [!TIP]
> Ve výchozím nastavení pro výkon, vrstvy symbolů optimalizují vykreslování symbolů skrytím symbolů, které se překrývají. Při přiblížení se budou skryté symboly zobrazovat. Chcete-li tuto funkci zakázat a vykreslit všechny symboly za všech okolností `allowOverlap` , nastavte vlastnost `iconOptions` možností na `true`.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Přidání vlastní ikony do vrstvy symbolů

Vrstvy symbolů se vykreslují pomocí WebGL. Jako takové všechny prostředky, například obrázky ikon, je nutné načíst do kontextu WebGL. Tento příklad ukazuje, jak přidat vlastní ikonu do prostředků mapy a pak ji použít k vykreslení dat bodů s vlastním symbolem na mapě. `textField` Vlastnost vrstvy symbolů vyžaduje, aby byl zadán výraz. V tomto případě chceme vykreslit vlastnost teploty, ale vzhledem k tomu, že se jedná o číslo, je nutné ji převést na řetězec. Navíc chceme k němu připojit "°F". K tomu lze použít výraz. `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Ikona obrázku vlastního symbolu' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Azure Maps <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>Ikona obrázku vlastního symbolu</a> pera na<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>() se zobrazí na obrázku.
</iframe>

## <a name="customize-a-symbol-layer"></a>Přizpůsobení vrstvy symbolů 

Vrstva symbolů má k dispozici mnoho možností stylů. Tady je nástroj, který slouží k otestování těchto různých možností stylů.

<br/>

<iframe height='700' scrolling='no' title='Možnosti vrstvy symbolů' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/PxVXje/'>Možnosti vrstvy symbolů</a> pera od Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další postup

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TexTOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

Další ukázky kódu pro přidání do vašich map najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Přidat automaticky otevírané okno](map-add-popup.md)

> [!div class="nextstepaction"]
> [Použití výrazů stylu založených na datech](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Přidat tvar](map-add-shape.md)

> [!div class="nextstepaction"]
> [Přidat bublinovou vrstvu](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Přidání tvůrců HTML](map-add-bubble-layer.md)

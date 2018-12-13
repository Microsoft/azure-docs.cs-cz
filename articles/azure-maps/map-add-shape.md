---
title: Přidání obrazce s Azure Maps | Dokumentace Microsoftu
description: Přidání obrazce do mapy jazyka Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0753e4f2bee1259356f1c27c3b9967a914b27798
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888629"
---
# <a name="add-a-shape-to-a-map"></a>Přidání obrazce do mapy

V tomto článku se dozvíte, jak přidat [tvar](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) mapy a aktualizace vlastností existujícího tvar na mapě.

<a id="addALine"></a>

## <a name="add-a-line"></a>Přidat řádek

<iframe height='500' scrolling='no' title='Přidání řádku do mapy' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/qomaKv/'>přidá řádek do mapy</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

První blok kódu ve výše uvedeném kódu vytvoří objekt Map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

V druhém bloku kódu, je vytvořen objekt zdroje dat pomocí [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) třídy. A [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest) je vytvořen a přidán do zdroje dat objektu.

A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) vykresluje řádek objektů, který je obalen [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Poslední blok kódu vytvoří a přidá čárovou vrstvu mapy. Zobrazit vlastnosti čárovou vrstvu na [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linestringlayeroptions?view=azure-iot-typescript-latest). Vytvořen a přidán do mapování v rámci zdroje dat a čárovou vrstvu [naslouchací proces událostí](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funkce, která se ujistěte, že po načtení mapy plně, zobrazí se řádek.

## <a name="customize-a-line-layer"></a>Přizpůsobení čárovou vrstvu

Na řádku vrstvy několik možností, jak stylů. Zde je nástroj, který je vyzkoušet.

<br/>

<iframe height='700' scrolling='no' title='Možnosti řádku vrstvy' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>vrstvy možností</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle"></a>Přidejte kruh

<iframe height='500' scrolling='no' title='Přidejte kruh do mapy' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>přidejte kruh k mapě</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

První blok kódu ve výše uvedeném kódu vytvoří objekt Map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

V druhém bloku kódu, je vytvořen objekt zdroje dat pomocí [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) třídy. Kruh je [funkce](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) z [bodu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) a má `subType` vlastnost nastavena na hodnotu "kroužek" a `radius` měřiče hodnotu vlastnosti. Když funkce bod se podtypem kruhu se přidá ke zdroji dat, ji převede do kruhové mnohoúhelníku v mapě.

A [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) vykreslí data, který je obalen [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) na mapě. Poslední blok kódu vytvoří a přidá mnohoúhelníkovou vrstvu mapy. Zobrazit vlastnosti vrstvy mnohoúhelníků na [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Zdroj dat a vrstvy mnohoúhelníků vytvořen a přidán do mapování v rámci [naslouchací proces událostí](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funkce zajistit, že po načtení mapy plně, zobrazí se kruh.

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>Přidat mnohoúhelníku

Existují dva různé způsoby mnohoúhelníku můžete přidat do mapy. Obě jsou vysvětlené v následujících příkladech.

### <a name="use-polygon-layer"></a>Použití mnohoúhelníková vrstva 

<iframe height='500' scrolling='no' title='Přidat mnohoúhelníku mapy ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>přidat mnohoúhelníku mapy </a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt Map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

V druhém bloku kódu, je vytvořen objekt zdroje dat pomocí [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) třídy. A [mnohoúhelníku](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) je vytvořen z pole souřadnic a přidán do zdroje dat. 

A [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) vykreslí data, který je obalen [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) na mapě. Poslední blok kódu vytvoří a přidá mnohoúhelníkovou vrstvu mapy. Zobrazit vlastnosti vrstvy mnohoúhelníků na [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Zdroj dat a vrstvy mnohoúhelníků vytvořen a přidán do mapování v rámci [naslouchací proces událostí](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funkce zajistit, že po načtení mapy plně, zobrazí se mnohoúhelníku.

### <a name="use-polygon-and-line-layer"></a>Použití vrstvy mnohoúhelníků a čar

<iframe height='500' scrolling='no' title='Přidat mnohoúhelníku vrstvy mnohoúhelníků a čar' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>přidat mnohoúhelníku vrstvy mnohoúhelníků a čar</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt Map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

V druhém bloku kódu, je vytvořen objekt zdroje dat pomocí [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) třídy. A [mnohoúhelníku](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) je vytvořen z pole souřadnic a přidán do zdroje dat. 

A [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) vykreslí data, který je obalen [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) na mapě. Zobrazit vlastnosti vrstvy mnohoúhelníků na [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) je pole řádků. Zobrazit vlastnosti čárovou vrstvu na [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). Třetí bloku kódu vytvoří vrstvy mnohoúhelníků a čar.

Poslední blok kódu přidá vrstvy mnohoúhelníků a čar do mapy. Vytvořen a přidán do mapování v rámci zdroje dat a vrstvy [naslouchací proces událostí](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funkce zajistit, že po načtení mapy plně, zobrazí se mnohoúhelníku.

## <a name="customize-a-polygon-layer"></a>Přizpůsobení mnohoúhelníková vrstva

Mnohoúhelníková vrstva má jenom pár možností stylu. Zde je nástroj, který je vyzkoušet.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="update-a-shape"></a>Aktualizace obrazec

Zabalí obrazec třídy [geometrie](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) nebo [funkce](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) a snadno aktualizovat a spravovat je.
`new Shape(data: Feature<data.Geometry, any>)` Vytvoří objekt tvar a inicializuje ji s určenou funkci.

<br/>

<iframe height='500' scrolling='no' title='Aktualizace vlastností obrazce' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>aktualizaci vlastností obrazce</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

První blok výše uvedený kód vytvoří objekt Map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

Bod je [funkce](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) z [bodu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) třídy. Druhý bloku kódu inicializuje radius hodnota prvku posuvník HTML a pak vytvoří a zabalí objekt point v [tvar](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) objektu třídy.

Třetí blok kódu vytvoří funkci, která přijímá hodnotu z prvku HTML rozsah posuvníku a změní hodnotu radius horizontálních oddílů pomocí třídy tvar [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest#addproperty) metody.

Ve čtvrtém bloku kódu, je vytvořen objekt zdroje dat pomocí [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) třídy. Bod se pak přidá do datového zdroje.

A [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) vykreslí data, který je obalen [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) na mapě. Třetí bloku kódu vytvoří mnohoúhelníkovou vrstvu. Zobrazit vlastnosti vrstvy mnohoúhelníků na [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Vytvořen a přidán do mapování v rámci zdroje dat, naslouchací proces událostí kliknutím a mnohoúhelníkovou vrstvu [naslouchací proces událostí](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funkce, která se ujistěte, že po načtení mapy plně, zobrazí se místo.

## <a name="next-steps"></a>Další postup

Další příklady kódu pro přidání do vaše mapy najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Přidat vlastního HTML](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Zobrazení výsledků hledání](./map-search-location.md)

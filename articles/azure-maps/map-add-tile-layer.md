---
title: Přidání vrstvy dlaždic do mapy | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak překrýt vrstvu dlaždice na mapě pomocí sady Microsoft Azure Maps Web SDK. Vrstvy dlaždic umožňují vykreslit obrazy na mapě.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 61d7a11df499e6b740adb45968721b6a9bb1af22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988596"
---
# <a name="add-a-tile-layer-to-a-map"></a>Přidání vrstvy dlaždic do mapy

V tomto článku se můžete naučit překrýt vrstvu dlaždic na mapě. Vrstvy dlaždic umožňují překrývat obrázky nad dlaždicemi základní mapy Azure Maps. Další informace o systému dlaždic Azure Maps najdete v tématu [Úrovně přiblížení a mřížka dlaždic](zoom-levels-and-tile-grid.md).

Vrstva dlaždice se načítá v dlaždicích ze serveru. Tyto obrazy mohou být předem vykresleny nebo dynamicky vykresleny. Předvykreslené obrazy jsou uloženy jako všechny ostatní obrázky na serveru pomocí konvence pojmenování, které rozumí vrstva dlaždice. Dynamicky vykreslené obrázky používají službu k načtení obrázků v blízkosti reálného času. Existují tři různé dlaždice služby pojmenování zásad podporovaných Azure Maps [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) třídy: 

* X, Y, Zoom zápis - X je sloupec, Y je řádek pozice dlaždice v mřížce dlaždice, a Zoom notace hodnotu na základě úrovně zvětšení.
* Zápis quadkey - Kombinuje x, y a zoom informace do hodnoty jednoho řetězce. Tato hodnota řetězce se stane jedinečným identifikátorem pro jednu dlaždici.
* Ohraničovací rámeček - Zadejte obraz `{west},{south},{east},{north}`ve formátu souřadnic ohraničovacího rámečku: . Tento formát je běžně používán [službou WMS (Web Mapping Services).](https://www.opengeospatial.org/standards/wms)

> [!TIP]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) je skvělý způsob, jak vizualizovat velké sady dat na mapě. Nejen, že vrstva dlaždice může být generována z obrazu, vektorová data mohou být také vykreslena jako vrstva dlaždic. Vykreslením vektorových dat jako vrstvy dlaždic musí ovládací prvek mapy načíst pouze dlaždice, které mají menší velikost souboru než vektorová data, která představují. Tato technika se běžně používá k vykreslení milionů řádků dat na mapě.

Adresa URL dlaždice předaná do vrstvy dlaždice musí být adresa HTTP nebo adresa URL https prostředku TileJSON nebo šablona adresy URL dlaždice, která používá následující parametry: 

* `{x}`- X pozice dlaždice. Také `{y}` potřeby `{z}`a .
* `{y}`- Pozice dlaždice. Také `{x}` potřeby `{z}`a .
* `{z}`- Úroveň přiblížení dlaždice. Také `{x}` potřeby `{y}`a .
* `{quadkey}`- Dlaždice quadkey identifikátor založený na službě Bing Maps dlaždice systému pojmenování konvence.
* `{bbox-epsg-3857}`- Ohraničovací řetězec `{west},{south},{east},{north}` s formátem v prostorovém referenčním systému EPSG 3857.
* `{subdomain}`- Zástupný symbol pro hodnoty subdomény, pokud je `subdomain` zadán, bude přidán.

## <a name="add-a-tile-layer"></a>Přidání vrstvy dlaždic

 Tato ukázka ukazuje, jak vytvořit vrstvu dlaždic, která odkazuje na sadu dlaždic. Tato ukázka používá systém dlaždic x, y, zoom. Zdrojem této vrstvy dlaždice je překrytí meteorologického radaru z [Iowa Environmental Mesonet z Iowa State University](https://mesonet.agron.iastate.edu/ogc/). Při prohlížení radarových dat by v ideálním případě uživatelé jasně viděli štítky měst při navigaci po mapě. Toto chování lze implementovat vložením `labels` vrstvy dlaždic pod vrstvu.

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

Níže je kompletní ukázka spuštěného kódu výše uvedených funkcí.

<br/>

<iframe height='500' scrolling='no' title='Vrstva dlaždic pomocí X, Y a Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na vrstvu dlaždice pera <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>pomocí X, Y a Z</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-tile-layer"></a>Přizpůsobení vrstvy dlaždic

Třída vrstvy dlaždic má mnoho možností stylingu. Zde je nástroj, jak je vyzkoušet.

<br/>

<iframe height='700' scrolling='no' title='Volby vrstvy dlaždic' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>možnosti vrstvy dlaždice</a> pera podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách použitých v tomto článku:

> [!div class="nextstepaction"]
> [Vrstva dlaždic](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Možnosti vrstvy tile](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

Další ukázky kódu, které chcete přidat do map, naleznete v následujících článcích:

> [!div class="nextstepaction"]
> [Přidání vrstvy obrázků](./map-add-image-layer.md)

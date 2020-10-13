---
title: Přidat vrstvu dlaždice do mapy | Mapy Microsoft Azure
description: Naučte se superimpose obrázky na mapách. Podívejte se na příklad, který používá sadu Azure Maps Web SDK k přidání vrstvy dlaždic obsahující dopředný paprskový překryv na mapě.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 8450fbd5287c88431f21753a9c88e8603455f844
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91310420"
---
# <a name="add-a-tile-layer-to-a-map"></a>Přidání vrstvy dlaždic do mapy

V tomto článku se dozvíte, jak překrýt vrstvu dlaždice na mapě. Vrstvy dlaždic vám umožní superimpose obrázky nad Azure Maps dlaždice základní mapy. Další informace o Azure Maps systému skládání naleznete v tématu [úrovně přiblížení a mřížka dlaždic](zoom-levels-and-tile-grid.md).

Vrstva dlaždice se načte do dlaždic ze serveru. Tyto obrázky mohou být buď předem vykresleny, nebo dynamicky vykresleny. Předem vykreslené obrázky jsou uloženy stejně jako jakékoli jiné obrázky na serveru pomocí zásady vytváření názvů, kterou vrstva dlaždice rozumí. Dynamicky vykreslené image používají službu k načtení imagí blízko v reálném čase. Existují tři různé konvence pojmenování dlaždic, které podporuje Azure Maps třídy [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer) : 

* X, Y, zoom Notation-X je sloupec, Y je umístěním dlaždice v mřížce dlaždice a přiblížením je hodnota na základě úrovně přiblížení.
* Quadkey Notation – kombinuje informace x, y a přiblížení do jediné řetězcové hodnoty. Tato řetězcová hodnota se pro jednu dlaždici stala jedinečným identifikátorem.
* Ohraničovací rámeček – určete obrázek v souřadnicích ohraničovacího rámečku: `{west},{south},{east},{north}` . Tento formát se běžně používá ve [službě mapování webu (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer) je skvělý způsob, jak vizualizovat velké datové sady na mapě. Pouze z obrázku lze vytvořit pouze dlaždicovou vrstvu, vektorová data lze také vykreslovat jako dlaždicovou vrstvu. Vykreslováním vektorových dat jako vrstvy dlaždice musí mapový ovládací prvek načíst pouze dlaždice, které jsou menší než velikost souboru, než vektorová data, která představují. Tato technika se běžně používá k vykreslování milionů řádků dat na mapě.

Adresa URL dlaždice předaná vrstvě dlaždice musí být adresa URL protokolu HTTP nebo HTTPS pro prostředek TileJSON nebo šablonu URL dlaždice, která používá následující parametry: 

* `{x}` -X pozice dlaždice. Také potřebuje `{y}` a `{z}` .
* `{y}` -Y pozice dlaždice. Také potřebuje `{x}` a `{z}` .
* `{z}` – Úroveň přiblížení dlaždice Také potřebuje `{x}` a `{y}` .
* `{quadkey}` -Dlaždice quadkey identifikátor založený na konvenci pojmenování systému dlaždice mapy Bing.
* `{bbox-epsg-3857}` – Řetězec ohraničujícího pole ve formátu `{west},{south},{east},{north}` v prostorovém referenčním systému EPSG 3857.
* `{subdomain}` – Zástupný symbol pro hodnoty subdomény, pokud je zadaný, se `subdomain` přidají.
* `{azMapsDomain}` – Zástupný symbol pro zarovnání domény a ověřování žádostí dlaždic se stejnými hodnotami, které používá mapa.

## <a name="add-a-tile-layer"></a>Přidání vrstvy dlaždic

 Tento příklad ukazuje, jak vytvořit dlaždicovou vrstvu, která odkazuje na sadu dlaždic. V této ukázce se používá systém dělení osy x, y a lupy. Zdrojem této vrstvy dlaždic je překrytí paprsky v podobě počasí z [mesonetu Iowa v oblasti životního prostředí Iowa státní školy](https://mesonet.agron.iastate.edu/ogc/). Při prohlížení dat paprsků se ideálním uživatelům jasně zobrazí popisky měst při navigaci na mapě. Toto chování lze implementovat vložením vrstvy dlaždice pod `labels` vrstvou.

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

Níže je uvedená ukázka kompletního spuštění kódu výše uvedené funkce.

<br/>

<iframe height='500' scrolling='no' title='Dlaždice vrstev pomocí X, Y a Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io/azuremaps/pen/BGEQjG/'>Pomocí X, Y a Z</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>se podívejte na vrstvu dlaždice pera.
</iframe>

## <a name="customize-a-tile-layer"></a>Přizpůsobení vrstvy dlaždice

Třída vrstvy dlaždice má mnoho možností stylů. Tady je nástroj pro jejich vyzkoušení.

<br/>

<iframe height='700' scrolling='no' title='Možnosti vrstvy dlaždic' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Viz <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>Možnosti vrstvy dlaždice</a> pera podle Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions)

Další ukázky kódu pro přidání do vašich map najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Přidání vrstvy obrázků](./map-add-image-layer.md)

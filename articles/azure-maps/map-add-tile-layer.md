---
title: Přidat vrstvu dlaždice do mapy | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak překrýt vrstvu dlaždic na mapě pomocí webové sady SDK Microsoft Azure Maps. Vrstvy dlaždic umožňují vykreslování obrázků na mapě.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 83e8f6d684d6d39102fd682653cd19816a9f7b10
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911097"
---
# <a name="add-a-tile-layer-to-a-map"></a>Přidání vrstvy dlaždic do mapy

V tomto článku se dozvíte, jak lze překrýt vrstvu dlaždice na mapě. Vrstvy dlaždic vám umožní superimpose obrázky nad Azure Maps dlaždice základní mapy. Další informace o Azure Maps systému dlaždic najdete v dokumentaci [úrovně přiblížení a mřížka dlaždic](zoom-levels-and-tile-grid.md) .

Vrstva dlaždice se načte do dlaždic ze serveru. Tyto obrázky mohou být buď předem vykresleny a uloženy jako jakákoli jiná bitová kopie na serveru pomocí zásad vytváření názvů, kterou vrstva dlaždice rozumí, nebo dynamické služby, která vytváří bitové kopie za běhu. Existují tři různé konvence pojmenování dlaždic, které podporuje Azure Maps třídy [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) : 

* X, Y, přibližování zápisu na úrovni přiblížení, x je sloupec a Y je pozice dlaždice v mřížce dlaždice.
* Quadkey Notation – kombinace x, y, informace o přiblížení na jednu řetězcovou hodnotu, která je jedinečný identifikátor pro dlaždici.
* Souřadnice ohraničovacího rámečku vázaného rámečku lze použít k určení obrázku ve formátu `{west},{south},{east},{north}`, který se běžně používá službou [mapování webu (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) je skvělý způsob, jak vizualizovat velké datové sady na mapě. Z obrázku lze generovat pouze dlaždicovou vrstvu, ale vektorová data lze také vykreslovat jako dlaždicovou vrstvu. Vykreslováním vektorových dat jako dlaždicovou vrstvou musí mapový ovládací prvek načíst pouze dlaždice, jejichž velikost může být mnohem menší než vektorová data, která představují. Tato technika je používána mnoha uživateli, kteří potřebují vykreslit miliony řádků dat na mapě.

Adresa URL dlaždice předaná do vrstvy dlaždice musí být adresa URL protokolu HTTP/HTTPS pro prostředek TileJSON nebo šablona adresy URL dlaždice, která používá následující parametry: 

* pozice dlaždice `{x}`-X Také musí `{y}` a `{z}`.
* pozice dlaždice `{y}`-Y. Také musí `{x}` a `{z}`.
* `{z}` – úroveň přiblížení dlaždice Také musí `{x}` a `{y}`.
* `{quadkey}` – quadkey identifikátor dlaždice založený na konvenci pojmenování systému dlaždic mapy Bing
* `{bbox-epsg-3857}` – řetězec ohraničujícího pole s formátem `{west},{south},{east},{north}` v prostorovém referenčním systému EPSG 3857.
* `{subdomain}` – zástupný symbol, kde se budou hodnoty subdomény zadat, se přidají.

## <a name="add-a-tile-layer"></a>Přidání vrstvy dlaždic

 Tento příklad ukazuje, jak vytvořit dlaždici vrstvy, která odkazuje na sadu dlaždic, které používají systém x, y a zoom dláždění. Zdrojem této vrstvy dlaždic je překrytí paprsky v podobě počasí z [mesonetu Iowa v oblasti životního prostředí Iowa státní školy](https://mesonet.agron.iastate.edu/ogc/). Při prohlížení dat paprsků by uživatelé měli možnost jasně zobrazit popisky měst při navigaci na mapě, což lze provést vložením vrstvy dlaždice pod `labels`ovou vrstvou.

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

<iframe height='500' scrolling='no' title='Dlaždice vrstev pomocí X, Y a Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io/azuremaps/pen/BGEQjG/'>Pomocí X, Y a Z</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>se podívejte na vrstvu dlaždice pera.
</iframe>

## <a name="customize-a-tile-layer"></a>Přizpůsobení vrstvy dlaždice

Třída vrstvy dlaždice má mnoho možností stylů. Tady je nástroj pro jejich vyzkoušení.

<br/>

<iframe height='700' scrolling='no' title='Možnosti vrstvy dlaždic' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>V <a href='https://codepen.io'>CodePen</a>(<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na se podívejte na Azure Maps <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>Možnosti vrstvy dlaždice</a> pera.
</iframe>

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

Další ukázky kódu pro přidání do vašich map najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Přidat vrstvu obrázku](./map-add-image-layer.md)

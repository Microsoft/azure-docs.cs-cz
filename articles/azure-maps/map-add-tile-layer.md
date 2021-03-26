---
title: Přidat vrstvu dlaždice do mapy | Mapy Microsoft Azure
description: Naučte se superimpose obrázky na mapách. Podívejte se na příklad, který používá sadu Azure Maps Web SDK k přidání vrstvy dlaždic obsahující dopředný paprskový překryv na mapě.
author: rbrundritt
ms.author: richbrun
ms.date: 3/25/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: e0fda77be23f6ea16d5e64b5d4796813c53f0e94
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608098"
---
# <a name="add-a-tile-layer-to-a-map"></a>Přidání vrstvy dlaždic do mapy

V tomto článku se dozvíte, jak překrýt vrstvu dlaždice na mapě. Vrstvy dlaždic vám umožní superimpose obrázky nad Azure Maps dlaždice základní mapy. Další informace o Azure Maps systému skládání naleznete v tématu [úrovně přiblížení a mřížka dlaždic](zoom-levels-and-tile-grid.md).

Vrstva dlaždice se načte do dlaždic ze serveru. Tyto obrázky mohou být buď předem vykresleny, nebo dynamicky vykresleny. Předem vykreslené obrázky jsou uloženy stejně jako jakékoli jiné obrázky na serveru pomocí zásady vytváření názvů, kterou vrstva dlaždice rozumí. Dynamicky vykreslené image používají službu k načtení imagí blízko v reálném čase. Existují tři různé konvence pojmenování dlaždic, které podporuje Azure Maps třídy [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) :

* X, Y, zoom Notation-X je sloupec, Y je umístěním dlaždice v mřížce dlaždice a přiblížením je hodnota na základě úrovně přiblížení.
* Quadkey Notation – kombinuje informace x, y a přiblížení do jediné řetězcové hodnoty. Tato řetězcová hodnota se pro jednu dlaždici stala jedinečným identifikátorem.
* Ohraničovací rámeček – určete obrázek v souřadnicích ohraničovacího rámečku: `{west},{south},{east},{north}` . Tento formát obvykle používají [služby webového mapování (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) je skvělý způsob, jak vizualizovat velké datové sady na mapě. Pouze z obrázku lze vytvořit pouze dlaždicovou vrstvu, vektorová data lze také vykreslovat jako dlaždicovou vrstvu. Vykreslováním vektorových dat jako vrstvy dlaždice musí mapový ovládací prvek načíst pouze dlaždice, které jsou menší než velikost souboru, než vektorová data, která představují. Tato technika se běžně používá k vykreslování milionů řádků dat na mapě.

Adresa URL dlaždice předaná vrstvě dlaždice musí být adresa URL protokolu HTTP nebo HTTPS pro prostředek TileJSON nebo šablonu URL dlaždice, která používá následující parametry:

* `{x}` -X pozice dlaždice. Také potřebuje `{y}` a `{z}` .
* `{y}` -Y pozice dlaždice. Také potřebuje `{x}` a `{z}` .
* `{z}` – Úroveň přiblížení dlaždice Také potřebuje `{x}` a `{y}` .
* `{quadkey}` -Dlaždice quadkey identifikátor založený na konvenci pojmenování systému dlaždice mapy Bing.
* `{bbox-epsg-3857}` – Řetězec ohraničujícího pole ve formátu `{west},{south},{east},{north}` v prostorovém referenčním systému EPSG 3857.
* `{subdomain}` – Zástupný symbol pro hodnoty subdomény, pokud je zadaný, se `subdomain` přidají.
* `{azMapsDomain}` – Zástupný symbol pro zarovnání domény a ověřování žádostí dlaždic se stejnými hodnotami, které používá mapa.

## <a name="add-a-tile-layer"></a>Přidání vrstvy dlaždic

 Tento příklad ukazuje, jak vytvořit dlaždicovou vrstvu, která odkazuje na sadu dlaždic. V této ukázce se používá systém dělení osy x, y a lupy. zdrojem této dlaždice vrstev je [projekt OpenSeaMap](https://openseamap.org/index.php), který obsahuje námořní grafy s přeplněnými zdroji. Při prohlížení dat paprsků se ideálním uživatelům jasně zobrazí popisky měst při navigaci na mapě. Toto chování lze implementovat vložením vrstvy dlaždice pod `labels` vrstvou.

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256,
    minSourceZoom: 7,
    maxSourceZoom: 17
}), 'labels');
```

Níže je uvedená ukázka kompletního spuštění kódu výše uvedené funkce.

<br/>

<iframe height='500' scrolling='no' title='Dlaždice vrstev pomocí X, Y a Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io/azuremaps/pen/BGEQjG/'>Pomocí X, Y a Z</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>se podívejte na vrstvu dlaždice pera.
</iframe>

## <a name="add-an-ogc-web-mapping-service-wms"></a>Přidání služby pro mapování webu (WMS) OGC

Služba Mapování webu (WMTS) je standardem Open Geospatial Consortium (OGC) pro poskytování imagí dat mapy. V tomto formátu je dostupných mnoho otevřených datových sad, které můžete použít s Azure Maps. Tento typ služby lze použít s vrstvou dlaždice, pokud služba podporuje `EPSG:3857` referenční systém souřadnic. Při použití služby WMS nastavte parametry Width a Height na stejnou hodnotu, jakou podporuje služba, a nezapomeňte tuto hodnotu nastavit v `tileSize` Možnosti. Ve formátované adrese URL nastavte `BBOX` Parametr služby s `{bbox-epsg-3857}` zástupným symbolem.

Na následujícím snímku obrazovky vidíte výše uvedený kód, který překrývá webovou službu mapování geografických logických dat z [USA (sadě USGS)](https://mrdata.usgs.gov/) nad mapou, pod štítky.

<br/>

<iframe height="265" style="width: 100%;" scrolling="no" title="WMS – vrstva dlaždice" src="https://codepen.io/azuremaps/embed/BapjZqr?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Podívejte se na <a href='https://codepen.io/azuremaps/pen/BapjZqr'>dlaždici</a> se <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>seCodePenm</a>pera po Azure Maps () na.
</iframe>

## <a name="add-an-ogc-web-mapping-tile-service-wmts"></a>Přidání služby OGC (WMTS) pro mapování webu

Služba WMTS (Web-Mapping Tile Service) je standardem Open Geospatial Consortium (OGC) pro poskytování dlaždicově vycházejících překryvů pro mapy. V tomto formátu je dostupných mnoho otevřených datových sad, které můžete použít s Azure Maps. Tento typ služby lze použít s vrstvou dlaždice, pokud služba podporuje `EPSG:3857` `GoogleMapsCompatible` systém nebo souřadnicový referenční systém (počítačový systém). Pokud používáte službu WMTS, nastavte parametry Width a Height na stejnou hodnotu, kterou služba podporuje, a nezapomeňte tuto hodnotu nastavit v `tileSize` Možnosti. Ve formátované adrese URL nahraďte odpovídajícím způsobem následující zástupné symboly:

* `{TileMatrix}` => `{z}`
* `{TileRow}` => `{y}`
* `{TileCol}` => `{x}`

Na následujícím snímku obrazovky vidíte výše uvedený kód překrytí služby dlaždic mapování webu z obrázku z [národní mapy geografického sadě USGS (USA)](https://viewer.nationalmap.gov/services/) , která se nachází na mapě, pod částmi cesty a popisky.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vrstva dlaždic WMTS" src="https://codepen.io/azuremaps/embed/BapjZVY?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Podívejte se na Azure Maps <a href='https://codepen.io/azuremaps/pen/BapjZVY'>dlaždici WMTS</a> ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) pera na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-tile-layer"></a>Přizpůsobení vrstvy dlaždice

Třída vrstvy dlaždice má mnoho možností stylů. Tady je nástroj pro jejich vyzkoušení.

<br/>

<iframe height='700' scrolling='no' title='Možnosti vrstvy dlaždic' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Viz <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>Možnosti vrstvy dlaždice</a> pera podle Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer)

> [!div class="nextstepaction"]
> [TileLayerOptions](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

Další ukázky kódu pro přidání do vašich map najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Přidání vrstvy obrázků](./map-add-image-layer.md)

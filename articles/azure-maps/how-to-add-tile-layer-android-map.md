---
title: Přidat dlaždicovou vrstvu do map pro Android | Mapy Microsoft Azure
description: Naučte se, jak přidat vrstvu dlaždice na mapu. Podívejte se na příklad, který používá Android SDK Azure Maps k přidání překrytí paprsků počasí na mapu.
author: rbrundritt
ms.author: richbrun
ms.date: 3/25/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: ac37a4e6d68decdf6780560963a0c534689e8dbb
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608971"
---
# <a name="add-a-tile-layer-to-a-map-android-sdk"></a>Přidání vrstvy dlaždice na mapu (Android SDK)

V tomto článku se dozvíte, jak vykreslit vrstvu dlaždice na mapě pomocí Android SDK Azure Maps. Vrstvy dlaždic vám umožní superimpose obrázky nad Azure Maps dlaždice základní mapy. Další informace o Azure Maps systému dlaždic najdete v dokumentaci [úrovně přiblížení a mřížka dlaždic](zoom-levels-and-tile-grid.md) .

Vrstva dlaždice se načte do dlaždic ze serveru. Tyto obrázky mohou být předem vykresleny a uloženy jako jakákoli jiná bitová kopie na serveru pomocí zásady vytváření názvů, kterou vrstva dlaždice rozumí. Nebo tyto obrázky můžete vykreslit pomocí dynamické služby, která vygeneruje obrázky v reálném čase. Existují tři různé konvence pojmenování dlaždic, které podporuje Azure Maps třídy TileLayer:

* X, Y, přibližování zápisu na úrovni přiblížení, x je sloupec a Y je pozice dlaždice v mřížce dlaždice.
* Quadkey Notation – kombinace x, y, informace o přiblížení na jednu řetězcovou hodnotu, která je jedinečný identifikátor pro dlaždici.
* Souřadnice ohraničovacího rámečku ohraničovacího rámečku se dají použít k určení obrázku ve formátu `{west},{south},{east},{north}` , který se běžně používá pro [služby webového mapování (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> TileLayer je skvělý způsob, jak vizualizovat velké datové sady na mapě. Z obrázku lze generovat pouze dlaždicovou vrstvu, ale vektorová data lze také vykreslovat jako dlaždicovou vrstvu. Vykreslováním vektorových dat jako dlaždicovou vrstvou musí ovládací prvek mapy pouze načíst dlaždice, jejichž velikost může být mnohem menší než vektorová data, která představují. Tato technika je používána mnoha uživateli, kteří potřebují vykreslit miliony řádků dat na mapě.

Adresa URL dlaždice předaná do vrstvy dlaždice musí být adresa URL protokolu HTTP/HTTPS pro prostředek TileJSON nebo šablona adresy URL dlaždice, která používá následující parametry:

* `{x}` -X pozice dlaždice. Také potřebuje `{y}` a `{z}` .
* `{y}` -Y pozice dlaždice. Také potřebuje `{x}` a `{z}` .
* `{z}` – Úroveň přiblížení dlaždice Také potřebuje `{x}` a `{y}` .
* `{quadkey}` -Dlaždice quadkey identifikátor založený na konvenci pojmenování systému dlaždice mapy Bing.
* `{bbox-epsg-3857}` – Řetězec ohraničujícího pole ve formátu `{west},{south},{east},{north}` v prostorovém referenčním systému EPSG 3857.
* `{subdomain}` – Zástupný symbol pro hodnoty subdomény, pokud je zadána hodnota subdomény.
* `azmapsdomain.invalid` – Zástupný symbol pro zarovnání domény a ověřování žádostí dlaždic se stejnými hodnotami, které používá mapa. Tuto hodnotu použijte při volání služby dlaždice hostované Azure Maps.

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit proces v tomto článku, je nutné nainstalovat [Azure Maps Android SDK](how-to-use-android-map-control-library.md) , aby se načetla mapa.

## <a name="add-a-tile-layer-to-the-map"></a>Přidat na mapu dlaždicovou vrstvu

Tento příklad ukazuje, jak vytvořit dlaždicovou vrstvu, která odkazuje na sadu dlaždic. V této ukázce se používá systém dlaždic x, y, lupy. Zdrojem této vrstvy dlaždic je [projekt OpenSeaMap](https://openseamap.org/index.php), který obsahuje námořní grafy s přeplněnými zdroji. Často se při prohlížení vrstev dlaždic je žádoucí, aby bylo možné jasně zobrazit popisky měst na mapě. Toto chování lze dosáhnout vložením vrstvy dlaždice pod vrstvy popisku mapy.

::: zone pivot="programming-language-java-android"

```java
TileLayer layer = new TileLayer(
    tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
    opacity(0.8f),
    tileSize(256),
    minSourceZoom(7),
    maxSourceZoom(17)
);

map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
    opacity(0.8f),
    tileSize(256),
    minSourceZoom(7),
    maxSourceZoom(17)
)

map.layers.add(layer, "labels")
```

::: zone-end

Následující snímek obrazovky ukazuje výše uvedený kód, který zobrazuje dlaždici na mapě s tmavým stylem stupňů šedi.

![Mapa Androidu znázorňující dlaždici vrstev](media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)

## <a name="add-an-ogc-web-mapping-service-wms"></a>Přidání služby pro mapování webu (WMS) OGC

Služba Mapování webu (WMTS) je standardem Open Geospatial Consortium (OGC) pro poskytování imagí dat mapy. V tomto formátu je dostupných mnoho otevřených datových sad, které můžete použít s Azure Maps. Tento typ služby lze použít s vrstvou dlaždice, pokud služba podporuje `EPSG:3857` referenční systém souřadnic. Při použití služby WMS nastavte parametry Width a Height na stejnou hodnotu, jakou podporuje služba, a nezapomeňte tuto hodnotu nastavit v `tileSize` Možnosti. Ve formátované adrese URL nastavte `BBOX` Parametr služby s `{bbox-epsg-3857}` zástupným symbolem.

::: zone pivot="programming-language-java-android"

``` java
TileLayer layer = new TileLayer(
    tileUrl("https://mrdata.usgs.gov/services/gscworld?FORMAT=image/png&HEIGHT=1024&LAYERS=geology&REQUEST=GetMap&STYLES=default&TILED=true&TRANSPARENT=true&WIDTH=1024&VERSION=1.3.0&SERVICE=WMS&CRS=EPSG:3857&BBOX={bbox-epsg-3857}"),
    tileSize(1024)
);

map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://mrdata.usgs.gov/services/gscworld?FORMAT=image/png&HEIGHT=1024&LAYERS=geology&REQUEST=GetMap&STYLES=default&TILED=true&TRANSPARENT=true&WIDTH=1024&VERSION=1.3.0&SERVICE=WMS&CRS=EPSG:3857&BBOX={bbox-epsg-3857}"),
    tileSize(1024)
)

map.layers.add(layer, "labels")
```

::: zone-end

Na následujícím snímku obrazovky vidíte výše uvedený kód, který překrývá webovou službu mapování geografických logických dat z [USA (sadě USGS)](https://mrdata.usgs.gov/) nad mapou, pod štítky.

![Mapa Androidu, která zobrazuje vrstvu dlaždice WMS](media/how-to-add-tile-layer-android-map/android-tile-layer-wms.jpg)

## <a name="add-an-ogc-web-mapping-tile-service-wmts"></a>Přidání služby OGC (WMTS) pro mapování webu

Služba WMTS (Web-Mapping Tile Service) je standardem Open Geospatial Consortium (OGC) pro poskytování dlaždicově vycházejících překryvů pro mapy. V tomto formátu je dostupných mnoho otevřených datových sad, které můžete použít s Azure Maps. Tento typ služby lze použít s vrstvou dlaždice, pokud služba podporuje `EPSG:3857` `GoogleMapsCompatible` systém nebo souřadnicový referenční systém (počítačový systém). Pokud používáte službu WMTS, nastavte parametry Width a Height na stejnou hodnotu, kterou služba podporuje, a nezapomeňte tuto hodnotu nastavit v `tileSize` Možnosti. Ve formátované adrese URL nahraďte odpovídajícím způsobem následující zástupné symboly:

* `{TileMatrix}` => `{z}`
* `{TileRow}` => `{y}`
* `{TileCol}` => `{x}`

::: zone pivot="programming-language-java-android"

``` java
TileLayer layer = new TileLayer(
    tileUrl("https://basemap.nationalmap.gov/arcgis/rest/services/USGSImageryOnly/MapServer/WMTS/tile/1.0.0/USGSImageryOnly/default/GoogleMapsCompatible/{z}/{y}/{x}"),
    tileSize(256),
    bounds(-173.25000107492872, 0.0005794121990209753, 146.12527718104752, 71.506811402077),
    maxSourceZoom(18)
);

map.layers.add(layer, "transit");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://basemap.nationalmap.gov/arcgis/rest/services/USGSImageryOnly/MapServer/WMTS/tile/1.0.0/USGSImageryOnly/default/GoogleMapsCompatible/{z}/{y}/{x}"),
    tileSize(256),
    bounds(-173.25000107492872, 0.0005794121990209753, 146.12527718104752, 71.506811402077),
    maxSourceZoom(18)
)

map.layers.add(layer, "transit")
```

::: zone-end

Na následujícím snímku obrazovky vidíte výše uvedený kód překrytí služby dlaždic mapování webu z obrázku z [národní mapy geografického sadě USGS (USA)](https://viewer.nationalmap.gov/services/) , která se nachází na mapě, pod částmi cesty a popisky.

![Mapa Androidu zobrazující vrstvu dlaždice WMTS](media/how-to-add-tile-layer-android-map/android-tile-layer-wmts.jpg)

## <a name="next-steps"></a>Další kroky

V následujícím článku se dozvíte více o způsobech překrytí snímků na mapě.

> [!div class="nextstepaction"]
> [Vrstva obrázku](map-add-image-layer-android.md)

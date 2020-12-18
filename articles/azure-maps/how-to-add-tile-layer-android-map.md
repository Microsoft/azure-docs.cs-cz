---
title: Přidat dlaždicovou vrstvu do map pro Android | Mapy Microsoft Azure
description: Naučte se, jak přidat vrstvu dlaždice na mapu. Podívejte se na příklad, který používá Android SDK Azure Maps k přidání překrytí paprsků počasí na mapu.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 8ea6f44c47c5cd4d223b053640f65827f46db482
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679287"
---
# <a name="add-a-tile-layer-to-a-map-android-sdk"></a>Přidání vrstvy dlaždice na mapu (Android SDK)

V tomto článku se dozvíte, jak vykreslit vrstvu dlaždice na mapě pomocí Android SDK Azure Maps. Vrstvy dlaždic vám umožní superimpose obrázky nad Azure Maps dlaždice základní mapy. Další informace o Azure Maps systému dlaždic najdete v dokumentaci [úrovně přiblížení a mřížka dlaždic](zoom-levels-and-tile-grid.md) .

Vrstva dlaždice se načte do dlaždic ze serveru. Tyto obrázky mohou být předem vykresleny a uloženy jako jakákoli jiná bitová kopie na serveru pomocí zásady vytváření názvů, kterou vrstva dlaždice rozumí. Nebo tyto obrázky můžete vykreslit pomocí dynamické služby, která vygeneruje obrázky v reálném čase. Existují tři různé konvence pojmenování dlaždic, které podporuje Azure Maps třídy TileLayer:

* X, Y, přibližování zápisu na úrovni přiblížení, x je sloupec a Y je pozice dlaždice v mřížce dlaždice.
* Quadkey Notation – kombinace x, y, informace o přiblížení na jednu řetězcovou hodnotu, která je jedinečný identifikátor pro dlaždici.
* Souřadnice ohraničovacího rámečku ohraničovacího rámečku se dají použít k určení obrázku ve formátu `{west},{south},{east},{north}` , který se běžně používá ve [službě mapování webu (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> TileLayer je skvělý způsob, jak vizualizovat velké datové sady na mapě. Z obrázku lze generovat pouze dlaždicovou vrstvu, ale vektorová data lze také vykreslovat jako dlaždicovou vrstvu. Vykreslováním vektorových dat jako dlaždicovou vrstvou musí ovládací prvek mapy pouze načíst dlaždice, jejichž velikost může být mnohem menší než vektorová data, která představují. Tato technika je používána mnoha uživateli, kteří potřebují vykreslit miliony řádků dat na mapě.

Adresa URL dlaždice předaná do vrstvy dlaždice musí být adresa URL protokolu HTTP/HTTPS pro prostředek TileJSON nebo šablona adresy URL dlaždice, která používá následující parametry: 

* `{x}` -X pozice dlaždice. Také potřebuje `{y}` a `{z}` .
* `{y}` -Y pozice dlaždice. Také potřebuje `{x}` a `{z}` .
* `{z}` – Úroveň přiblížení dlaždice Také potřebuje `{x}` a `{y}` .
* `{quadkey}` -Dlaždice quadkey identifikátor založený na konvenci pojmenování systému dlaždice mapy Bing.
* `{bbox-epsg-3857}` – Řetězec ohraničujícího pole ve formátu `{west},{south},{east},{north}` v prostorovém referenčním systému EPSG 3857.
* `{subdomain}` – Zástupný symbol pro hodnoty subdomény, pokud je zadána hodnota subdomény.

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit proces v tomto článku, je nutné nainstalovat [Azure Maps Android SDK](how-to-use-android-map-control-library.md) , aby se načetla mapa.

## <a name="add-a-tile-layer-to-the-map"></a>Přidat na mapu dlaždicovou vrstvu

Tento příklad ukazuje, jak vytvořit dlaždicovou vrstvu, která odkazuje na sadu dlaždic. V této ukázce se používá systém dlaždic x, y, lupy. Zdrojem této vrstvy dlaždic je [projekt OpenSeaMap](https://openseamap.org/index.php), který obsahuje námořní grafy s přeplněnými zdroji. Často se při prohlížení vrstev dlaždic je žádoucí, aby bylo možné jasně zobrazit popisky měst na mapě. Toto chování lze dosáhnout vložením vrstvy dlaždice pod vrstvy popisku mapy.

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

Následující snímek obrazovky ukazuje výše uvedený kód, který zobrazuje dlaždici na mapě s tmavým stylem stupňů šedi.

![Mapa Androidu znázorňující dlaždici vrstev](media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak nastavit styly mapy, najdete v následujícím článku.

> [!div class="nextstepaction"]
> [Změna stylu mapy](set-android-map-styles.md)

> [!div class="nextstepaction"]
> [Přidat Heat mapu](map-add-heat-map-layer-android.md)

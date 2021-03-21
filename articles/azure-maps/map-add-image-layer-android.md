---
title: Přidat vrstvu obrázku do mapy Androidu | Mapy Microsoft Azure
description: Přečtěte si, jak přidat obrázky na mapu. Viz jak použít Android SDK Azure Maps k přizpůsobení vrstev obrázků a překryvných obrázků v pevně stanovených sadách souřadnic.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: e1d99297c0357039606149bdf7e5a526258fc7c5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102054556"
---
# <a name="add-an-image-layer-to-a-map-android-sdk"></a>Přidat vrstvu obrázku na mapu (Android SDK)

V tomto článku se dozvíte, jak překrýt obrázek do pevné sady souřadnic. Tady je několik příkladů různých typů imagí, které je možné v mapách překrývají:

* Obrázky zachycené z DRONY zachraňují životy
* Sestavování floorplans
* Historické nebo jiné specializované image map
* Modrotisky webů úloh

> [!TIP]
> Vrstva obrázku představuje snadný způsob, jak překrýt obrázek na mapě. Pamatujte, že velké obrázky můžou spotřebovat spoustu paměti a potenciálně způsobovat problémy s výkonem. V takovém případě zvažte rozdělení obrázku do dlaždic a jejich načtení do mapy jako dlaždicovou vrstvu.

## <a name="add-an-image-layer"></a>Přidání vrstvy obrázků

Následující kód překrývá obrázek [mapy newyorském, New Jersey, z 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) na mapě. Tato bitová kopie se přidá do `drawable` složky projektu. Vrstva obrázku je vytvořena nastavením obrázku a souřadnic pro čtyři rohy ve formátu `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]` . Přidávání vrstev obrázků pod `label` vrstvou je často žádoucí.

::: zone pivot="programming-language-java-android"

```java
//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(
        new Position[] {
            new Position(-74.22655, 40.773941), //Top Left Corner
            new Position(-74.12544, 40.773941), //Top Right Corner
            new Position(-74.12544, 40.712216), //Bottom Right Corner
            new Position(-74.22655, 40.712216)  //Bottom Left Corner
        }
    ),
    setImage(R.drawable.newark_nj_1922)
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(
        arrayOf<Position>(
            Position(-74.22655, 40.773941),  //Top Left Corner
            Position(-74.12544, 40.773941),  //Top Right Corner
            Position(-74.12544, 40.712216),  //Bottom Right Corner
            Position(-74.22655, 40.712216)   //Bottom Left Corner
        )
    ),
    setImage(R.drawable.newark_nj_1922)
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

Alternativně lze zadat adresu URL k imagi hostované v online režimu. Pokud ale váš scénář umožňuje, přidejte image do `drawable` složky projekty, která se načte rychleji, protože bitová kopie bude k dispozici místně a nebude nutné ji stáhnout.

::: zone pivot="programming-language-java-android"

```java
//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(
        new Position[] {
            new Position(-74.22655, 40.773941), //Top Left Corner
            new Position(-74.12544, 40.773941), //Top Right Corner
            new Position(-74.12544, 40.712216), //Bottom Right Corner
            new Position(-74.22655, 40.712216)  //Bottom Left Corner
        }
    ),
    setUrl("https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg")
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(
        arrayOf<Position>(
            Position(-74.22655, 40.773941),  //Top Left Corner
            Position(-74.12544, 40.773941),  //Top Right Corner
            Position(-74.12544, 40.712216),  //Bottom Right Corner
            Position(-74.22655, 40.712216) //Bottom Left Corner
        )
    ),
    setUrl("https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg")
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

Následující snímek obrazovky ukazuje mapu newyorském, New Jersey, od 1922 překrytí pomocí vrstvy obrázku.

![Mapa newyorském, New Jersey, ze 1922 překrytí pomocí vrstvy obrázku](media/map-add-image-layer-android/android-image-layer.gif)

## <a name="import-a-kml-file-as-ground-overlay"></a>Import souboru KML jako překryvné desky

Tento příklad ukazuje, jak přidat informace o překrytí základní desky KML jako vrstvu obrázku na mapě. KML překryvná deska poskytují souřadnice sever, jih, východní a západní délky a rotace po směru hodinových ručiček. Ale vrstva obrázku očekává souřadnice pro každý roh obrázku. Překrytí základní desky KML v této ukázce je pro Cathedral Chartres a je nasource z [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

```xml
<?xml version="1.0" encoding="UTF-8"?>
<kml xmlns="http://www.opengis.net/kml/2.2" xmlns:gx="http://www.google.com/kml/ext/2.2" xmlns:kml="http://www.opengis.net/kml/2.2" xmlns:atom="http://www.w3.org/2005/Atom">
<GroundOverlay>
    <name>Map of Chartres cathedral</name>
    <Icon>
        <href>https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png</href>
        <viewBoundScale>0.75</viewBoundScale>
    </Icon>
    <LatLonBox>
        <north>48.44820923628113</north>
        <south>48.44737203258976</south>
        <east>1.488833825534365</east>
        <west>1.486788581643038</west>
        <rotation>46.44067597839695</rotation>
    </LatLonBox>
</GroundOverlay>
</kml>
```

Kód používá statickou `getCoordinatesFromEdges` metodu z `ImageLayer` třídy. Tato metoda vypočítá čtyři rohy obrázku pomocí informací o Severní, Jižní, východní, západní a rotaci překrytí základní desky KML.

::: zone pivot="programming-language-java-android"

```java
//Calculate the corner coordinates of the ground overlay.
Position[] corners = ImageLayer.getCoordinatesFromEdges(
    //North, south, east, west
    48.44820923628113, 48.44737203258976, 1.488833825534365, 1.486788581643038,

    //KML rotations are counter-clockwise, subtract from 360 to make them clockwise.
    360 - 46.44067597839695
);

//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(corners),
    setUrl("https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png")
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Calculate the corner coordinates of the ground overlay.
val corners: Array<Position> =
    ImageLayer.getCoordinatesFromEdges( //North, south, east, west
        48.44820923628113,
        48.44737203258976,
        1.488833825534365,
        1.486788581643038,  //KML rotations are counter-clockwise, subtract from 360 to make them clockwise.
        360 - 46.44067597839695
    )

//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(corners),
    setUrl("https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png")
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

Na následujícím snímku obrazovky vidíte mapu s překrytím KML základní desky pomocí vrstvy obrazu.

![Mapování s překrytím na základní desku KML s použitím vrstvy obrázku](media/map-add-image-layer-android/android-ground-overlay.jpg)

> [!TIP]
> Použijte `getPixels` metody a `getPositions` třídy obrazové vrstvy pro převod mezi geografickými souřadnicemi umístěné vrstvy obrazu a souřadnicí obrazových bodů místní obrázek.

## <a name="next-steps"></a>Další kroky

V následujícím článku se dozvíte více o způsobech překrytí snímků na mapě.

> [!div class="nextstepaction"]
> [Přidání vrstvy dlaždic](how-to-add-tile-layer-android-map.md)
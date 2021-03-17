---
title: Nastavení stylu mapy v zařízeních s Androidem Maps | Mapy Microsoft Azure
description: Přečtěte si dva způsoby nastavení stylu mapy. Chcete-li upravit styl, přečtěte si téma jak použít Azure Maps Android SDK v souboru rozložení nebo třídě Activity.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: aef8fbacf8302fb5dd4b5fe28afc615c6bf56090
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100980"
---
# <a name="set-map-style-android-sdk"></a>Nastavit styl mapy (Android SDK)

Tento článek ukazuje dva způsoby, jak nastavit styly mapy pomocí Azure Maps Android SDK. Azure Maps má šest různých stylů mapy, ze kterých si můžete vybrat. Další informace o podporovaných stylech map najdete [v tématu Podporované styly mapy v Azure Maps](supported-map-styles.md).

## <a name="prerequisites"></a>Požadavky

Ujistěte se, že jste dokončili kroky v [rychlém startu: vytvoření dokumentu aplikace pro Android](quick-android-map.md) .

## <a name="set-map-style-in-the-layout"></a>Nastavení stylu mapy v rozložení

Při přidávání mapového ovládacího prvku lze v souboru rozložení pro třídu Activity nastavit styl mapy. Následující kód nastaví střed umístění, úroveň přiblížení a styl mapy.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/mapcontrol"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_centerLat="47.602806"
    app:mapcontrol_centerLng="-122.329330"
    app:mapcontrol_zoom="12"
    app:mapcontrol_style="grayscale_dark"
    />
```

Na následujícím snímku obrazovky vidíte výše uvedený kód, který zobrazuje mapu s tmavým stylem ve stupních šedi.

![Mapa s tmavou silniční mapou ve stupních šedi](media/set-android-map-styles/android-grayscale-dark.png)

## <a name="set-map-style-in-code"></a>Nastavení stylu mapy v kódu

Styl mapy lze nastavit programově v kódu pomocí `setStyle` metody mapy. Následující kód nastaví střed umístění a úroveň přiblížení pomocí `setCamera` metody map a stylu mapy na `SATELLITE_ROAD_LABELS` .

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {

    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-122.33, 47.64)), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE_ROAD_LABELS));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-122.33, 47.64)), zoom(14))

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE_ROAD_LABELS))
}
```

::: zone-end

Na následujícím snímku obrazovky vidíte výše uvedený kód, který zobrazuje mapu se stylem satelitních cest.

![Mapování se stylem satelitních cest k popiskům](media/set-android-map-styles/android-satellite-road-labels.png)

## <a name="setting-the-map-camera"></a>Nastavení videokamery mapy

Mapová kamera řídí, jaká část mapy se zobrazuje na mapě. Fotoaparát může být v rozložení programově v kódu. Při nastavování v kódu existují dvě hlavní metody nastavení pozice mapy; použití Center a přiblížení nebo předání v ohraničujícím poli. Následující kód ukazuje, jak nastavit všechny volitelné možnosti kamery při použití `center` a `zoom` .

::: zone pivot="programming-language-java-android"

```java
//Set the camera of the map using center and zoom.
map.setCamera(
    center(Point.fromLngLat(-122.33, 47.64)), 

    //The zoom level. Typically a value between 0 and 22.
    zoom(14),

    //The amount of tilt in degrees the map where 0 is looking straight down.
    pitch(45),

    //Direction the top of the map is pointing in degrees. 0 = North, 90 = East, 180 = South, 270 = West
    bearing(90),

    //The minimum zoom level the map will zoom-out to when animating from one location to another on the map.
    minZoom(10),
    
    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Set the camera of the map using center and zoom.
map.setCamera(
    center(Point.fromLngLat(-122.33, 47.64)), 

    //The zoom level. Typically a value between 0 and 22.
    zoom(14),

    //The amount of tilt in degrees the map where 0 is looking straight down.
    pitch(45),

    //Direction the top of the map is pointing in degrees. 0 = North, 90 = East, 180 = South, 270 = West
    bearing(90),

    //The minimum zoom level the map will zoom-out to when animating from one location to another on the map.
    minZoom(10),
    
    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
)
```

::: zone-end

Často je žádoucí soustředit se na mapu v sadě dat. Ohraničující rámeček lze vypočítat z funkcí pomocí `MapMath.fromData` metody a lze jej předat do `bounds` Možnosti na mapě. Při nastavování zobrazení mapy založeného na ohraničujícím poli je často užitečné zadat `padding` hodnotu pro určení velikosti pixelu pro body vykreslené jako bubliny nebo symboly. Následující kód ukazuje, jak nastavit všechny volitelné možnosti kamery při použití ohraničujícího pole k nastavení pozice kamery.

::: zone pivot="programming-language-java-android"

```java
//Set the camera of the map using a bounding box.
map.setCamera(
    //The area to focus the map on.
    bounds(BoundingBox.fromLngLats(
        //West
        -122.4594,

        //South
        47.4333,
        
        //East
        -122.21866,
        
        //North
        47.75758
    )),

    //Amount of pixel buffer around the bounding box to provide extra space around the bounding box.
    padding(20),

    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Set the camera of the map using a bounding box.
map.setCamera(
    //The area to focus the map on.
    bounds(BoundingBox.fromLngLats(
        //West
        -122.4594,

        //South
        47.4333,
        
        //East
        -122.21866,
        
        //North
        47.75758
    )),

    //Amount of pixel buffer around the bounding box to provide extra space around the bounding box.
    padding(20),

    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
)
```

::: zone-end

Všimněte si, že poměr stran ohraničujícího pole nesmí být stejný jako poměr stran mapy, protože tato mapa často zobrazuje celou oblast ohraničujícího pole, ale často je příliš svislá nebo horizontálně.

## <a name="next-steps"></a>Další kroky

Další ukázky kódu pro přidání do vašich map najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Přidání vrstvy symbolů](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy bublin](map-add-bubble-layer-android.md)

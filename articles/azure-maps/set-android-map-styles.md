---
title: Mapování funkce styl ve službě Azure Maps | Dokumentace Microsoftu
description: Další informace o Azure Maps styl související funkce pro sadu Android SDK.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3c8c5d4bae16d8e15c8f2c5b1cc8e00eb14e4ce3
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870969"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Nastavit styl mapy pomocí Azure Maps Android SDK

Tento článek ukazuje dva způsoby, jak nastavit styly mapy pomocí Azure Maps Android SDK. Azure Maps obsahuje šest různých map stylů lze vybírat. Další informace o podporovaných Mapovat styly, naleznete v tématu [podporován styly mapy ve službě Azure Maps](./supported-map-styles.md).


## <a name="prerequisites"></a>Požadavky

K dokončení procesu v tomto článku, je potřeba nainstalovat [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) načíst mapu.


## <a name="set-map-style-in-the-layout"></a>Nastavit styl mapu v rozložení

Styl mapy můžete nastavit v souboru rozložení pro vaši třídu aktivity. Upravit **res > rozložení > activity_main.xml**, takže bude vypadat jako následující:

```XML
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    >

    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/mapcontrol"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
        app:mapcontrol_style="grayscale_dark"
        />

</FrameLayout>
```

`mapcontrol_style` Výše uvedený atribut nastaví styl mapy **grayscale_dark**. 

<center>

![style-grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>Nastavit styl mapu v třídě aktivity

Styl mapy je možné nastavit v třídy activity. Zkopírujte následující fragment kódu do **onCreate()** metodu vaše `MainActivity.java` třídy. Toto nastaví styl mapy **satellite_road_labels**.

```Java
    mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![style-satellite-road-labels](./media/set-android-map-styles/satellite-road-labels.png)</center>
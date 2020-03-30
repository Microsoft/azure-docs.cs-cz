---
title: Nastavení stylu mapy pomocí sady Azure Maps Android SDK| Mapy Microsoft Azure
description: V tomto článku se dozvíte o funkcích souvisejících se stylem Microsoft Azure Maps pro sadu Android SDK.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: a4d761abf54682ed0263922d0a118debc9eccf0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334355"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Nastavení stylu mapy pomocí sady Azure Maps Android SDK

Tento článek ukazuje dva způsoby, jak nastavit styly mapy pomocí Azure Maps Android SDK. Azure Maps má na výběr ze šesti různých stylů map. Další informace o podporovaných stylech map najdete [v tématu Podporované styly map v Azure Maps](./supported-map-styles.md).


## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit proces v tomto článku, je třeba nainstalovat [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) načíst mapu.


## <a name="set-map-style-in-the-layout"></a>Nastavení stylu mapy v rozvržení

Styl mapy můžete nastavit v souboru rozložení pro třídu aktivity. Upravit **res > rozložení > activity_main.xml**, takže to vypadá jako ten níže:

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

Výše `mapcontrol_style` uvedený atribut nastaví styl mapy na **grayscale_dark**. 

<center>

![styl-grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>Nastavení stylu mapy ve třídě aktivity

Styl mapy lze nastavit ve třídě aktivity. Zkopírujte následující fragment kódu do metody **onCreate()** vaší `MainActivity.java` třídy. Tento kód nastaví styl mapy na **satellite_road_labels**.

```Java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![styl-satelitní-silniční štítky](./media/set-android-map-styles/satellite-road-labels.png)</center>
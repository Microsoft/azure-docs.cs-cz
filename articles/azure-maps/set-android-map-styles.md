---
title: Funkce stylů mapy v Azure Maps | Microsoft Docs
description: Přečtěte si o funkcích Azure Maps souvisejících s Style pro Android SDK.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 5bcc73c63e7c9d804d01df98551aa51b81d98d07
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844845"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Nastavení stylu mapy pomocí Azure Maps Android SDK

Tento článek ukazuje dva způsoby, jak nastavit styly mapy pomocí Azure Maps Android SDK. Azure Maps má šest různých stylů mapy, ze kterých si můžete vybrat. Další informace o podporovaných stylech map najdete [v tématu Podporované styly mapy v Azure Maps](./supported-map-styles.md).


## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit proces v tomto článku, je nutné nainstalovat [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) , aby se načetla mapa.


## <a name="set-map-style-in-the-layout"></a>Nastavení stylu mapy v rozložení

Můžete nastavit styl mapy v souboru rozložení pro třídu Activity. Upravte **> rozložení pro res > activity_main. XML**, takže vypadá takto:

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

Atribut výše nastaví styl mapy na grayscale_dark. `mapcontrol_style` 

<center>

![style-grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>Nastavit styl mapy ve třídě Activity

Styl mapy lze nastavit ve třídě Activity. Zkopírujte následující fragment kódu do metody **Create ()** vaší `MainActivity.java` třídy. Tím se nastaví styl mapy na **satellite_road_labels**.

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
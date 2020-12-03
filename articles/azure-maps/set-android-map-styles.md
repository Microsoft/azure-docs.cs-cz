---
title: Nastavení stylu mapy pomocí Azure Maps Android SDK
description: Přečtěte si dva způsoby nastavení stylu mapy. Chcete-li upravit styl, přečtěte si téma jak použít Microsoft Azure Maps Android SDK v souboru rozložení nebo třídě Activity.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8c7689fb87575ac6e150f793b43f35e8bf6adc83
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532467"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Nastavení stylu mapy pomocí Azure Maps Android SDK

V tomto článku se dozvíte, jak nastavit styly mapy pomocí Android SDK Azure Maps. Azure Maps má šest různých stylů mapy, ze kterých si můžete vybrat. Další informace o podporovaných stylech map najdete [v tématu Podporované styly mapy v Azure Maps](./supported-map-styles.md).

## <a name="prerequisites"></a>Předpoklady

1. [Vytvořit účet Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Získejte primární klíč předplatného](quick-demo-map-app.md#get-the-primary-key-for-your-account), označovaný také jako primární klíč nebo klíč předplatného.
3. Stáhněte a nainstalujte [Azure Maps Android SDK](./how-to-use-android-map-control-library.md).


## <a name="set-map-style-in-the-layout"></a>Nastavení stylu mapy v rozložení

Můžete nastavit styl mapy v souboru rozložení pro třídu Activity. Upravte `res > layout > activity_main.xml` , takže vypadá takto:

```XML
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

`mapcontrol_style`Atribut výše nastaví styl mapy na **grayscale_dark**.

:::image type="content" source="./media/set-android-map-styles/grayscale-dark.png" border="true" alt-text="Azure Maps, obrázek mapy znázorňující styl jako grayscale_dark":::

## <a name="set-map-style-in-the-mainactivity-class"></a>Nastavení stylu mapy ve třídě MainActivity

Styl mapy lze také nastavit ve třídě MainActivity. Otevřete `java > com.example.myapplication > MainActivity.java` soubor a zkopírujte následující fragment kódu do metody **Create ()** . Tento kód nastaví styl mapy na **satellite_road_labels**.

>[!WARNING]
>Android Studio pravděpodobně neimportoval požadované třídy.  V důsledku toho bude mít kód nějaké nepřeložitelné odkazy. Chcete-li importovat požadované třídy, Stačí umístit ukazatel myši na každý nerozpoznaný odkaz a stisknout `Alt + Enter` (možnost + návrat na Macu).

```Java
mapControl.onReady(map -> {

    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle((style(SATELLITE_ROAD_LABELS)));
       
});
```

:::image type="content" source="./media/set-android-map-styles/satellite-road-labels.png" border="true" alt-text="Azure Maps, obrázek mapy znázorňující styl jako satellite_road_labels":::
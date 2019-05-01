---
title: Jak používat s Androidem mapový ovládací prvek ve službě Azure Maps | Dokumentace Microsoftu
description: Android mapový ovládací prvek ve službě Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 15706addbe6b7f6310223978130158c792a47c89
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60770329"
---
# <a name="how-to-use-the-azure-maps-android-sdk"></a>Jak používat Azure Maps Android SDK

Azure Maps Android SDK je knihovna vektorové mapy pro Android. Tento článek vás provede procesy, které instalace sady Android SDK Azure Maps, načítání mapu a uvedení na mapě kódu pin.

## <a name="prerequisites"></a>Požadavky

### <a name="create-an-azure-maps-account"></a>Vytvoření účtu Azure Maps

K dokončení postupu v tomto článku, musíte nejprve [vytvořit účet Azure Maps](how-to-manage-account-keys.md) v cenovou úroveň S1.

### <a name="download-android-studio"></a>Stáhnout Android Studio

Budete muset stáhnout Android Studio a vytvořte projekt pomocí prázdná aktivita před instalací sady Azure Maps Android SDK. Je možné [stáhnout Android Studio](https://developer.android.com/studio/) zdarma z Googlu. 

## <a name="create-a-project-in-android-studio"></a>Vytvoření projektu v nástroji Android Studio

Nejprve musíte vytvořit nový projekt s prázdná aktivita. Dokončete tyto kroky pro vytvoření projektu Android Studio:

1. V části **zvolte projekt**vyberte **telefony a tablety**. Vaše aplikace poběží v tomto provedení.
2. Na **telefony a tablety** kartu, vyberte možnost **prázdná aktivita**a pak vyberte **Další**.
3. V části **nakonfigurujete svůj projekt**vyberte `API 21: Android 5.0.0 (Lollipop)` jako minimální sadu SDK. Toto je první verze podporovaná Azure Maps Android SDK.
4. Přijměte výchozí nastavení `Activity Name` a `Layout Name` a vyberte **Dokončit**.

Zobrazit [dokumentace k sadě Android Studio](https://developer.android.com/studio/intro/) další pomoc s instalací Android Studio a vytvoření nového projektu.

![Vytvoření projektu](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Nastavit virtuální zařízení

Android Studio umožňuje nastavit virtuální zařízení s Androidem ve vašem počítači. To vám může pomoct testování aplikací během vývoje. Zřídit virtuální zařízení, vyberte ikonu Android Virtual Device (AVD) Manager v pravém horním rohu obrazovky projektu a pak vyberte **vytvořit virtuální zařízení**. Můžete také získat AVD Manager tak, že vyberete **nástroje** > **Android** > **AVD Manager** z panelu nástrojů. V **telefony** vyberte **Nexus 5 X**a pak vyberte **Další**.

Další informace o nastavení AVD v [dokumentace k sadě Android Studio](https://developer.android.com/studio/run/managing-avds).

![Emulátor androidu](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Instalace sady Android SDK Azure Maps

Dalším krokem při sestavování aplikace je instalace Azure Maps Android SDK. Dokončete tyto kroky pro instalaci sady SDK:

1. Přidejte následující kód, který **všechny projekty**, **úložišť** blok, ve vaší **build.gradle** souboru.

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Aktualizace vašeho **app/build.gradle** a přidejte do ní následující kód:

    1. Přidejte následující kód pro Android bloku:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    2. Aktualizujte vaše bloku závislostí a přidejte do ní následující kód:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.1"
        ```

3. Nastavení oprávnění tak, že přidáte následující kód XML pro vaše **AndroidManifest.xml** souboru:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <manifest>
        ...
        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
        ...
    </manifest>
    ```

4. Upravit **res** > **rozložení** > **activity_main.xml** tak, aby vypadala podobně jako tato konfigurace XML:
    
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
            app:mapcontrol_cameraTargetLat="47.64"
            app:mapcontrol_cameraTargetLng="-122.33"
            app:mapcontrol_cameraZoom="12"
            />

    </FrameLayout>
    ```

5. Upravit **MainActivity.java** pro vytvoření třídy aktivity zobrazení mapy. Po úpravě, by měl vypadat podobně jako tato třída:

    ```java
    package com.example.myapplication;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
        
        static {
            AzureMaps.setSubscriptionKey("{subscription-key}");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);

        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }

        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }

        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }

        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }

        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }

    }

    ```

## <a name="import-classes"></a>Import tříd

Po dokončení předchozích kroků, pravděpodobně dostanete upozornění z Android Studio o některý kód. Chcete-li vyřešit tato upozornění, importujte třídy odkazuje `MainActivity.java`.

Tyto třídy můžete automaticky importovat tak, že vyberete kombinaci kláves Alt + Enter (možnost + Return na počítači Mac).

Tlačítka Spustit vyberte, jak ukazuje následující obrázek (nebo stiskněte klávesu ovládací prvek + R na počítači Mac), k sestavení aplikace.

![Klikněte na tlačítko Spustit](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio bude trvat několik sekund pro sestavení aplikace. Aplikaci můžete otestovat v emulované zařízení s Androidem, po dokončení sestavení. Měli byste vidět mapu podobné následující:

![Android mapy](./media/how-to-use-android-map-control-library/android-map.png)

## <a name="add-a-marker-to-the-map"></a>Přidat značku k mapě

Chcete-li přidat značky do mapy, přidejte `mapView.getMapAsync()` funkce `MainActivity.java`. Finální `MainActivity.java` kód by měl vypadat takto:

```java
package com.example.myapplication;

import android.app.Activity;
import android.os.Bundle;
import com.mapbox.geojson.Feature;
import com.mapbox.geojson.Point;
import com.microsoft.azure.maps.mapcontrol.AzureMaps;
import com.microsoft.azure.maps.mapcontrol.MapControl;
import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
import com.microsoft.azure.maps.mapcontrol.source.DataSource;
import static com.microsoft.azure.maps.mapcontrol.options.SymbolLayerOptions.iconImage;
public class MainActivity extends AppCompatActivity {
    
    static{
            AzureMaps.setSubscriptionKey("{subscription-key}");
        }

    MapControl mapControl;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mapControl = findViewById(R.id.mapcontrol);

        mapControl.onCreate(savedInstanceState);

        mapControl.getMapAsync(map -> {
            DataSource dataSource = new DataSource();
            dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

            SymbolLayer symbolLayer = new SymbolLayer(dataSource);
            symbolLayer.setOptions(iconImage("my-icon"));

            map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            map.sources.add(dataSource);
            map.layers.add(symbolLayer);
        });
    }

    @Override
    public void onStart() {
        super.onStart();
        mapControl.onStart();
    }

    @Override
    public void onResume() {
        super.onResume();
        mapControl.onResume();
    }

    @Override
    public void onPause() {
        super.onPause();
        mapControl.onPause();
    }

    @Override
    public void onStop() {
        super.onStop();
        mapControl.onStop();
    }

    @Override
    public void onLowMemory() {
        super.onLowMemory();
        mapControl.onLowMemory();
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        mapControl.onDestroy();
    }

    @Override
    protected void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);
        mapControl.onSaveInstanceState(outState);
    }
}
```

Spusťte aplikaci znovu. Značky byste měli vidět na mapě, jak je znázorněno zde:

![Android mapy kódu pin](./media/how-to-use-android-map-control-library/android-map-pin.png)
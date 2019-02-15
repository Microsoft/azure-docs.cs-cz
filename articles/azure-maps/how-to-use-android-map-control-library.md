---
title: Jak používat s Androidem mapový ovládací prvek ve službě Azure Maps | Dokumentace Microsoftu
description: Pomocí Android mapový ovládací prvek ve službě Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: c1258ab619b165553c55481ad67d663be65ac832
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56269599"
---
# <a name="how-to-use-azure-maps-android-sdk"></a>Jak používat Azure Maps Android SDK

Azure Maps Android SDK je knihovna vektorové mapy pro Android. Tento článek vás provede procesem instalace sady Android SDK Azure Maps, načítání mapy a uvedení PIN kód na něm.

## <a name="prerequisites-to-get-started"></a>Požadavky na Začínáme

### <a name="create-an-azure-maps-account"></a>Vytvoření účtu Azure Maps 

Postupovat podle kroků v tomto průvodci, musíte nejprve zobrazíte [spravovat účet a klíče](how-to-manage-account-keys.md) k vytváření a správě účtu předplatného S1 pro cenovou úroveň.

### <a name="download-android-studio"></a>Stáhnout Android studio

Můžete si stáhnout [Android Studio](https://developer.android.com/studio/) zdarma z Googlu. Pokud chcete nainstalovat Azure Maps Android SDK, musíte nejdřív stáhnout Android Studio a vytvořte projekt pomocí prázdná aktivita.

## <a name="create-a-project-in-android-studio"></a>Vytvoření projektu v nástroji Android Studio

Budete muset vytvořit nový projekt s prázdná aktivita. Postupujte podle pokynů můžete vytvořit nový projekt Android Studio:

1. V části *zvolte projekt*, zaškrtněte políčko "Telefonu a tabletu" jako faktor formuláře, který vaše aplikace poběží.
2. Klikněte na tlačítko *prázdná aktivita* v rámci provedení a klikněte na tlačítko **Další**.
3. V části *nakonfigurujete svůj projekt*vyberte `API 21: Android 5.0.0 (Lollipop)` jako minimální sadu SDK. Toto je nejnižší verze podporuje Azure Maps Android SDK.
4. Přijměte výchozí nastavení `Activity Name` a `Layout Name` a klikněte na tlačítko **dokončit**

Zobrazit [dokumentace k sadě Android Studio](https://developer.android.com/studio/intro/) další pomoc s instalací Android Studio a vytvoření nového projektu.

![Vytvoření nového projektu](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Nastavit virtuální zařízení

Android Studio umožňuje nastavit virtuální zařízení s Androidem ve vašem počítači. Pomáhá při vývoji, testování vaší aplikace. Nastavit virtuální zařízení klikněte na ikonu Android Virtual Device (AVD) Manager v horním pravém rohu obrazovky projektu. Klikněte **vytvořit virtuální zařízení** tlačítko. Můžete také získat pro správce prostřednictvím **nástroje > Android > AVD Manager** na panelu nástrojů. Z **telefony** vyberte **Nexus 5 X** a klikněte na tlačítko **Další**.

Další informace o nastavení AVD v [dokumentace k sadě Android Studio](https://developer.android.com/studio/run/managing-avds).

![Emulátor androidu](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-azure-maps-android-sdk"></a>Instalace sady Android SDK Azure Maps

Než budete pokračovat následujícími směrem k sestavení aplikace, postupujte podle pokynů k instalaci sady Android SDK pro Azure Maps. 

1. Přidejte následující text do **všechny projekty**, blok úložišť ve vaší **build.gradle** souboru.

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Aktualizace vašeho **app/build.gradle** a přidejte následující:

    1. Přidejte následující text do bloku s Androidem:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    2. Aktualizujte vaše bloku závislostí a přidejte následující:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.1"
        ```

3. Nastavení oprávnění přidáním následujícího kódu do vaší **AndroidManifest.xml**

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <manifest>
        ...
        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
        ...
    </manifest>
    ```

4. Upravit **res > rozložení > activity_main.xml**, takže to vypadá, že kód XML:
    
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

5. Upravit **MainActivity.java** pro vytvoření třídy aktivity zobrazení mapy. Po dokončení úprav by měl vypadat jako následující třídy:

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

Po dokončení výše uvedených kroků, bude pravděpodobně získat upozornění z Android Studio na část textu v kódu. Chcete-li vyřešit tato upozornění, importovat třídy, na kterou se odkazuje v `MainActivity.java`.

Tyto třídy mohl automaticky importovat stisknutím kombinace kláves `Alt` + `Enter`(`Option` + `Return` na počítači Mac). 

Klikněte na tlačítko **spuštění "Aplikace"** tlačítko (nebo `Control` + `R` na počítači Mac) k sestavení aplikace.

![Klikněte na tlačítko Spustit](./media/how-to-use-android-map-control-library/run-app.png)

Bude trvat několik sekund pro android studio sestavte aplikaci. Aplikaci můžete otestovat v emulované zařízení s Androidem, po dokončení sestavení. Zobrazí se mapu podobné následující.

![Android mapy](./media/how-to-use-android-map-control-library/android-map.png)

## <a name="add-a-marker-to-the-map"></a>Přidat značku k mapě

Chcete-li přidat značky do mapy přidat `mapView.getMapAsync()` funkce `MainActivity.java`. Finální `MainActivity.java` by měl vypadat nějak takto:

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

Znovu spusťte aplikaci a značky byste měli vidět na mapě, jako je uvedený níže.

![Android mapy kódu pin](./media/how-to-use-android-map-control-library/android-map-pin.png)
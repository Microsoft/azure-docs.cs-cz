---
title: Začínáme s Androidem mapový ovládací prvek ve službě Azure Maps | Dokumentace Microsoftu
description: Android mapový ovládací prvek ve službě Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: e655b442ba9290d4b4525108521f2d1a0c766b48
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64869829"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Začínáme s Azure Maps Android SDK

Azure Maps Android SDK je knihovna vektorové mapy pro Android. Tento článek vás provede procesy, které instalace sady Azure Maps Android SDK a načítání mapy.

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

1. Otevřete na nejvyšší úrovni **build.gradle** a přidejte následující kód, který **všechny projekty**, **úložiště** blokovat části:

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Aktualizace vašeho **app/build.gradle** a přidejte do ní následující kód:
    
    1. Ujistěte se, že váš projekt **minSdkVersion** v rozhraní API 21 nebo vyšší.

    2. Přidejte následující kód do části s Androidem:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Aktualizace vašeho bloku závislostí a přidejte nový řádek provádění závislostí pro nejnovější Azure Maps Android SDK:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

    > [!Note]
    > Azure Maps Android SDK se pravidelně upgraduje a rozšířené. Zobrazí se [Začínáme s Androidem mapový ovládací prvek](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) dokumentaci, chcete-li získat číslo verze nejnovější implementace Azure Maps. Můžete také nastavit číslo verze od "0.2" do "0 +" tak, aby vždy odkazoval na nejnovější verzi.

3. Upravit **res** > **rozložení** > **activity_main.xml** a nahraďte následujícím kódem:
    
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
            />
    </FrameLayout>
    ```

4. V **MainActivity.java** soubor, budete muset:
    
    * Přidejte importy pro sadu SDK Azure Maps
    * Nastavte vaše informace o ověřování Azure Maps
    * získání instance ovládacího prvku mapy **onCreate** – metoda

    Nastavení informací o ověřování na třídy AzureMaps globálně pomocí metody setSubscriptionKey nebo setAadProperties díky, nebudete už muset přidejte své informace o ověřování pro každé zobrazení. Mapový ovládací prvek obsahuje vlastní životního cyklu metody pro správu životního cyklu OpenGL pro Android, která musí být volána přímo z nadřazeného aktivity. V pořadí pro vaši aplikaci správně, volání metod životního cyklu mapový ovládací prvek musí přepsat následující metody životní cyklus v aktivitě, která obsahuje ovládací prvek mapy a volat metodu příslušné mapování ovládacího prvku. 

    Upravit **MainActivity.java** to následujícím způsobem:
    
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
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
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

<center>

![Android mapy](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="next-steps"></a>Další postup

Chcete-li přidat položky do mapy, naleznete v tématu:

> [!div class="nextstepaction"]
> [Přidat vrstvu symbol na mapu s Androidem](https://review.docs.microsoft.com/azure/azure-maps/how-to-add-symbol-to-android-map)

> [!div class="nextstepaction"]
> [Přidávání obrazců do Android mapy](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Styly mapy změn v rámci služby Android maps](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)



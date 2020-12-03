---
title: Začínáme s Azure Maps Android SDK
description: Seznamte se s Android SDK Microsoft Azure Maps. Podívejte se, jak vytvořit projekt v Android Studio, jak nainstalovat sadu SDK a vytvořit interaktivní mapu.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 1da003bb8d285dbedde87cbc6cd4708fda2dc38b
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531246"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Začínáme s Azure Maps Android SDK

Azure Maps Android SDK je knihovna vektorových map pro Android. Tento článek vás provede procesy instalace Azure Maps Android SDK a načtení mapy.

## <a name="prerequisites"></a>Předpoklady

### <a name="create-an-azure-maps-account"></a>Vytvoření účtu Azure Maps

1. [Vytvořit účet Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Získejte primární klíč předplatného](quick-demo-map-app.md#get-the-primary-key-for-your-account), označovaný také jako primární klíč nebo klíč předplatného.
Další informace o ověřování v Azure Maps najdete v tématu [Správa ověřování v Azure Maps](./how-to-manage-authentication.md).
3. [Stáhněte a nainstalujte Android Studio Google](https://developer.android.com/studio/).

## <a name="create-a-project-in-android-studio"></a>Vytvořit projekt v Android Studio

Chcete-li vytvořit projekt Android Studio, proveďte tyto kroky:

1. Spusťte Android Studio.
2. Klikněte na **+ vytvořit nový projekt**.
3. Na kartě **telefon a tablet** klikněte na **prázdná aktivita**. Klikněte na **Next** (Další).
4. V části **Konfigurovat projekt** vyberte `API 21: Android 5.0.0 (Lollipop)` jako minimální sadu SDK.
5. `Java`Jako jazyk vyberte.
6. Přijměte výchozí hodnotu `Name` pro projekt. Klikněte na **Finish** (Dokončit).

Další nápovědu k instalaci Android Studio a vytvoření nového projektu najdete v [dokumentaci k Android Studio](https://developer.android.com/studio/intro/) .

![Vytvoření projektu v Android studiu ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-device"></a>Nastavení zařízení

K otestování aplikace během vývoje můžete použít buď telefon s Androidem, nebo emulátor pro Android.

Další informace o nastavení AVD (virtuální zařízení s Androidem) najdete v [dokumentaci k Android Studio](https://developer.android.com/studio/run/managing-avds).

## <a name="install-the-azure-maps-android-sdk"></a>Instalace Azure Maps Android SDK

Dalším krokem při sestavování aplikace je instalace Android SDK Azure Maps.

Provedením těchto kroků nainstalujete sadu SDK:

1. Na kartě projekt rozbalte položku **Gradle skripty**. Otevřete **Build. Gradle (Project: My_Application)** a přidejte následující kód do části **všechny projekty**, `repositories`  část:

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Otevřete **Build. Gradle (modul: My_Application)**.

3. Ujistěte se, že **hodnotu minsdkversion** v `defaultConfig` části jsou na rozhraní API 21 nebo vyšší.

4. Do části Android přidejte následující kód:

    ```
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    ```

5. Do oddílu přidejte následující kód `dependencies` :

    ```
    implementation "com.microsoft.azure.maps:mapcontrol:0.6"
    ```

6. Na hlavním panelu nástrojů klikněte na **soubor** a pak vyberte **synchronizovat projekt se soubory Gradle**.

7. Otevřete `res > layout > activity_main.xml`. V pravém `Code` horním rohu klikněte na zobrazení. Do elementu přidejte následující kód XML `<androidx.constraintlayout.widget.ConstraintLayout>` .
    
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
            />
    </FrameLayout>
    ```

8. V `java > com.example.myapplication > MainActivity.java` souboru budete potřebovat:

    * Přidejte importy pro sadu Azure Maps SDK.
    * Nastavte informace o ověřování Azure Maps.
    * získat instanci mapového ovládacího prvku v metodě **Create** .

    Aby nedocházelo k přidávání ověřovacích informací pro každé zobrazení aplikace, nastavili jsme ověřovací informace globálně voláním `AzureMaps.setSubscriptionKey` . Můžete také volat `AzureMaps.setAadProperties` , pokud chcete ověřit pomocí Azure Active Directory.

    Mapový ovládací prvek Přepisuje následující metody životního cyklu třídy MainActivity. Tyto metody zodpovídají za správu životního cyklu rozhraní OpenGL pro Android.

    * Create (sada)
    * OnStart ()
    * proces obnovení ()
    * pozastaveno ()
    * zarážka ()
    * Odstranit ()
    * onSaveInstanceState (sada)
    * onLowMemory()

    Upravte `MainActivity.java` soubor následujícím způsobem:

    ```java
    package com.example.myapplication;

    //For older versions use: import android.support.v7.app.AppCompatActivity; 
    import androidx.appcompat.app.AppCompatActivity;
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

            //Wait until the map resources are ready.
            mapControl.onReady(map -> {
                //Add your post map load code here.

            });
        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        protected void onStart(){
            super.onStart();
            mapControl.onStart();
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

>[!WARNING]
>Android Studio pravděpodobně neimportoval požadované třídy.  V důsledku toho bude mít kód nějaké nepřeložitelné odkazy. Chcete-li importovat požadované třídy, Stačí umístit ukazatel myši na každý nerozpoznaný odkaz a stisknout `Alt + Enter` (možnost + návrat na Macu).

Sestavení aplikace bude trvat několik sekund Android Studio. Po dokončení sestavení můžete aplikaci otestovat v emulovaném zařízení se systémem Android. Měla by se zobrazit mapa, jako je tato:

:::image type="content" source="./media/how-to-use-android-map-control-library/android-map.png" border="true" alt-text="Azure Maps v aplikaci pro Android":::

## <a name="localizing-the-map"></a>Lokalizace mapy

Azure Maps Android SDK poskytuje tři různé způsoby nastavení jazyka a místních nastavení mapy.

1. Nastavte jazyk a místní nastavení voláním statických metod pro třídu AzureMaps.

    ```Java
    static {
        //Set your Azure Maps Key.
        AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");

        //Set the regional view.
        AzureMaps.setLanguage("Auto");
    
    }
    ```

2. Definujte jazyk a místní nastavení v XML mapového ovládacího prvku.

    ```XML
    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/myMap"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_language="fr-FR"
        app:mapcontrol_view="Auto"
        />
    ```

3. Nastavte jazyk a místní nastavení voláním metod na mapovém ovládacím prvku. Tato možnost umožňuje změnit nastavení během běhu.

    ```Java
    mapControl.onReady(map -> {
        map.setStyle(StyleOptions.language("fr-FR"));
        map.setStyle(StyleOptions.view("Auto"));
    
    });
    ```

Tady je příklad Azure Maps s jazykem nastaveným na `fr-FR` .

:::image type="content" source="./media/how-to-use-android-map-control-library/android-localization.png" border="true" alt-text="Azure Maps, obrázek mapy znázorňující popisky ve francouzštině":::

Úplný seznam podporovaných jazyků a regionálních zobrazení je popsán [zde](supported-languages.md).

## <a name="navigating-the-map"></a>Navigace v mapě

Existuje několik různých způsobů, jak lze mapu zvětšit, vytočit, otočit a rozteč. Následující podrobnosti jsou všechny různými způsoby navigace v mapě.

**Zvětšit mapu**

- Připojte se k mapě pomocí dvou prsty a gesto roztažení prstů společně, abyste se přiblížili nebo rozdělili prsty.
- Poklepáním na mapu přiblížíte jednu úroveň.
- Poklikáním na dva prsty přiblížíte rozvržení na jednu úroveň.
- Klepněte dvakrát; při druhém klepněte na mapu na mapě a přetažením nahoru nebo dolů zmenšete zobrazení.

**Posouvání mapy**

- Najeďte na mapu a táhněte v libovolném směru.

**Otočit mapu**

- Dotkněte se mapy pomocí dvou prsty a otočení.

**Rozteč mapy**

- Dotkněte se mapy dvěma prsty a přetáhněte je dohromady nahoru nebo dolů.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak přidat překryvná data na mapu:

> [!div class="nextstepaction"]
> [Přidání vrstvy symbolů na mapu pro Android](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Přidání obrazců na mapu pro Android](./how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Změna stylů mapy v doplňkech Android Maps](./set-android-map-styles.md)
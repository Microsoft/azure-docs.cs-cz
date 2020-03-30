---
title: Začínáme s ovládáním mapy Android | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak začít s ovládacím prvkem mapy Android pomocí Sady Microsoft Azure Maps Android SDK.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 55bfb5030d0a168e7556240212fcd5f3be30a289
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335365"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Začínáme s Azure Maps Android SDK

Azure Maps Android SDK je knihovna vektorových map pro Android. Tento článek vás provede procesy instalace sady Azure Maps Android SDK a načtení mapy.

## <a name="prerequisites"></a>Požadavky

### <a name="create-an-azure-maps-account"></a>Vytvoření účtu Azure Maps

Chcete-li dokončit postupy v tomto článku, musíte [nejprve vytvořit účet Azure Maps](quick-demo-map-app.md#create-an-account-with-azure-maps) v cenové úrovni S1 a získat primární [klíč](quick-demo-map-app.md#get-the-primary-key-for-your-account) pro váš účet.

Další informace o ověřování v Azure Maps najdete v [tématu správa ověřování v Azure Maps](./how-to-manage-authentication.md).

### <a name="download-android-studio"></a>Stažení Android Studio

Stáhněte si Android Studio a vytvořte projekt s prázdnou aktivitou před instalací sady Azure Maps Android SDK. Zde [si](https://developer.android.com/studio/) můžete stáhnout Android Studio zdarma od Společnosti Google. 

## <a name="create-a-project-in-android-studio"></a>Vytvoření projektu ve Studiu pro Android

Nejprve vytvořte nový projekt s prázdnou aktivitou. Chcete-li vytvořit projekt Android Studio, proveďte tyto kroky:

1. V části **Vyberte projekt**vyberte **Telefon a tablet**. Aplikace bude spuštěna na tomto faktoru formuláře.
2. Na kartě **Telefon a tablet** vyberte **Vyprázdnit aktivitu**a pak vybrat **Další**.
3. V části **Konfigurace** `API 21: Android 5.0.0 (Lollipop)` projektu vyberte jako minimální sadu SDK. Toto je nejstarší verze podporovaná sadou Azure Maps Android SDK.
4. Přijměte výchozí `Activity Name` nastavení a `Layout Name` vyberte **Dokončit**.

Další [nápovědu](https://developer.android.com/studio/intro/) k instalaci Android Studia a vytvoření nového projektu najdete v dokumentaci k Android Uvámporu.

![Vytvoření projektu ve studiu pro Android ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Nastavení virtuálního zařízení

Android Studio umožňuje nastavit virtuální zařízení Android v počítači. To vám může pomoci otestovat aplikaci během vývoje. Chcete-li nastavit virtuální zařízení, vyberte ikonu Správce virtuálního zařízení (AVD) systému Android v pravém horním rohu obrazovky projektu a pak vyberte **Vytvořit virtuální zařízení**. Můžete se také dostat do Správce AVD výběrem **nástroje** > **Android** > **AVD Manager** z panelu nástrojů. V kategorii **Telefony** vyberte **Nexus 5X**a pak vyberte **Další**.

Další informace o nastavení avd v [dokumentaci K Android Studio](https://developer.android.com/studio/run/managing-avds).

![Emulátor Androidu](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Instalace sady Azure Maps Android SDK

Dalším krokem při vytváření aplikace je instalace sady Azure Maps Android SDK. Chcete-li nainstalovat sadu SDK, postupujte takto:

1. Otevřete soubor **build.gradle** nejvyšší úrovně a přidejte následující kód do oddílu **bloku všech projektů**, **úložišť:**

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Aktualizujte **aplikaci/build.gradle** a přidejte do ní následující kód:
    
    1. Ujistěte se, že váš projekt **minSdkVersion** je na API 21 nebo vyšší.

    2. Do sekce Android přidejte následující kód:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Aktualizujte blok závislostí a přidejte nový řádek závislostí implementace pro nejnovější sdk Android Azure Maps:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```
    
    4. Přejděte na panel **u souborů** na panelu nástrojů a klikněte na **Synchronizovat projekt s gradle soubory**.
3. Přidejte fragment mapy k hlavní \> aktivitě\_(res layout activity \> main.xml):
    
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

4. V souboru **MainActivity.java** budete muset:
    
    * přidání importů pro sdk Azure Maps SDK
    * nastavení ověřovacích informací služby Azure Maps
    * získání instance ovládacího prvku mapy v metodě **onCreate**

    Nastavení informací o `AzureMaps` ověřování na třídu globálně pomocí metody `setSubscriptionKey` nebo `setAadProperties` umožňuje, takže nebudete muset přidávat ověřovací informace na každé zobrazení. 

    Ovládací prvek mapy obsahuje vlastní metody životního cyklu pro správu životního cyklu OpenGL androida. Tyto metody životního cyklu musí být volána přímo z obsahující aktivity. Aby vaše aplikace správně volala metody životního cyklu ovládacího prvku mapy, musíte přepsat následující metody životního cyklu v aktivitě, která obsahuje ovládací prvek mapy. A musíte volat příslušnou metodu řízení mapy. 

    * onCreate(balíček) 
    * onStart() 
    * onResume() 
    * onPause() 
    * onStop() 
    * onDestroy() 
    * onSaveInstanceState(balíček) 
    * onLowMemory() 

    Upravte soubor **MainActivity.java** takto:
    
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

## <a name="import-classes"></a>Import ovat třídy

Po dokončení předchozích kroků pravděpodobně dostanete upozornění od Android Studio o některých kód. Chcete-li tato upozornění vyřešit, `MainActivity.java`importujte třídy uvedené v .

Tyto třídy můžete automaticky importovat výběrem alt+enter (Option+Return na Macu).

Vyberte tlačítko spustit, jak je znázorněno na následující grafice (nebo stiskněte Ctrl+R na Macu), abyste vytvořili aplikaci.

![Klikněte na Run (Spustit).](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio bude trvat několik sekund k vytvoření aplikace. Po dokončení sestavení můžete otestovat aplikaci v emulovaném zařízení Android. Měli byste vidět mapu, jako je tato:

<center>

![Mapy Azure v aplikaci pro Android](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>Lokalizace mapy

Sada Azure Maps Android SDK poskytuje tři různé způsoby nastavení jazyka a místního zobrazení mapy. Následující kód ukazuje, jak nastavit jazyk na francouzštinu ("fr-FR") a regionální zobrazení "auto". 

První možností je předat jazyk a zobrazit `AzureMaps` regionální informace `setLanguage` do `setView` třídy pomocí statické a metody globálně. Tím nastavíte výchozí jazyk a místní zobrazení napříč všemi ovládacími prvky Azure Maps načtenými ve vaší aplikaci.

```Java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

Druhou možností je předat jazyk a zobrazit informace do xml ovládacího prvku mapy.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

Třetí možností je programově nastavit jazyk a regionální zobrazení mapy `setStyle` pomocí metody mapy. To lze provést kdykoli změnit jazyk a regionální zobrazení mapy.

```Java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Tady je příklad Azure Maps s jazykem nastaveným na "fr-FR" a místní zobrazení nastavené na "auto".

<center>

![Azure Maps, obrázek mapy s popisky ve francouzštině](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

Kompletní seznam podporovaných jazyků a regionálních zobrazení je zdokumentován [zde](supported-languages.md).

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak na mapu přidat překryvná data:

> [!div class="nextstepaction"]
> [Přidání vrstvy symbolů do mapy Androidu](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Přidání obrazců do mapy Androidu](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Změna stylů map v mapách Android](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)

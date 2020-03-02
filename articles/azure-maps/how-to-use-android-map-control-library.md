---
title: Začínáme s ovládacím prvkem pro mapování Androidu | Mapy Microsoft Azure
description: V tomto článku se naučíte, jak začít s mapovým ovládacím prvkem pro Android pomocí Android SDK Microsoft Azure Maps.
author: farah-alyasari
ms.author: v-faalya
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 1858b221418e6cc59c6441e0653299710b5c7e72
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2020
ms.locfileid: "78208640"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Začínáme s Azure Maps Android SDK

Azure Maps Android SDK je knihovna vektorových map pro Android. Tento článek vás provede procesy instalace Azure Maps Android SDK a načtení mapy.

## <a name="prerequisites"></a>Požadavky

### <a name="create-an-azure-maps-account"></a>Vytvoření účtu Azure Maps

Pokud chcete dokončit postupy v tomto článku, musíte nejdřív [vytvořit účet Azure Maps](quick-demo-map-app.md#create-an-account-with-azure-maps) v cenové úrovni S1 a získat pro svůj účet [primární klíč](quick-demo-map-app.md#get-the-primary-key-for-your-account) .

Další informace o ověřování v Azure Maps najdete v tématu [Správa ověřování v Azure Maps](./how-to-manage-authentication.md).

### <a name="download-android-studio"></a>Stáhnout Android Studio

Před instalací Android SDK Azure Maps si stáhněte Android Studio a vytvořte projekt s prázdnou aktivitou. Zdarma [si můžete stáhnout Android Studio](https://developer.android.com/studio/) zdarma od společnosti Google. 

## <a name="create-a-project-in-android-studio"></a>Vytvořit projekt v Android Studio

Nejprve vytvořte nový projekt s prázdnou aktivitou. Chcete-li vytvořit projekt Android Studio, proveďte tyto kroky:

1. V části **zvolit projekt**vyberte **telefon a tablet**. Vaše aplikace se spustí v tomto faktoru formuláře.
2. Na kartě **telefon a tablet** vyberte **prázdná aktivita**a pak vyberte **Další**.
3. V části **Konfigurovat projekt**vyberte jako minimální sadu SDK možnost `API 21: Android 5.0.0 (Lollipop)`. Toto je nejstarší verze, kterou podporuje Azure Maps Android SDK.
4. Přijměte výchozí `Activity Name` a `Layout Name` a vyberte **Dokončit**.

Další nápovědu k instalaci Android Studio a vytvoření nového projektu najdete v [dokumentaci k Android Studio](https://developer.android.com/studio/intro/) .

![Vytvoření projektu v Android studiu ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Nastavení virtuálního zařízení

Android Studio umožňuje na počítači nastavit virtuální zařízení s Androidem. To vám může pomáhat při testování aplikace během vývoje. Pokud chcete nastavit virtuální zařízení, vyberte ikonu správce virtuálního zařízení (AVD) Android v pravém horním rohu obrazovky projektu a pak vyberte **vytvořit virtuální zařízení**. Do AVD Manageru se můžete dostat taky tak, že na panelu nástrojů vyberete **nástroje** > **Android** > **AVD Manager** . V kategorii **telefony** vyberte **Nexus pětinásobné**a pak vyberte **Další**.

Další informace o nastavení AVD najdete v [dokumentaci k Android Studio](https://developer.android.com/studio/run/managing-avds).

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Instalace Azure Maps Android SDK

Dalším krokem při sestavování aplikace je instalace Android SDK Azure Maps. Provedením těchto kroků nainstalujete sadu SDK:

1. Otevřete soubor **Build. Gradle** nejvyšší úrovně a přidejte následující kód do části **všechny projekty**, blok **úložišť** :

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Aktualizujte svou **aplikaci/Build. Gradle** a přidejte do ní následující kód:
    
    1. Ujistěte se, že je **hodnotu minsdkversion** projektu na rozhraní API 21 nebo vyšší.

    2. Do části Android přidejte následující kód:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Aktualizujte svůj blok závislosti a přidejte nový řádek s závislostí implementace pro nejnovější Azure Maps Android SDK:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```
    
    4. Přejděte na **soubor** na panelu nástrojů a pak klikněte na **synchronizovat projekt se soubory Gradle**.
3. Přidejte do hlavní aktivity fragment mapy (res \> layout \> Activity\_Main. XML):
    
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

4. V souboru **MainActivity. Java** budete potřebovat:
    
    * Přidání importů pro sadu Azure Maps SDK
    * Nastavení ověřovacích informací Azure Maps
    * Získání instance mapového ovládacího prvku v metodě **Create**

    Nastavení ověřovacích informací pro třídu `AzureMaps` globálně pomocí metod `setSubscriptionKey` nebo `setAadProperties` ji vytvoří, takže nebudete muset přidávat informace o ověřování do každého zobrazení. 

    Mapový ovládací prvek obsahuje vlastní metody životního cyklu pro správu životního cyklu OpenGL pro Android. Tyto metody životního cyklu musí být volány přímo z obsažené aktivity. Aby vaše aplikace správně volala metody životního cyklu mapového ovládacího prvku, je nutné přepsat následující metody životního cyklu v aktivitě, která obsahuje mapový ovládací prvek. A, je nutné zavolat příslušnou metodu mapového ovládacího prvku. 

    * Create (sada) 
    * OnStart () 
    * proces obnovení () 
    * pozastaveno () 
    * zarážka () 
    * Odstranit () 
    * onSaveInstanceState (sada) 
    * onLowMemory() 

    Upravte soubor **MainActivity. Java** následujícím způsobem:
    
    ```java
    package com.example.myapplication;

    //For older versions use: import android.support.v7.app.AppCompatActivity;
    import androidx.appcompat.app.AppCompatActivity;

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

## <a name="import-classes"></a>Importovat třídy

Po dokončení předchozích kroků se pravděpodobně zobrazí upozornění od Android Studio o některém z kódů. Chcete-li vyřešit tato upozornění, importujte třídy, na které se odkazuje v `MainActivity.java`.

Tyto třídy můžete automaticky importovat tak, že vyberete ALT + ENTER (možnost + návrat na Macu).

Vyberte tlačítko Spustit, jak je znázorněno na následujícím obrázku (nebo stiskněte CTRL + R na Macu) a sestavte aplikaci.

![Klikněte na Run (Spustit).](./media/how-to-use-android-map-control-library/run-app.png)

Sestavení aplikace bude trvat několik sekund Android Studio. Po dokončení sestavení můžete aplikaci otestovat v emulovaném zařízení se systémem Android. Měla by se zobrazit mapa, jako je tato:

<center>

![Azure Maps v aplikaci pro Android](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>Lokalizace mapy

Azure Maps Android SDK poskytuje tři různé způsoby nastavení jazyka a regionálního zobrazení mapy. Následující kód ukazuje, jak nastavit jazyk na francouzštinu ("fr-FR") a místní zobrazení na "auto". 

První možností je předat jazyk a zobrazit regionální informace do třídy `AzureMaps` pomocí statických metod `setLanguage` a `setView` globálně. Tím se nastaví výchozí jazyk a místní zobrazení ve všech Azure Mapsch ovládacích prvcích načtených ve vaší aplikaci.

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

Druhou možností je předat jazyk a zobrazit informace do XML mapového ovládacího prvku.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

Třetí možností je programově nastavit jazyk a regionální zobrazení mapy pomocí metody map `setStyle`. To lze provést kdykoli, chcete-li změnit jazyk a regionální zobrazení mapy.

```Java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Tady je příklad Azure Maps s jazykem, který je nastaven na "fr-FR" a místní zobrazení nastavené na "auto".

<center>

![Azure Maps, obrázek mapy znázorňující popisky ve francouzštině](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

Úplný seznam podporovaných jazyků a regionálních zobrazení je popsán [zde](supported-languages.md).

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak přidat překryvná data na mapu:

> [!div class="nextstepaction"]
> [Přidání vrstvy symbolů na mapu pro Android](https://review.docs.microsoft.com/azure/azure-maps/how-to-add-symbol-to-android-map)

> [!div class="nextstepaction"]
> [Přidání obrazců na mapu pro Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Změna stylů mapy v doplňkech Android Maps](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)

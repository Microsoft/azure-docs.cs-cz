---
title: Začínáme s ovládacím prvkem pro mapování Androidu | Mapy Microsoft Azure
description: Seznámení se Azure Maps Android SDK. Podívejte se, jak vytvořit projekt v Android Studio, jak nainstalovat sadu SDK a vytvořit interaktivní mapu.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 2cda543781118a19ba4999e774b96ae81857f442
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91330923"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Začínáme s Azure Maps Android SDK

Azure Maps Android SDK je knihovna vektorových map pro Android. Tento článek vás provede procesy instalace Azure Maps Android SDK a načtení mapy.

## <a name="prerequisites"></a>Požadavky

### <a name="create-an-azure-maps-account"></a>Vytvoření účtu Azure Maps

Pokud chcete dokončit postupy v tomto článku, musíte nejdřív [vytvořit účet Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account) v cenové úrovni S1 a získat pro svůj účet [primární klíč](quick-demo-map-app.md#get-the-primary-key-for-your-account) .

Další informace o ověřování v Azure Maps najdete v tématu [Správa ověřování v Azure Maps](./how-to-manage-authentication.md).

### <a name="download-android-studio"></a>Stáhnout Android Studio

Před instalací Android SDK Azure Maps si stáhněte Android Studio a vytvořte projekt s prázdnou aktivitou. Zdarma [si můžete stáhnout Android Studio](https://developer.android.com/studio/) zdarma od společnosti Google. 

## <a name="create-a-project-in-android-studio"></a>Vytvořit projekt v Android Studio

Nejprve vytvořte nový projekt s prázdnou aktivitou. Chcete-li vytvořit projekt Android Studio, proveďte tyto kroky:

1. V části **zvolit projekt**vyberte **telefon a tablet**. Vaše aplikace se spustí v tomto faktoru formuláře.
2. Na kartě **telefon a tablet** vyberte **prázdná aktivita**a pak vyberte **Další**.
3. V části **Konfigurovat projekt**vyberte `API 21: Android 5.0.0 (Lollipop)` jako minimální sadu SDK. Toto je nejstarší verze, kterou podporuje Azure Maps Android SDK.
4. Přijměte výchozí nastavení `Activity Name` a `Layout Name` klikněte na **Dokončit**.

Další nápovědu k instalaci Android Studio a vytvoření nového projektu najdete v [dokumentaci k Android Studio](https://developer.android.com/studio/intro/) .

![Vytvoření projektu v Android studiu ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Nastavení virtuálního zařízení

Android Studio umožňuje na počítači nastavit virtuální zařízení s Androidem. To vám může pomáhat při testování aplikace během vývoje. Pokud chcete nastavit virtuální zařízení, vyberte ikonu správce virtuálního zařízení (AVD) Android v pravém horním rohu obrazovky projektu a pak vyberte **vytvořit virtuální zařízení**. Do AVD Manageru se můžete dostat taky tak, že na panelu nástrojů vyberete **nástroje**pro  >  **Android**  >  **AVD Manager** . V kategorii **telefony** vyberte **Nexus pětinásobné**a pak vyberte **Další**.

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
        implementation "com.microsoft.azure.maps:mapcontrol:0.4"
        ```
    
    4. Přejděte na **soubor** na panelu nástrojů a pak klikněte na **synchronizovat projekt se soubory Gradle**.
3. Přidejte do hlavní aktivity fragment mapy ( \> aktivita rozložení res \> \_main.xml):
    
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
    * získání instance mapového ovládacího prvku v metodě **Create**

    Nastavení ověřovacích informací `AzureMaps` třídy globálně pomocí `setSubscriptionKey` `setAadProperties` metod nebo ji vytvoří, takže nebudete muset přidávat informace o ověřování do každého zobrazení. 

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

Po dokončení předchozích kroků se pravděpodobně zobrazí upozornění od Android Studio o některém z kódů. Chcete-li tato upozornění vyřešit, importujte třídy, které jsou odkazovány v `MainActivity.java` .

Tyto třídy můžete automaticky importovat tak, že vyberete ALT + ENTER (možnost + návrat na Macu).

Vyberte tlačítko Spustit, jak je znázorněno na následujícím obrázku (nebo stiskněte CTRL + R na Macu) a sestavte aplikaci.

![Klikněte na Run (Spustit).](./media/how-to-use-android-map-control-library/run-app.png)

Sestavení aplikace bude trvat několik sekund Android Studio. Po dokončení sestavení můžete aplikaci otestovat v emulovaném zařízení se systémem Android. Měla by se zobrazit mapa, jako je tato:

<center>

![Azure Maps v aplikaci pro Android](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>Lokalizace mapy

Azure Maps Android SDK poskytuje tři různé způsoby nastavení jazyka a regionálního zobrazení mapy. Následující kód ukazuje, jak nastavit jazyk na francouzštinu ("fr-FR") a místní zobrazení na "auto". 

První možností je předat jazyk a zobrazit regionální informace do `AzureMaps` třídy pomocí globálně statických `setLanguage` a `setView` metod. Tím se nastaví výchozí jazyk a místní zobrazení ve všech Azure Mapsch ovládacích prvcích načtených ve vaší aplikaci.

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

Třetí možností je programově nastavit jazyk a regionální zobrazení mapy pomocí `setStyle` metody map. To lze provést kdykoli, chcete-li změnit jazyk a regionální zobrazení mapy.

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

## <a name="navigating-the-map"></a>Navigace v mapě

Existuje několik různých způsobů, jak lze mapu zvětšit, vytočit, otočit a rozteč. Následující podrobnosti jsou všechny různými způsoby navigace v mapě.

**Zvětšit mapu**

- Připojte se k mapě pomocí dvou prsty a gesto roztažení prstů společně, abyste se přiblížili nebo rozdělili prsty.
- Poklepáním na mapu přiblížíte jednu úroveň.
- Poklikáním na dva prsty přiblížíte mapu o jednu úroveň.
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
> [Přidání obrazců na mapu pro Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Změna stylů mapy v doplňkech Android Maps](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)

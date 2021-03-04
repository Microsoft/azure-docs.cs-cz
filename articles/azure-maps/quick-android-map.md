---
title: 'Rychlý Start: Vytvoření aplikace pro Android s Azure Maps | Microsoft Azure '
description: 'Rychlý Start: Naučte se, jak vytvořit aplikaci pro Android pomocí Android SDK Azure Maps.'
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2020
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: mvc
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 3c0f95c1252b6895b4604d14e5565395beab8952
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039644"
---
# <a name="quickstart-create-an-android-app-with-azure-maps"></a>Rychlý Start: Vytvoření aplikace pro Android pomocí Azure Maps

V tomto článku se dozvíte, jak přidat Azure Maps do aplikace pro Android. Provede vás těmito základními kroky:

* Nastavte vývojové prostředí.
* Vytvořte si vlastní účet Azure Maps.
* Získejte primární Azure Maps klíč pro použití v aplikaci.
* Odkaz na knihovny Azure Maps z projektu.
* Přidejte do aplikace ovládací prvek Azure Maps.

## <a name="prerequisites"></a>Požadavky

1. Vytvořte účet Azure Maps tím, že se přihlásíte k [Azure Portal](https://portal.azure.com). Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/).
2. [Vytvořit účet Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Získejte primární klíč předplatného](quick-demo-map-app.md#get-the-primary-key-for-your-account), označovaný také jako primární klíč nebo klíč předplatného. Další informace o ověřování v Azure Maps najdete v tématu [Správa ověřování v Azure Maps](how-to-manage-authentication.md).
4. [Stáhněte si Android Studio](https://developer.android.com/studio/) zdarma od společnosti Google.

## <a name="create-an-azure-maps-account"></a>Vytvoření účtu Azure Maps

Vytvořte nový Azure Maps účet pomocí následujících kroků:

1. V levém horním rohu webu [Azure Portal](https://portal.azure.com) klikněte na **Vytvořit prostředek**.
2. Do pole *Hledat na Marketplace* zadejte **Azure Maps**.
3. Z *výsledků* vyberte **Azure Maps**. Klikněte na tlačítko **Vytvořit**, které se zobrazí pod mapou.
4. Na stránce **Vytvořit účet Maps** zadejte následující hodnoty:
    * *Předplatné*, které chcete pro tento účet použít.
    * Název *skupiny prostředků* pro tento účet. Můžete zvolit možnost *Vytvořit novou* nebo *Použít existující* skupinu prostředků.
    * *Název* vašeho nového účtu.
    * *Cenová úroveň* pro tento účet.
    * Přečtěte si *Licenční podmínky* a *Prohlášení o zásadách ochrany osobních údajů* a zaškrtnutím příslušného políčka podmínky přijměte.
    * Klikněte na tlačítko **Vytvořit**.

    ![Vytvoření účtu Maps na portálu](media/quick-android-map/create-account.png)

## <a name="get-the-primary-key-for-your-account"></a>Získání primárního klíče pro váš účet

Po úspěšném vytvoření účtu Maps načtěte primární klíč, který vám umožní dotazovat se na rozhraní API mapy.

1. Otevřete svůj účet Maps na portálu.
2. V části nastavení vyberte **ověřování**.
3. Zkopírujte do schránky **Primární klíč**. Místně ho uložte, abyste ho mohli použít později v tomto kurzu.

>[!NOTE]
> Pokud místo Azure Maps primárního klíče použijete klíč předplatného Azure, nebude se vaše mapa správně vykreslovat. Z bezpečnostních důvodů se navíc doporučuje střídat mezi primárním a sekundárním klíčem. Pokud chcete otočit klíče, aktualizujte aplikaci tak, aby používala sekundární klíč, nasaďte a pak stisknutím tlačítka cyklus/obnovení vedle primárního klíče Vygenerujte nový primární klíč. Starý primární klíč bude zakázán. Další informace o rotaci klíčů najdete v tématu [nastavení Azure Key Vault s použitím rotace a auditování klíčů](../key-vault/secrets/tutorial-rotation-dual.md) .

![Získání klíče primárního klíče Azure Maps v Azure Portal](media/quick-android-map/get-key.png)

## <a name="create-a-project-in-android-studio"></a>Vytvořit projekt v Android Studio

Nejprve vytvořte nový projekt s prázdnou aktivitou. Chcete-li vytvořit projekt Android Studio, proveďte tyto kroky:

1. V části **zvolit projekt** vyberte **telefon a tablet**. Vaše aplikace se spustí v tomto faktoru formuláře.
2. Na kartě **telefon a tablet** vyberte **prázdná aktivita** a pak vyberte **Další**.
3. V části **Konfigurovat projekt** vyberte `API 21: Android 5.0.0 (Lollipop)` jako minimální sadu SDK. Toto je nejstarší verze, kterou podporuje Azure Maps Android SDK.
4. Přijměte výchozí nastavení `Activity Name` a `Layout Name` klikněte na **Dokončit**.

Další nápovědu k instalaci Android Studio a vytvoření nového projektu najdete v [dokumentaci k Android Studio](https://developer.android.com/studio/intro/) .

![Vytvoření projektu v Android studiu](media/quick-android-map/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Nastavení virtuálního zařízení

Android Studio umožňuje na počítači nastavit virtuální zařízení s Androidem. To vám může pomáhat při testování aplikace během vývoje. Pokud chcete nastavit virtuální zařízení, vyberte ikonu správce virtuálního zařízení (AVD) Android v pravém horním rohu obrazovky projektu a pak vyberte **vytvořit virtuální zařízení**. Do AVD Manageru se můžete dostat taky tak, že na panelu nástrojů vyberete **nástroje** pro  >  **Android**  >  **AVD Manager** . V kategorii **telefony** vyberte **Nexus pětinásobné** a pak vyberte **Další**.

Další informace o nastavení AVD najdete v [dokumentaci k Android Studio](https://developer.android.com/studio/run/managing-avds).

![Android Emulator](media/quick-android-map/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Instalace Azure Maps Android SDK

Dalším krokem při sestavování aplikace je instalace Android SDK Azure Maps. Provedením těchto kroků nainstalujete sadu SDK:

1. Otevřete soubor **Build. Gradle** nejvyšší úrovně a přidejte následující kód do části **všechny projekty**, blok **úložišť** :

    ```gradle
    maven {
        url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Aktualizujte svou **aplikaci/Build. Gradle** a přidejte do ní následující kód:

    1. Ujistěte se, že je **hodnotu minsdkversion** projektu na rozhraní API 21 nebo vyšší.

    2. Do části Android přidejte následující kód:

        ```gradle
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```

    3. Aktualizujte svůj blok závislosti a přidejte nový řádek s závislostí implementace pro nejnovější Azure Maps Android SDK:

        ```gradle
        implementation "com.microsoft.azure.maps:mapcontrol:0.7"
        ```

        > [!Note]
        > Můžete nastavit číslo verze na 0 +, aby váš kód vždy odkazoval na nejnovější verzi.

    4. Přejděte na **soubor** na panelu nástrojů a pak klikněte na **synchronizovat projekt se soubory Gradle**.
3. Přidejte do hlavní aktivity fragment mapy ( \> aktivita rozložení res \> \_main.xml):

    ```xml
    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/mapcontrol"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        />
    ```

::: zone pivot="programming-language-java-android"

4. V souboru **MainActivity. Java** budete potřebovat:

    * Přidání importů pro sadu Azure Maps SDK
    * Nastavení ověřovacích informací Azure Maps
    * získání instance mapového ovládacího prvku v metodě **Create**

    Nastavení ověřovacích informací `AzureMaps` třídy globálně pomocí `setSubscriptionKey` `setAadProperties` metod nebo ji vytvoří, takže nebudete muset přidávat informace o ověřování do každého zobrazení.

    Mapový ovládací prvek obsahuje vlastní metody životního cyklu pro správu životního cyklu OpenGL pro Android. Tyto metody životního cyklu musí být volány přímo z obsažené aktivity. Aby vaše aplikace správně volala metody životního cyklu mapového ovládacího prvku, je nutné přepsat následující metody životního cyklu v aktivitě, která obsahuje mapový ovládací prvek. A, je nutné zavolat příslušnou metodu mapového ovládacího prvku.

    * `onCreate(Bundle)`
    * `onDestroy()`
    * `onLowMemory()`
    * `onPause()`
    * `onResume()`
    * `onSaveInstanceState(Bundle)`
    * `onStart()`
    * `onStop()`

    Upravte soubor **MainActivity. Java** následujícím způsobem:

    ```java
    package com.example.myapplication;
    
    import androidx.appcompat.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    
    public class MainActivity extends AppCompatActivity {
        
    static {
        AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");

        //Alternatively use Azure Active Directory authenticate.
        //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
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
    }}
    ```

    > [!NOTE]
    > Po dokončení předchozích kroků můžete získat upozornění od Android Studio o některém z kódů. Chcete-li tato upozornění vyřešit, importujte třídy, které jsou odkazovány v `MainActivity.java` .
    > Tyto třídy můžete automaticky importovat tak, že vyberete `Alt`  +  `Enter` ( `Option`  +  `Return` na Macu).

::: zone-end

::: zone pivot="programming-language-kotlin"

4. V souboru **MainActivity. kt** budete potřebovat:

    * Přidání importů pro sadu Azure Maps SDK
    * Nastavení ověřovacích informací Azure Maps
    * získání instance mapového ovládacího prvku v metodě **Create**

    Nastavení ověřovacích informací `AzureMaps` třídy globálně pomocí `setSubscriptionKey` `setAadProperties` metod nebo ji vytvoří, takže nebudete muset přidávat informace o ověřování do každého zobrazení.

    Mapový ovládací prvek obsahuje vlastní metody životního cyklu pro správu životního cyklu OpenGL pro Android. Tyto metody životního cyklu musí být volány přímo z obsažené aktivity. Aby vaše aplikace správně volala metody životního cyklu mapového ovládacího prvku, je nutné přepsat následující metody životního cyklu v aktivitě, která obsahuje mapový ovládací prvek. A, je nutné zavolat příslušnou metodu mapového ovládacího prvku.

    * `onCreate(Bundle)`
    * `onDestroy()`
    * `onLowMemory()`
    * `onPause()`
    * `onResume()`
    * `onSaveInstanceState(Bundle)`
    * `onStart()`
    * `onStop()`

    Upravte soubor **MainActivity. kt** následujícím způsobem:

    ```kotlin
    package com.example.myapplication;

    import androidx.appcompat.app.AppCompatActivity
    import android.os.Bundle
    import com.microsoft.azure.maps.mapcontrol.AzureMap
    import com.microsoft.azure.maps.mapcontrol.AzureMaps
    import com.microsoft.azure.maps.mapcontrol.MapControl
    import com.microsoft.azure.maps.mapcontrol.events.OnReady
    
    class MainActivity : AppCompatActivity() {
    
        companion object {
            init {
                AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
    
                //Alternatively use Azure Active Directory authenticate.
                //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
            }
        }
    
        var mapControl: MapControl? = null
    
        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            setContentView(R.layout.activity_main)
    
            mapControl = findViewById(R.id.mapcontrol)
    
            mapControl?.onCreate(savedInstanceState)
    
            //Wait until the map resources are ready.
            mapControl?.onReady(OnReady { map: AzureMap -> })
        }
    
        public override fun onStart() {
            super.onStart()
            mapControl?.onStart()
        }
    
        public override fun onResume() {
            super.onResume()
            mapControl?.onResume()
        }
    
        public override fun onPause() {
            mapControl?.onPause()
            super.onPause()
        }
    
        public override fun onStop() {
            mapControl?.onStop()
            super.onStop()
        }
    
        override fun onLowMemory() {
            mapControl?.onLowMemory()
            super.onLowMemory()
        }
    
        override fun onDestroy() {
            mapControl?.onDestroy()
            super.onDestroy()
        }
    
        override fun onSaveInstanceState(outState: Bundle) {
            super.onSaveInstanceState(outState)
            mapControl?.onSaveInstanceState(outState)
        }
    }
    ```

    > [!NOTE]
    > Po dokončení předchozích kroků můžete získat upozornění od Android Studio o některém z kódů. Chcete-li tato upozornění vyřešit, importujte třídy, které jsou odkazovány v `MainActivity.kt` .
    > Tyto třídy můžete automaticky importovat tak, že vyberete `Alt`  +  `Enter` ( `Option`  +  `Return` na Macu).

::: zone-end

5. Vyberte tlačítko Spustit, jak je znázorněno na následujícím obrázku (nebo stiskněte `Control`  +  `R` na Macu) a sestavte aplikaci.

    ![Klikněte na Run (Spustit).](media/quick-android-map/run-app.png)

Sestavení aplikace bude trvat několik sekund Android Studio. Po dokončení sestavení můžete aplikaci otestovat v emulovaném zařízení se systémem Android. Měla by se zobrazit mapa, jako je tato:

![Azure Maps v aplikaci pro Android](media/quick-android-map/quickstart-android-map.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

>[!WARNING]
> Kurzy uvedené v části [Další kroky](#next-steps) podrobně popisují, jak použít a nakonfigurovat Azure Maps s vaším účtem. Pokud se chystáte pokračovat v kurzech, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu.

Pokud neplánujete pokračovat v kurzech, proveďte tyto kroky a vyčistěte prostředky:

1. Zavřete Android Studio a odstraňte aplikaci, kterou jste vytvořili.
2. Pokud jste aplikaci otestovali na externím zařízení, odinstalujte ji z tohoto zařízení.

Pokud neplánujete pokračovat v vývoji s Azure Maps Android SDK:

1. Přejděte na stránku Azure Portal. Na hlavní stránce portálu vyberte **všechny prostředky** . Případně klikněte na ikonu nabídky v levém horním rohu. Vyberte **Všechny prostředky**.
2. Klikněte na účet Azure Maps. V horní části stránky klikněte na **Odstranit**.
3. Pokud neplánujete pokračovat v vývoji aplikací pro Android, odinstalujte Android Studio.

Další příklady kódu naleznete v následujících příručkách:

* [Správa ověřování v Azure Maps](how-to-manage-authentication.md)
* [Změna stylů mapy v doplňkech Android Maps](set-android-map-styles.md)
* [Přidání vrstvy symbolů](how-to-add-symbol-to-android-map.md)
* [Přidání řádkové vrstvy](android-map-add-line-layer.md)
* [Přidání mnohoúhelníkové vrstvy](how-to-add-shapes-to-android-map.md)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili účet Azure Maps a vytvořili jste ukázkovou aplikaci. Další informace o Azure Maps najdete v následujících kurzech:

> [!div class="nextstepaction"]
> [Načíst data o injson do Azure Maps](tutorial-load-geojson-file-android.md)
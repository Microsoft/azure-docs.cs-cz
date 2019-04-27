---
title: Kurz – podrobné pokyny k vytvoření nové aplikace pro Android pomocí Azure prostorových kotvy | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak vytvořit novou aplikaci Android pomocí Azure prostorových ukotvení.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 04/03/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9838add4f83434848d61f3ae86db71765efdc59a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60786400"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-android-app-using-azure-spatial-anchors"></a>Kurz: Podrobné pokyny k vytvoření nové aplikace pro Android pomocí Azure prostorových kotvy

Tomto kurzu se dozvíte, jak vytvořit novou aplikaci Android, která se integruje s Azure prostorových kotvy ARCore funkce.

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento kurz, ujistěte se, že máte následující:

- Windows nebo macOS počítače s <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3 +</a>.
- A <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">developer povolené</a> a <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore podporující</a> zařízení s Androidem.

## <a name="getting-started"></a>Začínáme

Spusťte Android Studio. V **Vítejte v nástroji Android Studio** okna, klikněte na tlačítko **spusťte nový projekt Android Studio**. Nebo, pokud máte projekt už otevřený, vyberte **souboru**->**nový projekt**.

V **vytvořit nový projekt** okně v části **telefony a tablety** zvolte **prázdná aktivita**a klikněte na tlačítko **Další**. Potom v části **rozhraní API minimálně úrovně**, zvolte `API 26: Android 8.0 (Oreo)`a zajistit **jazyk** je nastavena na `Java`. Můžete změnit název projektu & umístění a název balíčku. U ostatních možností ponechte, protože jde o. Klikněte na **Dokončit**. **Instalační program komponenty** poběží. Po dokončení klikněte na tlačítko **Dokončit**. Po nějaké zpracování Android Studio otevře rozhraní IDE.

## <a name="trying-it-out"></a>Vyzkoušejte si

K otestování vaší nové aplikace, připojení zařízení povolené pro vývojáře na vývojovém počítači pomocí kabelu USB. Klikněte na tlačítko **spustit**->**spuštění "aplikace"**. V **vyberte cíl nasazení** okna, vyberte zařízení a klikněte na tlačítko **OK**. Android Studio nainstaluje aplikaci na připojeném zařízení a spustí ho. Teď byste měli vidět "Hello World!" zobrazí v aplikaci spuštěnou na zařízení. Klikněte na tlačítko **spustit**->**Stop "aplikace"**.

## <a name="integrating-arcore"></a>Integrace _ARCore_

<a href="https://developers.google.com/ar/discover/" target="_blank">_ARCore_ </a> je od Googlu platforma pro vytváření prostředí pro rozšířené Reality, aktivace vašeho zařízení ke sledování jeho pozice přesune a sestaví vlastní znalost reálného světa.

Upravit `app\manifests\AndroidManifest.xml` zahrnout následující položky v kořenovém adresáři `<manifest>` uzlu. Tento fragment kódu se provede několik věcí:

- To vám umožní vaší aplikaci přístup k fotoaparátu zařízení.
- Také zajistí, že vaše aplikace je viditelná jen v Store Google Play na zařízení, která podporují ARCore.
- Nakonfiguruje Google Play Store ke stažení a instalaci ARCore, pokud nainstalovaná není, ještě při instalaci aplikace.

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera.ar" />

<application>
    ...
    <meta-data android:name="com.google.ar.core" android:value="required" />
    ...
</application>
```

Upravit `Gradle Scripts\build.gradle (Module: app)` přidejte následující položku. Tento kód, který zajistí vaše aplikace cílí ARCore verze 1.7. Po této změně, může se zobrazit oznámení z Gradle s výzvou k synchronizaci: klikněte na tlačítko **synchronizovat nyní**.

```
dependencies {
    ...
    implementation 'com.google.ar:core:1.7.0'
    ...
}
```

## <a name="integrating-sceneform"></a>Integrace _Sceneform_

<a href="https://developers.google.com/ar/develop/java/sceneform/" target="_blank">_Sceneform_ </a> usnadňuje vykreslení realistické 3D scény v aplikacích pro rozšířené Reality, bez nutnosti učit OpenGL.

Upravit `Gradle Scripts\build.gradle (Module: app)` zahrnout následující položky. Tento kód vám umožní vaši aplikaci, aby používala jazykovým konstrukcím z Javy 8, které `Sceneform` vyžaduje. Také zajistí vaší aplikaci zaměřuje `Sceneform` verze 1.7, protože to by měla odpovídat verzi ARCore vaše aplikace používá. Po této změně, může se zobrazit oznámení z Gradle s výzvou k synchronizaci: klikněte na tlačítko **synchronizovat nyní**.

```
android {
    ...

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.google.ar.sceneform.ux:sceneform-ux:1.7.0'
    ...
}
```

Otevřete váš `app\res\layout\activity_main.xml`a nahraďte existující Hello Wolrd `<TextView>` element s následující ArFragment. Tento kód způsobí, že zobrazený na obrazovce povolení ARCore sledovat svou pozici zařízení při jejich přesunu kanálu fotoaparátu funguje.

```xml
<fragment android:name="com.google.ar.sceneform.ux.ArFragment"
    android:id="@+id/ux_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

[Znovu nasadit](#trying-it-out) aplikaci tak, aby vaše zařízení, abyste ověřili, že ještě jednou. Tentokrát by měl vyzváni k fotoaparátu oprávnění. Po schválení, měli byste vidět fotoaparátu kanálu vykreslování na obrazovce.

## <a name="place-an-object-in-the-real-world"></a>Umístit objekt v reálném světě

Pojďme vytvořit & umístit objekt pomocí vaší aplikace. Nejprve přidejte následující importy do vaší `app\java\<PackageName>\MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=23-33)]

Pak přidejte následující proměnné členů do vaší `MainActivity` třídy:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=52-57)]

Dále přidejte následující kód do vašeho `app\java\<PackageName>\MainActivity` `onCreate()` metody. Tento kód se připojí naslouchací proces nazvaný `handleTap()`, který bude rozpoznat, kdy se uživatel klepl na obrazovku na zařízení. V případě vzoru tap bude v reálném světě povrch, který již rozpoznávaných ARCore pro sledování se spustí naslouchací proces.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-74,85&highlight=6-7)]

Nakonec přidejte následující `handleTap()` metoda, která vše spojí dohromady. Bude vytvořit kouli a umístěte ho na které jste klepli umístění. Oblasti budou zpočátku černé od `this.recommendedSessionProgress` je nastaven na hodnotu nula hned teď. Tato hodnota bude později upravit.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-171,174-182,198-199)]

[Znovu nasadit](#trying-it-out) aplikaci tak, aby vaše zařízení, abyste ověřili, že ještě jednou. Tentokrát se můžete pohybovat zařízení zobrazíte ARCore spustit uznání vašeho prostředí. Potom klepněte na obrazovku k vytváření a umístěte černé sphere na plochu podle vašeho výběru.

## <a name="attach-a-local-azure-spatial-anchor"></a>Připojení místní prostorových ukotvení Azure

Upravit `Gradle Scripts\build.gradle (Module: app)` přidejte následující položku. Tento kód se zajistit, aby vaše aplikace cíle ukotvení prostorových Azure verze 1.0.2. Ale nutné dodat, odkazuje na všechny nejnovější verzi Azure prostorových kotvy by mělo fungovat.

```
dependencies {
    ...
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_jni:[1.0.2]"
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_java:[1.0.2]"
    ...
}
```

Klikněte pravým tlačítkem na `app\java\<PackageName>` -> **nové**->**třídy Java**. Nastavte **název** k _Mojeprvníaplikace_, a **supertřídě** k _android.app.Application_. U ostatních možností ponechte, protože jde o. Klikněte na **OK**. Soubor s názvem `MyFirstApp.java` vytvoří. Přidejte do něj následující import:

```java
import com.microsoft.CloudServices;
```

Potom přidejte následující kód do nové `MyFirstApp` třídu, která zajistí prostorových kotvy Azure se inicializuje s kontextem vaší aplikace.

```java
    @Override
    public void onCreate() {
        super.onCreate();
        CloudServices.initialize(this);
    }
```

Nyní, upravte `app\manifests\AndroidManifest.xml` přidejte následující položku v kořenovém adresáři `<application>` uzlu. Tento kód bude připojení – třída aplikace, kterou jste vytvořili do vaší aplikace.

```xml
    <application
        android:name=".MyFirstApp"
        ...
    </application>
```

Zpět v `app\java\<PackageName>\MainActivity`, přidejte do něj následující importy:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=33-40&highlight=2-8)]

Pak přidejte následující proměnné členů do vaší `MainActivity` třídy:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=57-60&highlight=3-4)]

V dalším kroku přidejme následující `initializeSession()` metoda uvnitř vaší `mainActivity` třídy. Po zavolání zajistí relaci prostorových kotvy Azure se vytvoří a správně inicializován během spuštění aplikace.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-97,146)]

Teď můžeme zapojit vaše `initializeSession()` metodu do vaší `onCreate()` – metoda. Také jsme zajistíte, že snímky z kanálu fotoaparátu funguje odesílají do Azure prostorových kotvy SDK pro zpracování.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-85&highlight=9-17)]

Nakonec přidejte následující kód do vašeho `handleTap()` metody. Místní prostorových ukotvení Azure se bude připojení k černé oblasti, které jsme umísťování v reálném světě.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-182,198-199&highlight=12-13)]

[Znovu nasadit](#trying-it-out) ještě jednou vaše aplikace. Pohyb zařízení, klepněte na obrazovku a umístěte černé koule. Tentokrát ale kódu vytvářet a místní prostorových ukotvení Azure se připojuje k vaší oblasti.

Než budete pokračovat dále, budete muset vytvořit prostorových ukotvení Azure účtu identifikátor a klíč, pokud ještě nemáte je. Pomocí postupu v následující části je můžete získat.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Nahrání místního ukotvení do cloudu

Jakmile budete mít účet Azure prostorových kotvy identifikátor a klíč, jsme se můžete vrátit `app\java\<PackageName>\MainActivity`, přidejte do něj následující importy:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=40-45&highlight=3-6)]

Pak přidejte následující proměnné členů do vaší `MainActivity` třídy:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=60-65&highlight=3-6)]

Teď přidejte následující kód do vašeho `initializeSession()` metody. Nejprve, tento kód vám umožní vaši aplikaci pro sledování pokroku, sady SDK Azure prostorových kotvy díky shromažďuje snímky z kanálu fotoaparátu funguje. Stejně, barva vaší oblasti začne změna z jeho původní černé do šedá. Pak ji vypne bílé po dostatek snímků jsou shromažďovány pro odeslání vašeho ukotvení do cloudu. Tento kód bude kromě toho poskytují přihlašovací údaje potřebné ke komunikaci s back-end cloudu. Zde je, kde budete konfigurovat aplikace pro použití vašeho účtu identifikátor a klíč. Jste zkopírovali do textového editoru, když [nastavení zdroje prostorových kotvy](#create-a-spatial-anchors-resource).

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-120,142-146&highlight=11-36)]

V dalším kroku přidejte následující `uploadCloudAnchorAsync()` metoda uvnitř vaší `mainActivity` třídy. Po zavolání této metody asynchronně počká, až dostatek snímků se shromažďují ze svého zařízení. Poté, co se stane, že přepne barva vaší oblasti na žlutou a pak spustí nahrávání místní prostorových ukotvení Azure do cloudu. Po dokončení nahrávání kód vrátí identifikátor elementu anchor.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=uploadCloudAnchorAsync)]

Nakonec Pojďme integrovat všechno dohromady. Ve vaší `handleTap()` metodu, přidejte následující kód. Se vyvolá vaše `uploadCloudAnchorAsync()` metoda co nejdříve po vytvoření vaší oblasti. Jakmile se metoda vrátí hodnotu, následující kód provede jednu poslední aktualizaci do vaší oblasti, změníte jeho barvu na modrou.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-199&highlight=24-37)]

[Znovu nasadit](#trying-it-out) ještě jednou vaše aplikace. Pohyb zařízení, klepněte na obrazovku a umístěte vaší oblasti. Tentokrát ale vaší oblasti změní jeho barva od černé směrem k prázdné, jako fotoaparátu snímků se shromažďují. Jakmile budeme mít dostatek snímků, oblasti se změní na žlutou barvou a spustí se nahrávání cloudu. Po dokončení nahrávání se modře vaší oblasti. Volitelně můžete také použít `Logcat` okna v prostředí Android Studio pro monitorování zpráv protokolu vaše aplikace odesílá. Například zachycuje průběh relace během rámce a identifikátor ukotvení cloudu vrátí po odeslání se dokončí.

## <a name="locate-your-cloud-spatial-anchor"></a>Vyhledejte své cloudové prostorových kotvy

Jeden vaše ukotvení je nahraná na cloud, jsme připraveni pokusu o vyhledání znovu. Nejprve přidejte následující importy do kódu.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=45-48&highlight=3-4)]

Potom přidejte následující kód do vašeho `handleTap()` metody. Tento kód vám:

- Naše stávající modré sphere odeberte z obrazovky.
- Naše prostorových kotvy Azure relaci znovu inicializujte. Tím zajistíte, že anchor, které chceme vyhledejte pochází z cloudu místo místních anchor, který jsme vytvořili.
- Zadat dotaz na ukotvení, že jsme nahráli do cloudu.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=handleTap&highlight=10-19)]

Teď Pojďme upravíme kód, který bude vyvolán při ukotvení jsme se pro dotazování se nachází. Uvnitř vaší `initializeSession()` metodu, přidejte následující kód. Tento fragment kódu se vytvořit & na místě kouli zelené po ukotvení prostorových cloud nachází. Také umožní obrazovky klepněte znovu, takže můžete opakovat celý scénář ještě jednou: vytvořit jiného místního ukotvení, nahrajte ho a znovu ho najít.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=initializeSession&highlight=34-53)]

A to je vše! [Znovu nasadit](#trying-it-out) aplikace jeden poslední vyzkoušet si celý scénář koncového času. Pohyb zařízení a umístěte černé koule. Potom přesouvat zařízení pro zachycování snímků fotoaparát, dokud oblasti se změní na žlutou. Nahraje místní ukotvení a bude modře vaší oblasti. A konečně klepněte na obrazovku ještě jednou klikněte tak, aby místní ukotvení se odebere a pak jsme dotaz pro jeho protějšek cloudu. Pokračujte, dokud se nachází vaše cloudové prostorových ukotvení přesouvat zařízení. Zelená koule by se měla objevit ve správném umístění a může zasažení vodou a znovu opakujte celý scénář.

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-new-android-app-finished.md)]

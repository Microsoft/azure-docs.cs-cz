---
title: 'Kurz: Vytvoření nové aplikace pro Android'
description: V tomto kurzu se dozvíte, jak vytvořit novou aplikaci pro Android pomocí Azure Spatial Anchors.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 04/03/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: e1773ef81a5b727187a9a69ccc7ce7ad0421fb2c
ms.sourcegitcommit: 940e16ff194d5163f277f98d038833b1055a1a3e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2020
ms.locfileid: "80246768"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-android-app-using-azure-spatial-anchors"></a>Kurz: Podrobné pokyny k vytvoření nové aplikace pro Android pomocí prostorových ukotvení Azure

V tomto kurzu se vám ukáže, jak vytvořit novou aplikaci pro Android, která integruje funkce ARCore s prostorovými kotvami Azure.

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento kurz, ujistěte se, že máte následující:

- Počítač se systémem Windows nebo macOS se <a href="https://developer.android.com/studio/" target="_blank">systémem Android Studio 3.4+</a>.
- <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">Vývojář povoleno</a> a <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore schopné</a> zařízení Android.

## <a name="getting-started"></a>Začínáme

Spusťte Android Studio. V okně **Vítejte na Android Studio** klikněte na **Spustit nový projekt Android Studio**. Nebo pokud máte projekt již otevřený, vyberte **Soubor**->**nový projekt**.

V okně **Vytvořit nový projekt** v části Telefon a **tablet** zvolte **Vyprázdnit aktivitu**a klepněte na tlačítko **Další**. Potom v části Minimální `API 26: Android 8.0 (Oreo)`úroveň rozhraní **API**zvolte `Java`a ujistěte se, že je **jazyk** nastaven na . Můžete chtít změnit název projektu & umístění a název balíčku. Ostatní možnosti nechte tak, jak jsou. Klikněte na **Finish** (Dokončit). Spustí se **Instalační služba součásti.** Po dokončení klikněte na **Dokončit**. Po nějaké zpracování, Android Studio otevře ide.

## <a name="trying-it-out"></a>Vyzkoušet to

Chcete-li otestovat novou aplikaci, připojte zařízení s podporou vývojáře k vývojovému počítači pomocí kabelu USB. Klepněte na tlačítko **Spustit**->**aplikaci .** V okně **Vybrat cíl nasazení** vyberte zařízení a klepněte na tlačítko **OK**. Android Studio nainstaluje aplikaci do připojeného zařízení a spustí ji. Nyní byste měli vidět "Hello World!" v aplikaci spuštěné na vašem zařízení. Klikněte na **Spustit**->**stop 'aplikace'**.

## <a name="integrating-_arcore_"></a>Integrace _ARCore_

<a href="https://developers.google.com/ar/discover/" target="_blank">_ARCore_</a> je platforma společnosti Google pro budování zkušeností s rozšířenou realitou, která umožňuje vašemu zařízení sledovat jeho pozici, jak se pohybuje, a buduje vlastní chápání reálného světa.

Upravte `app\manifests\AndroidManifest.xml` tak, aby zahrnovala následující položky uvnitř kořenového `<manifest>` uzlu. Tento fragment kódu dělá několik věcí:

- Umožní vaší aplikaci přístup ke kameře zařízení.
- Zajistí také, že vaše aplikace bude viditelná pouze v Obchodě Google Play pro zařízení, která podporují ARCore.
- Nakonfiguruje obchod Google Play tak, aby stáhl a nainstaloval ARCore, pokud ještě není nainstalován, když je vaše aplikace nainstalována.

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera.ar" />

<application>
    ...
    <meta-data android:name="com.google.ar.core" android:value="required" />
    ...
</application>
```

Upravte `Gradle Scripts\build.gradle (Module: app)` tak, aby zahrnovala následující položku. Tento kód zajistí, že vaše aplikace cílí na ARCore verze 1.8. Po této změně můžete od Gradle dostat oznámení s žádostí o synchronizaci: klikněte na **Synchronizovat .**

```
dependencies {
    ...
    implementation 'com.google.ar:core:1.11.0'
    ...
}
```

## <a name="integrating-_sceneform_"></a>Integrace _sceneformu_

[_Sceneform_](https://developers.google.com/sceneform/develop/) usnadňuje vykreslování realistických 3D scén v aplikacích rozšířené reality, aniž byste se museli učit OpenGL.

Upravte `Gradle Scripts\build.gradle (Module: app)` tak, aby obsahoval následující položky. Tento kód umožní vaší aplikaci používat jazykové konstrukce `Sceneform` z Javy 8, což vyžaduje. Také zajistí, že `Sceneform` vaše aplikace se zaměřuje na verzi 1.8, protože by měla odpovídat verzi ARCore, kterou vaše aplikace používá. Po této změně můžete od Gradle dostat oznámení s žádostí o synchronizaci: klikněte na **Synchronizovat .**

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
    implementation 'com.google.ar.sceneform.ux:sceneform-ux:1.11.0'
    ...
}
```

Otevřete `app\res\layout\activity_main.xml`a nahraďte existující `<TextView>` prvek Hello Wolrd následujícím ArFragmentem. Tento kód způsobí, že se na obrazovce zobrazí podavač kamery, který umožní arcore sledovat polohu vašeho zařízení při pohybu.

```xml
<fragment android:name="com.google.ar.sceneform.ux.ArFragment"
    android:id="@+id/ux_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

[Znovu nasaďte](#trying-it-out) aplikaci do zařízení, abyste ji znovu ověřili. Tentokrát byste měli být požádáni o oprávnění kamery. Po schválení byste měli vidět vykreslování informačního kanálu fotoaparátu na obrazovce.

## <a name="place-an-object-in-the-real-world"></a>Umístěte objekt do reálného světa

Pojďme vytvořit & umístit objekt pomocí aplikace. Nejprve do aplikace `app\java\<PackageName>\MainActivity`přidejte následující importy :

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=23-33)]

Potom přidejte do `MainActivity` třídy následující členské proměnné:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=52-57)]

Dále přidejte následující kód `app\java\<PackageName>\MainActivity` `onCreate()` do metody. Tento kód připojí naslouchací proces s názvem `handleTap()`, který zjistí, když uživatel klepne na obrazovku v zařízení. Pokud se kohoutek stane na povrchu reálného světa, který již byl rozpoznán sledováním ARCore, naslouchací proces se spustí.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-74,85&highlight=6-7)]

Nakonec přidejte `handleTap()` následující metodu, která vše spojí dohromady. Vytvoří kouli a umístí ji na poklepané místo. Koule bude zpočátku černá, protože `this.recommendedSessionProgress` je nastavena na nulu právě teď. Tato hodnota bude upravena později.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-171,174-182,198-199)]

[Znovu nasaďte](#trying-it-out) aplikaci do zařízení, abyste ji znovu ověřili. Tentokrát se můžete pohybovat po zařízení, abyste arcore začali rozpoznávat vaše prostředí. Potom klepnutím na obrazovku vytvořte & umístěte černou kouli na povrch podle vašeho výběru.

## <a name="attach-a-local-azure-spatial-anchor"></a>Připojení místní prostorové kotvy Azure

Upravte `Gradle Scripts\build.gradle (Module: app)` tak, aby zahrnovala následující položku. Tento kód zajistí, že vaše aplikace cílí na Azure Spatial Anchors verze 2.2.0. To znamená, že odkazování na nejnovější verzi Azure Spatial Anchors by mělo fungovat. Poznámky k verzi naleznete [zde.](https://github.com/Azure/azure-spatial-anchors-samples/releases)

```
dependencies {
    ...
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_jni:[2.2.0]"
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_java:[2.2.0]"
    ...
}
```

Klepněte `app\java\<PackageName>` ->pravým tlačítkem myši na **novou**->**třídu Java**. Nastavte **název** na _MyFirstApp_a **Superclass** na _android.app.Application_. Ostatní možnosti nechte tak, jak jsou. Klikněte na tlačítko **OK**. Bude vytvořen `MyFirstApp.java` soubor s názvem. Přidejte do něj následující import:

```java
import com.microsoft.CloudServices;
```

Potom přidejte následující kód `MyFirstApp` do nové třídy, která zajistí, že azure prostorové kotvy je inicializována s kontextem vaší aplikace.

```java
    @Override
    public void onCreate() {
        super.onCreate();
        CloudServices.initialize(this);
    }
```

Nyní upravte `app\manifests\AndroidManifest.xml` tak, aby zahrnovala následující položku uvnitř kořenového `<application>` uzlu. Tento kód připojí třídu aplikace, kterou jste vytvořili, do aplikace.

```xml
    <application
        android:name=".MyFirstApp"
        ...
    </application>
```

Zpět `app\java\<PackageName>\MainActivity`do aplikace přidejte následující importy:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=33-40&highlight=2-8)]

Potom přidejte do `MainActivity` třídy následující členské proměnné:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=57-60&highlight=3-4)]

Dále přidáme následující `initializeSession()` metodu do `mainActivity` třídy. Po volání zajistí, že relace Azure Spatial Anchors se vytvoří a správně inicializuje během spuštění aplikace.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-97,146)]

Nyní pojďme připojit `initializeSession()` svou metodu do vaší `onCreate()` metody. Také zajistíme, že snímky z vašeho kanálu kamery jsou odesílány do sady Azure Spatial Anchors SDK ke zpracování.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-85&highlight=9-17)]

Nakonec přidejte následující kód `handleTap()` do metody. Připojí místní Azure Spatial Anchor k černé koule, kterou umisťujeme do reálného světa.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-182,198-199&highlight=12-13)]

[Znovu nasaďte](#trying-it-out) aplikaci ještě jednou. Pohybujte se kolem zařízení, klepněte na obrazovku a umístěte černou kouli. Tentokrát však váš kód bude vytvářet a připojovat místní Azure prostorové kotvy do vaší koule.

Než budete pokračovat dál, budete muset vytvořit identifikátor účtu Azure Spatial Anchors a klíč, pokud je ještě nemáte. Postupujte podle následující části, abyste je získali.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Nahrání místní kotvy do cloudu

Jakmile budete mít identifikátor účtu azure prostorových kotev `app\java\<PackageName>\MainActivity`a klíč, můžeme se vrátit do aplikace , přidat do něj následující importy:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=40-45&highlight=3-6)]

Potom přidejte do `MainActivity` třídy následující členské proměnné:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=60-65&highlight=3-6)]

Nyní přidejte následující kód `initializeSession()` do metody. Za prvé, tento kód umožní vaší aplikaci sledovat průběh, který azure prostorové kotvy SDK dělá, protože shromažďuje snímky z vašeho kanálu kamery. Jak to dělá, barva vaší koule se začne měnit z původní černé, do šedé. Poté se zbělá, jakmile bude shromážděno dostatečné množství snímků, aby bylo nutné odeslat kotvu do cloudu. Za druhé tento kód bude poskytovat přihlašovací údaje potřebné ke komunikaci s back-endu cloudu. Tady nakonfigurujete aplikaci tak, aby používala identifikátor a klíč vašeho účtu. Zkopírovali jste je do textového [editoru při nastavování prostředku Prostorové kotvy](#create-a-spatial-anchors-resource).

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-120,142-146&highlight=11-36)]

Dále přidejte `uploadCloudAnchorAsync()` následující metodu do třídy. `mainActivity` Po volání bude tato metoda asynchronně čekat, dokud se z vašeho zařízení nenasbírá dostatek rámců. Jakmile se to stane, přepne barvu koule na žlutou a pak začne nahrávat místní Azure Spatial Anchor do cloudu. Po dokončení nahrávání kód vrátí identifikátor kotvy.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=uploadCloudAnchorAsync)]

Konečně, pojďme připojit všechno dohromady. Do `handleTap()` metody přidejte následující kód. Vyvolá vaši `uploadCloudAnchorAsync()` metodu, jakmile bude vytvořena vaše koule. Jakmile se metoda vrátí, níže uvedený kód provede jednu konečnou aktualizaci vaší koule a změní její barvu na modrou.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-199&highlight=24-37)]

[Znovu nasaďte](#trying-it-out) aplikaci ještě jednou. Pohybujte se kolem zařízení, klepněte na obrazovku a umístěte svou kouli. Tentokrát však vaše koule změní barvu z černé na bílou, protože se shromažďují snímky fotoaparátu. Jakmile budeme mít dostatek rámů, koule se změní na žlutou a nahrávání mraků začne. Jakmile nahrávání skončí, vaše koule zmodrá. Volitelně můžete také použít `Logcat` okno uvnitř Aplikace Android Studio ke sledování zpráv protokolu, které vaše aplikace odesílá. Například průběh relace během zachycení rámce a identifikátor kotvy, který cloud vrátí po dokončení nahrávání.

## <a name="locate-your-cloud-spatial-anchor"></a>Vyhledání prostorové kotvy cloudu

Jedna z vašich kotvy je nahrána do cloudu, jsme připraveni se ji pokusit znovu najít. Nejprve přidáme do kódu následující importy.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=45-48&highlight=3-4)]

Potom přidáme následující kód do `handleTap()` metody. Tento kód bude:

- Odstraňte naši stávající modrou kouli z obrazovky.
- Inicializovat naše relace Azure prostorových kotvy znovu. Tato akce zajistí, že kotva, kterou najdeme, pochází z cloudu namísto místní kotvy, kterou jsme vytvořili.
- Vydejte dotaz na kotvu, kterou jsme nahráli do cloudu.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=handleTap&highlight=10-19)]

Nyní zavěste kód, který bude vyvolán, když je umístěna kotva, na kterou se dotazujeme. Uvnitř `initializeSession()` metody přidejte následující kód. Tento úryvek vytvoří & umístit zelenou kouli, jakmile bude umístěna prostorová kotva oblačnosti. To také umožní nahrávání na obrazovce znovu, takže můžete opakovat celý scénář ještě jednou: vytvořit další místní kotvu, nahrát ji a najít ji znovu.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=initializeSession&highlight=34-53)]

A to je vše! [Znovu nasadit](#trying-it-out) aplikaci naposledy vyzkoušet celý scénář od konce do konce. Pohybujte se kolem zařízení a umístěte černou kouli. Poté pokračujte v pohybu zařízení, abyste zachytili snímky kamery, dokud koule nezežloutne. Vaše místní kotva bude nahrána a vaše koule zmodrá. Nakonec klepněte na obrazovku ještě jednou, aby se odebrala místní kotva, a pak se podíváme na jeho protějšek v cloudu. Pokračujte v pohybu zařízení, dokud nebude umístěna prostorová kotva cloudu. Zelená koule by se měla zobrazit ve správném umístění a můžete znovu opláchnut& opakovat celý scénář.

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-new-android-app-finished.md)]

---
title: 'Kurz: vytvoření nové aplikace pro Android'
description: V tomto kurzu se naučíte, jak vytvořit novou aplikaci pro Android pomocí prostorových kotev Azure.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 06/22/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3ef24e29e5dde90aa829c46d789256e6e5f3233b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85296198"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-android-app-using-azure-spatial-anchors"></a>Kurz: podrobné pokyny k vytvoření nové aplikace pro Android pomocí prostorových kotev Azure

V tomto kurzu se dozvíte, jak vytvořit novou aplikaci pro Android, která integruje funkce ARCore s prostorovými kotvami Azure.

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento kurz, ujistěte se, že máte následující:

- Počítač s Windows nebo macOS, který má <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4 +</a>.
- Zařízení s Androidem podporující <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">vývojáře</a> a <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore</a> .

## <a name="getting-started"></a>Začínáme

Spusťte Android Studio. V okně **Vítá vás Android Studio** klikněte na **spustit nový projekt Android Studio**. Nebo, pokud máte projekt již otevřen, vyberte **soubor** -> **Nový projekt**.

V okně **vytvořit nový projekt** v části **telefon a tablet** vyberte možnost **prázdná aktivita**a klikněte na tlačítko **Další**. Pak v části **minimální úroveň rozhraní API**zvolte `API 26: Android 8.0 (Oreo)` a zkontrolujte, že je **jazyk** nastavený na `Java` . Je možné, že budete chtít změnit název projektu & umístění a název balíčku. Ostatní možnosti ponechte beze změny. Klikněte na **Finish** (Dokončit). Spustí se **instalační program součásti** . Až to bude hotové, klikněte na **Dokončit**. Po nějakém zpracování Android Studio otevře IDE.

## <a name="trying-it-out"></a>Vyzkoušení

Pokud chcete otestovat novou aplikaci, připojte zařízení s podporou vývojářů k vývojovému počítači pomocí kabelu USB. Klikněte na **Spustit** -> **Spustit aplikaci**. V okně **vybrat cíl nasazení** vyberte své zařízení a klikněte na **OK**. Android Studio nainstaluje aplikaci na připojené zařízení a spustí ji. Nyní by se měla zobrazit zpráva "Hello World!". Zobrazuje se v aplikaci běžící na vašem zařízení. Klikněte na **Spustit** -> **zastavit aplikaci**.

## <a name="integrating-_arcore_"></a>Integrace _ARCore_

<a href="https://developers.google.com/ar/discover/" target="_blank">_ARCore_</a> je platforma Google pro vytváření rozšířených funkcí reálného realit a umožňuje tak vašemu zařízení sledovat jeho polohu při přesunu a sestavuje své znalosti reálného světa.

Upravit `app\manifests\AndroidManifest.xml` tak, aby zahrnoval následující položky uvnitř kořenového `<manifest>` uzlu. Tento fragment kódu provede několik věcí:

- Umožní vaší aplikaci přístup k fotoaparátu vašeho zařízení.
- Tím zajistíte, že se vaše aplikace bude zobrazovat jenom v Obchod Google Play na zařízeních, která podporují ARCore.
- Nakonfiguruje Obchod Google Play ke stažení a instalaci ARCore, pokud už není nainstalovaná, když je vaše aplikace nainstalovaná.

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera.ar" />

<application>
    ...
    <meta-data android:name="com.google.ar.core" android:value="required" />
    ...
</application>
```

Upravte `Gradle Scripts\build.gradle (Module: app)` , aby obsahovala následující položku. Tento kód zajistí, že vaše aplikace bude cílena na verzi ARCore 1,8. Po této změně můžete obdržet oznámení od Gradle s výzvou k synchronizaci: klikněte na **synchronizovat hned**.

```
dependencies {
    ...
    implementation 'com.google.ar:core:1.11.0'
    ...
}
```

## <a name="integrating-_sceneform_"></a>Integrace _Sceneform_

[_Sceneform_](https://developers.google.com/sceneform/develop/) usnadňuje vykreslování realistické 3D scény v rozšířených aplikacích realit, aniž byste se museli učit OpenGL.

Upravte `Gradle Scripts\build.gradle (Module: app)` , aby obsahovala následující položky. Tento kód umožní vaší aplikaci používat jazykové konstrukce z jazyka Java 8, který `Sceneform` vyžaduje. Tím se také zajistí, že vaše aplikace cílí na `Sceneform` verzi 1,8, protože by se měla shodovat s verzí ARCore, kterou vaše aplikace používá. Po této změně můžete obdržet oznámení od Gradle s výzvou k synchronizaci: klikněte na **synchronizovat hned**.

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

Otevřete své `app\res\layout\activity_main.xml` a nahraďte existující element Hello wolrd `<TextView>` následujícím ArFragment. Tento kód způsobí, že se na obrazovce zobrazí informační kanál kamery umožňující ARCore sledovat polohu zařízení při jeho přesunutí.

```xml
<fragment android:name="com.google.ar.sceneform.ux.ArFragment"
    android:id="@+id/ux_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

[Znovu nasaďte](#trying-it-out) aplikaci do svého zařízení a znovu ji ověřte. Tentokrát byste měli požádat o oprávnění kamery. Po schválení by se na obrazovce mělo zobrazit vykreslování informačního kanálu kamery.

## <a name="place-an-object-in-the-real-world"></a>Umístit objekt do reálného světa

Pojďme vytvořit & umístit objekt pomocí vaší aplikace. Nejdřív přidejte následující importy do `app\java\<PackageName>\MainActivity` :

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=23-33)]

Pak do své třídy přidejte následující proměnné členů `MainActivity` :

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=52-57)]

Dále do své metody přidejte následující kód `app\java\<PackageName>\MainActivity` `onCreate()` . Tento kód zaznamená naslouchací proces, `handleTap()` který se zavolá, který zjistí, že uživatel na zařízení klepne na obrazovku. Pokud se klepnutí stane na reálném světovém povrchu, který již byl rozpoznán sledováním ARCore, spustí se naslouchací proces.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-74,85&highlight=6-7)]

Nakonec přidejte následující `handleTap()` metodu, která bude spojovat vše dohromady. Vytvoří objekt sphere a umístí jej na místo, na které jste klepli. Koule bude zpočátku černá, protože `this.recommendedSessionProgress` je teď nastavená nula. Tato hodnota se upraví později.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=151-159,171-172,175-183,199-200)]

[Znovu nasaďte](#trying-it-out) aplikaci do svého zařízení a znovu ji ověřte. Tentokrát se můžete pohybovat v rámci svého zařízení, abyste mohli ARCore začít rozpoznávat vaše prostředí. Pak klepněte na obrazovku, abyste vytvořili & umístit černou plochu přes plochu podle vašeho výběru.

## <a name="attach-a-local-azure-spatial-anchor"></a>Připojení místního prostorového ukotvení Azure

Upravte `Gradle Scripts\build.gradle (Module: app)` , aby obsahovala následující položku. Tento kód zajistí, že vaše aplikace bude cílena na prostorové kotvy Azure verze 2.2.0. Tato verze by odkazovala na jakoukoli poslední verzi prostorových kotev Azure, která by měla fungovat. Poznámky k verzi najdete [tady.](https://github.com/Azure/azure-spatial-anchors-samples/releases)

```
dependencies {
    ...
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_jni:[2.2.0]"
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_java:[2.2.0]"
    ...
}
```

Klikněte pravým tlačítkem na `app\java\<PackageName>` -> **novou** -> **třídu Java**. Nastavte **název** na _MojePrvníAplikace_a **supertřída** na _Android. app. Application_. Ostatní možnosti ponechte beze změny. Klikněte na **OK**. Vytvoří se soubor s názvem `MyFirstApp.java` . Přidejte do něj následující import:

```java
import com.microsoft.CloudServices;
```

Potom do nové třídy přidejte následující kód `MyFirstApp` , který zajistí, že jsou prostorové kotvy Azure inicializovány s kontextem vaší aplikace.

```java
    @Override
    public void onCreate() {
        super.onCreate();
        CloudServices.initialize(this);
    }
```

Nyní upravte, `app\manifests\AndroidManifest.xml` aby zahrnovala následující položku uvnitř kořenového `<application>` uzlu. Tento kód zařadí třídu aplikace, kterou jste vytvořili do vaší aplikace.

```xml
    <application
        android:name=".MyFirstApp"
        ...
    </application>
```

Zpátky do `app\java\<PackageName>\MainActivity` přidejte do něj následující importy:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=33-40&highlight=2-8)]

Pak do své třídy přidejte následující proměnné členů `MainActivity` :

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=57-60&highlight=3-4)]

Nyní přidáme do `initializeSession()` vaší třídy následující metodu `mainActivity` . Po volání se zajistí, že se vytvoří relace prostorových kotev Azure, která se při spuštění vaší aplikace správně inicializuje.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-97,147)]

Nyní zapojte `initializeSession()` metodu do vaší `onCreate()` metody. Také zajistíme, aby se snímky z vašeho informačního kanálu kamery odesílaly do sady SDK prostorových kotev Azure ke zpracování.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-85&highlight=9-17)]

Nakonec do své metody přidejte následující kód `handleTap()` . Připojí místní prostor Azure do černé koule, kterou umísťujeme do reálného světa.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=151-159,171-183,199-200&highlight=12-13)]

[Znovu nasaďte](#trying-it-out) aplikaci ještě jednou. Pohybujte zařízení, klepněte na obrazovku a umístěte černou koule. Tentokrát ale váš kód bude vytvářet a připojovat místní prostorové ukotvení Azure do vaší koule.

Než budete pokračovat, budete muset vytvořit účet prostorových kotev Azure, abyste získali identifikátor účtu, klíč a doménu, pokud je ještě nemáte. Pokud je chcete získat, postupujte podle následujících částí.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Nahrání místního kotvy do cloudu

Jakmile budete mít identifikátor účtu, klíč a doménu prostorových ukotvení Azure, můžeme se zpátky vrátit do portálu `app\java\<PackageName>\MainActivity` a přidat do něj následující importy:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=40-45&highlight=3-6)]

Pak do své třídy přidejte následující proměnné členů `MainActivity` :

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=60-65&highlight=3-6)]

Nyní do své metody přidejte následující kód `initializeSession()` . Nejprve tento kód umožní vaší aplikaci monitorovat průběh, který sada SDK prostorových ukotvení vytvoří při shromažďování snímků z kanálu kamery. V takovém případě se barva vaší koule začne měnit z původní černé na šedá. Pak se v případě, že se shromáždí dostatek snímků pro odeslání kotvy do cloudu, zapíná bíle. Za druhé tento kód poskytne přihlašovací údaje potřebné ke komunikaci s back-end cloudu. Tady můžete nakonfigurovat aplikaci tak, aby používala identifikátor účtu, klíč a doménu. Při [nastavování prostředku prostorových ukotvení](#create-a-spatial-anchors-resource)jste je zkopírovali do textového editoru.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-120,142-148&highlight=11-37)]

Dále do `uploadCloudAnchorAsync()` vaší třídy přidejte následující metodu `mainActivity` . Po zavolání bude tato metoda asynchronně čekat, dokud nebudou ze zařízení shromažďovány dostatečné snímky. Jakmile k tomu dojde, změní se barva vaší koule na žlutou a potom se začne nahrávat vaše místní prostorové ukotvení Azure do cloudu. Po dokončení nahrávání kód vrátí identifikátor kotvy.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=uploadCloudAnchorAsync)]

Nakonec připojovat všechno dohromady. Do `handleTap()` metody přidejte následující kód. Vyvolá vaši `uploadCloudAnchorAsync()` metodu, jakmile bude vaše koule vytvořena. Jakmile se metoda vrátí, kód níže provede jednu poslední aktualizaci vaší koule, přičemž změna barvy na modrou.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=151-159,171-200&highlight=24-37)]

[Znovu nasaďte](#trying-it-out) aplikaci ještě jednou. Pohybujte na svém zařízení, klepněte na obrazovku a umístěte svoji koule. Tentokrát ale vaše koule změní barvu z černé na bílou, protože se shromažďují snímky kamery. Jakmile máme dostatek snímků, koule se změní na žlutou a spustí se nahrávání do cloudu. Až se nahrávání dokončí, vaše koule se změní na modrou. V případě potřeby můžete také použít `Logcat` okno uvnitř Android Studio k monitorování protokolů zpráv, které vaše aplikace posílá. Například průběh relace během zachycení snímku a identifikátor kotvy, který Cloud vrátí po dokončení nahrávání.

## <a name="locate-your-cloud-spatial-anchor"></a>Najděte své cloudové kotvy

Jedna vaše kotva se nahraje do cloudu, můžeme se pokusit o jejich vyhledání znovu. Nejprve přidáme následující importy do kódu.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=45-48&highlight=3-4)]

Pak přidejte do vaší metody následující kód `handleTap()` . Tento kód bude:

- Z obrazovky odeberte existující modrou koule.
- Znovu inicializujte naši relaci prostorové kotvy Azure. Tato akce zajistí, že kotva, kterou vyhledáme, pochází z cloudu namísto místního ukotvení, které jsme vytvořili.
- Vydejte dotaz na kotvu, kterou jsme nahráli do cloudu.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=handleTap&highlight=10-19)]

Nyní připravujeme kód, který bude vyvolán, když se na něj nachází kotva, pro kterou se dotazuje. V rámci `initializeSession()` metody přidejte následující kód. Tento fragment kódu vytvoří & umístit zelenou koule po umístění cloudové kotvy. Také se znovu aktivuje obrazovka, takže můžete celý scénář zopakovat, a to ještě jednou: vytvořte další místní kotvu, nahrajte ho a znovu ho vyhledejte.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=initializeSession&highlight=34-53)]

A je to! [Opětovným nasazením](#trying-it-out) aplikace z poslední doby můžete vyzkoušet celý scénář a ukončit ho. Pohybujte kolem zařízení a umístěte černou koule. Pak můžete pokračovat ve svém zařízení a zachytit snímky z kamery, dokud se koule nezmění žlutě. Vaše místní kotva se nahraje a vaše koule se zachová modře. Nakonec klepněte na obrazovku ještě jednou, aby se vaše místní kotva odebrala, a pak se podíváme na svůj cloudový protějšek. Pokračujte v přesouvání zařízení, dokud se neumístí cloudové ukotvení. Zelená koule by se měla zobrazit ve správném umístění a můžete ji vypláchněte & opakujte celý scénář.

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-new-android-app-finished.md)]

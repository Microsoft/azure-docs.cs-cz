---
title: 'Rychlý start: Rozpoznávání řeči v Javě na Androidu s využitím sady Speech SDK'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak rozpoznávat řeč v Javě na Androidu s využitím sady Speech SDK.
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: wolfma
ms.openlocfilehash: 0785383d8b5a8ab282d8097d5229fa5a8aade06f
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219422"
---
# <a name="quickstart-recognize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Rychlý start: Rozpoznávání řeči v Javě na Androidu s využitím sady Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku se dozvíte, jak v Javě vytvořit aplikaci pro Android pro přepis řeči na text pomocí sady Cognitive Services Speech SDK.
Tato aplikace je založená na balíčku Maven sady Microsoft Cognitive Services Speech SDK verze 1.1.0 a Android Studiu 3.1.
Sada Speech SDK je aktuálně kompatibilní se zařízeními s Androidem, která používají 32bitové nebo 64bitové procesory ARM a kompatibilní procesory Intel x86 nebo x64.

> [!NOTE]
> Informace o sadě Speech Devices SDK a zařízení Roobo najdete v sadě [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu potřebujete klíč předplatného služby Speech. Můžete ho získat zdarma. Podrobnosti najdete v článku [Vyzkoušejte si službu Speech zdarma](get-started.md).

## <a name="create-and-configure-a-project"></a>Vytvoření a konfigurace projektu

1. Spusťte Android Studio a v uvítacím okně zvolte **Start a new Android Studio projekt** (Začít nový projekt Android Studia).

    ![Snímek obrazovky s uvítacím oknem Android Studia](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. Zobrazí se průvodce **Create New Project** (Vytvořit nový projekt). Na obrazovce **Create Android Project** (Vytvořit projekt pro Android) zadejte **Quickstart** jako **název aplikace**, **samples.speech.cognitiveservices.microsoft.com** jako **doménu firmy** a pak zvolte adresář projektu. Políčka C++ a Kotlin nechejte nezaškrtnutá a vyberte **Next** (Další).

   ![Snímek obrazovky průvodce vytvořením nového projektu](media/sdk/qs-java-android-02-create-android-project.png)

1. Na obrazovce **Target Android Devices** (Cílová zařízení s Androidem) vyberte jen **Phone and Tablet** (Telefon a tablet). V rozevíracím seznamu dole zvolte **API 23: Android 6.0 (Marshmallow)** a vyberte **Next** (Další).

   ![Snímek obrazovky průvodce vytvořením nového projektu](media/sdk/qs-java-android-03-target-android-devices.png)

1. Na obrazovce **Add an Activity to Mobile** (Přidat aktivitu pro mobil) vyberte **Empty Activity** (Prázdná aktivita) a klikněte na **Next** (Další).

   ![Snímek obrazovky průvodce vytvořením nového projektu](media/sdk/qs-java-android-04-add-an-activity-to-mobile.png)

1. Na obrazovce **Configure Activity** (Konfigurovat aktivitu) použijte **MainActivity** jako název aktivity a **activity\_main** jako název rozložení. Zaškrtněte obě políčka a vyberte **Finish** (Dokončit).

   ![Snímek obrazovky průvodce vytvořením nového projektu](media/sdk/qs-java-android-05-configure-activity.png)

Android Studiu chvíli trvá, než nový projekt pro Android připraví. V dalším kroku nakonfigurujete v tomto projektu sadu Speech SDK a jazyk Java 8.

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Aktuální verze sady Cognitive Services Speech SDK je `1.1.0`.

Sada Speech SDK pro Android je zabalená jako [AAR (knihovna pro Android)](https://developer.android.com/studio/projects/android-library) a obsahuje potřebné knihovny, stejně jako požadovaná oprávnění Androidu k jejímu používání.
Je hostovaná v úložišti Maven na adrese https://csspeechstorage.blob.core.windows.net/maven/.

Nastavte projekt tak, aby používal sadu Speech SDK. Výběrem příkazu **File (Soubor)** > **Project Structure (Struktura projektu)** na řádku nabídek Android Studia otevřete okno se strukturou projektu. V okně se strukturou projektu udělejte následující změny: 

1. V seznamu na levé straně okna vyberte **Project** (Projekt). Upravte nastavení **Default Library Repository** (Výchozí úložiště knihoven) připojením čárky a adresy URL úložiště Maven v jednoduchých uvozovkách. 'https://csspeechstorage.blob.core.windows.net/maven/'

   ![Snímek obrazovky okna se strukturou projektu](media/sdk/qs-java-android-06-add-maven-repository.png)

1. Na stejné obrazovce vyberte na levé straně **app** (Aplikace). Pak vyberte kartu **Dependencies** (Závislosti) v horní části okna. Vyberte zelené znaménko plus (+) a v rozevírací nabídce zvolte **Library dependency** (Závislost na knihovně).

   ![Snímek obrazovky okna se strukturou projektu](media/sdk/qs-java-android-07-add-module-dependency.png)

1. V okně, které se zobrazí, zadejte název a verzi naší sady Speech SDK pro Android – `com.microsoft.cognitiveservices.speech:client-sdk:1.1.0`. Pak vyberte **OK**.
   Sada Speech SDK by teď měla být přidaná do seznamu závislostí, jak je znázorněno níže:

   ![Snímek obrazovky okna se strukturou projektu](media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Vyberte kartu **Properties** (Vlastnosti). U polí **Source Compatibility** (Kompatibilita zdroje) a **Target Compatibility** (Kompatibilita cíle) vyberte **1.8**.

   ![](media/sdk/qs-java-android-09-dependency-added.png)

1. Výběrem tlačítka **OK** zavřete okno se strukturou projektu a uplatněte na projekt provedené změny.

## <a name="create-user-interface"></a>Vytvoření uživatelského rozhraní

Vytvoříme základní uživatelské rozhraní pro aplikaci. Upravte rozložení hlavní aktivity `activity_main.xml`. Rozložení zpočátku obsahuje záhlaví s názvem vaší aplikace a element TextView obsahující text „Hello World!“.

* Klikněte na element TextView. Změňte jeho atribut ID v pravém horním rohu na `hello`.

* Z palety v levém horním rohu okna `activity_main.xml` přetáhněte tlačítko na prázdné místo nad textem.

* V atributech tlačítka na pravé straně zadejte u atributu `onClick` hodnotu `onSpeechButtonClicked`. Napíšeme metodu s tímto názvem, která bude obsluhovat událost tohoto tlačítka.  Změňte jeho atribut ID v pravém horním rohu na `button`.

* Pomocí ikony kouzelné hůlky v horní části návrháře vymezte omezení rozložení.

  ![Snímek obrazovky s ikonou kouzelné hůlky](media/sdk/qs-java-android-10-infer-layout-constraints.png)

Textová a grafická podoba uživatelského rozhraní by teď měla vypadat nějak takto.

<table>
<tr>
<td valign="top">
![](media/sdk/qs-java-android-11-gui.png)
</td>
<td valign="top">
[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]
</td>
</tr>
</table>

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete zdrojový soubor `MainActivity.java`. Veškerý kód v tomto souboru nahraďte následujícím kódem.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Metoda `onCreate` obsahuje kód, který požádá o oprávnění k mikrofonu a internetu a inicializuje vazby nativní platformy. Konfigurace vazeb nativní platformy se vyžaduje jen jednou. Měla by se provést v ranné fázi během inicializace aplikace.
   
   * Metoda `onSpeechButtonClicked` je obslužná rutina pro kliknutí na tlačítko, jak bylo uvedeno výše. Stisknutí tlačítka aktivuje přepis řeči na text.

1. Ve stejném souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.

1. Také řetězec `YourServiceRegion` nahraďte [oblastí](regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. Připojte k vývojovému počítači zařízení s Androidem. Nezapomeňte na tomto zařízení povolit [režim vývoje a ladění USB](https://developer.android.com/studio/debug/dev-options).

1. Stisknutím kláves Ctrl+F9 nebo volbou příkazu **Build (Sestavení)** > **Make Project (Vytvořit projekt)** na řádku nabídek aplikaci sestavte.

1. Aplikaci spustíte stisknutím kláves Shift+F10 nebo volbou příkazu **Run (Spustit)** >  **Run „aplikace“ (Spustit „aplikace“)**.

1. V okně s cílem nasazení, které se zobrazí, zvolte zařízení s Androidem.

   ![Snímek obrazovky okna s výběrem cíle nasazení](media/sdk/qs-java-android-12-deploy.png)

Stisknutím tlačítka v aplikaci spusťte rozpoznávání řeči. Následujících 15 sekund řeči v angličtině bude odesláno službě Speech a přepsáno. Výsledek se zobrazí v aplikaci pro Android a v okně logcat v Android Studiu.

![Snímek obrazovky aplikace pro Android](media/sdk/qs-java-android-13-gui-on-device.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Vyhledejte tuto ukázku ve složce `quickstart/java-android`.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Rozpoznávání záměrů z řeči pomocí sady Speech SDK pro Javu](how-to-recognize-intents-from-speech-java.md)

## <a name="see-also"></a>Viz také

- [Překlad řeči](how-to-translate-speech-csharp.md)
- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)

---
title: 'Rychlý start: Rozpoznávání řeči, jazyka Java (Android) – hlasové služby'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak rozpoznávat řeč v jazyce Java v Androidu pomocí sadou SDK pro řeč
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: wolfma
ms.openlocfilehash: 690656449fdb86c200a8978f0e17db562e4abbca
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62118208"
---
# <a name="quickstart-recognize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Rychlý start: Rozpoznávání řeči v jazyce Java v Androidu pomocí sadou SDK pro řeč

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku se dozvíte, jak vyvíjet aplikace v Javě pro Android pomocí Cognitive Services SDK řeči přepisy převod řeči na text.
Aplikace vychází z balíčku Maven sady SDK pro řeč, verze 1.4.0 a Android Studio 3.3.
Sada Speech SDK je aktuálně kompatibilní se zařízeními s Androidem, která používají 32bitové nebo 64bitové procesory ARM a kompatibilní procesory Intel x86 nebo x64.

> [!NOTE]
> Informace o sadě Speech Devices SDK a zařízení Roobo najdete v sadě [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Požadavky

Klíč předplatného hlasové služby k dokončení tohoto rychlého startu potřebujete. Můžete ho získat zdarma. Zobrazit [hlasové služby si můžete vyzkoušet zdarma](get-started.md) podrobnosti.

## <a name="create-and-configure-a-project"></a>Vytvoření a konfigurace projektu

1. Spusťte Android Studio a v uvítacím okně zvolte **Start a new Android Studio projekt** (Začít nový projekt Android Studia).

    ![Snímek obrazovky s uvítacím oknem Android Studia](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. **Zvolte projekt** průvodce se zobrazí, vyberte **telefony a tablety** a **prázdná aktivita** ve výběru aktivit. Vyberte **Další**.

   ![Snímek obrazovky zvolte projektu průvodce](media/sdk/qs-java-android-02-target-android-devices.png)

1. V **nakonfigurujete svůj projekt** obrazovky, zadejte **rychlý Start** jako **název**, **samples.speech.cognitiveservices.microsoft.com** jako **Název balíčku**a vyberte adresář projektu. Pro **úroveň rozhraní API minimální** vyberte **rozhraní API 23: Android 6.0 (Marshmallow)**, ponechte všechna ostatní zaškrtávací políčka není zaškrtnuto a vyberte **Dokončit**.

   ![Snímek obrazovky Konfigurace Průvodce projektu](media/sdk/qs-java-android-03-create-android-project.png)

Android Studiu chvíli trvá, než nový projekt pro Android připraví. V dalším kroku nakonfigurujete v tomto projektu sadu Speech SDK a jazyk Java 8.

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Aktuální verze sady Cognitive Services Speech SDK je `1.4.0`.

Sadou SDK pro řeč pro Android je zabalena jako [AAR (knihovna pro Android)](https://developer.android.com/studio/projects/android-library), který obsahuje potřebné knihovny a požadovaná oprávnění pro Android.
Je hostován v úložiště Maven v protokolu https:\//csspeechstorage.blob.core.windows.net/maven/.

Nastavte projekt tak, aby používal sadu Speech SDK. Výběrem příkazu **File (Soubor)** > **Project Structure (Struktura projektu)** na řádku nabídek Android Studia otevřete okno se strukturou projektu. V okně se strukturou projektu udělejte následující změny:

1. V seznamu na levé straně okna vyberte **Project** (Projekt). Upravte nastavení **Default Library Repository** (Výchozí úložiště knihoven) připojením čárky a adresy URL úložiště Maven v jednoduchých uvozovkách. 'https:\//csspeechstorage.blob.core.windows.net/maven/'

   ![Snímek obrazovky okna se strukturou projektu](media/sdk/qs-java-android-06-add-maven-repository.png)

1. Na stejné obrazovce vyberte na levé straně **app** (Aplikace). Pak vyberte kartu **Dependencies** (Závislosti) v horní části okna. Vyberte zelené znaménko plus (+) a v rozevírací nabídce zvolte **Library dependency** (Závislost na knihovně).

   ![Snímek obrazovky okna se strukturou projektu](media/sdk/qs-java-android-07-add-module-dependency.png)

1. V okně, které se zobrazí, zadejte název a verzi naší sady Speech SDK pro Android – `com.microsoft.cognitiveservices.speech:client-sdk:1.4.0`. Pak vyberte **OK**.
   Sada Speech SDK by teď měla být přidaná do seznamu závislostí, jak je znázorněno níže:

   ![Snímek obrazovky okna se strukturou projektu](media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Vyberte kartu **Properties** (Vlastnosti). U polí **Source Compatibility** (Kompatibilita zdroje) a **Target Compatibility** (Kompatibilita cíle) vyberte **1.8**.

   ![](media/sdk/qs-java-android-09-dependency-added.png)

1. Výběrem tlačítka **OK** zavřete okno se strukturou projektu a uplatněte na projekt provedené změny.

## <a name="create-user-interface"></a>Vytvoření uživatelského rozhraní

Vytvoříme základní uživatelské rozhraní pro aplikaci. Upravte rozložení hlavní aktivity `activity_main.xml`. Na začátku rozložení obsahuje záhlaví s názvem vaší aplikace a TextView obsahující text "Hello World!".

* Klikněte na element TextView. Změňte jeho atribut ID v pravém horním rohu na `hello`.

* Z palety v levém horním rohu okna `activity_main.xml` přetáhněte tlačítko na prázdné místo nad textem.

* V atributech tlačítka na pravé straně zadejte u atributu `onClick` hodnotu `onSpeechButtonClicked`. Napíšeme metodu s tímto názvem, která bude obsluhovat událost tohoto tlačítka.  Změňte jeho atribut ID v pravém horním rohu na `button`.

* Pomocí ikony kouzelné hůlky v horní části návrháře vymezte omezení rozložení.

  ![Snímek obrazovky s ikonou kouzelné hůlky](media/sdk/qs-java-android-10-infer-layout-constraints.png)

Textové a grafické vyjádření uživatelském rozhraní by teď měl vypadat takto:

![](media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]

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

Stisknutím tlačítka v aplikaci spusťte rozpoznávání řeči. Další 15 sekund anglické řeči se odesílají do hlasové služby a převede. Výsledek se zobrazí v aplikaci pro Android a v okně logcat v Android Studiu.

![Snímek obrazovky aplikace pro Android](media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte ukázky v Javě na Githubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Další informace najdete v tématech

- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)

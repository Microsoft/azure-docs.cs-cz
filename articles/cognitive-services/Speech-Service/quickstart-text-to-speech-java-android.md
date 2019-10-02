---
title: 'Rychlý Start: syntetizace řeči, Java (Android) – Speech Service'
titleSuffix: Azure Cognitive Services
description: Naučte se, jak syntetizovat řeč v Java v Androidu pomocí sady Speech SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/19/2019
ms.author: yulili
ms.openlocfilehash: 17fb0c1a4ba5495506ceb658eb61d0ac2959caf8
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71804038"
---
# <a name="quickstart-synthesize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Rychlý Start: syntetizace řeči v Java v Androidu pomocí sady Speech SDK

K dispozici jsou také rychlé starty pro [rozpoznávání řeči a funkce](quickstart-java-android.md) pro [virtuální asistenty hlasu First](quickstart-virtual-assistant-java-android.md).

V tomto článku se dozvíte, jak vyvíjet aplikace Java pro Android pomocí sady Cognitive Services Speech SDK pro syntetizování řeči před textem.
Aplikace je založena na balíčku sady Speech SDK Maven, verze 1.7.0 a Android Studio 3,3.
Sada Speech SDK je aktuálně kompatibilní se zařízeními s Androidem, která používají 32bitové nebo 64bitové procesory ARM a kompatibilní procesory Intel x86 nebo x64.

> [!NOTE]
> Informace o sadě Speech Devices SDK a zařízení Roobo najdete v sadě [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto rychlého startu budete potřebovat klíč předplatného služby Speech Services. Můžete ho získat zdarma. Další informace najdete v tématu o [bezplatném pokusu o hlasové služby](get-started.md) .

## <a name="create-and-configure-a-project"></a>Vytvoření a konfigurace projektu

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>Vytvoření uživatelského rozhraní

Vytvoříme základní uživatelské rozhraní pro aplikaci. Upravte rozložení hlavní aktivity `activity_main.xml`. Ve výchozím rozložení obsahuje záhlaví název vaší aplikace a TextView obsahující text "Hello World!".

1. Klikněte na element TextView. Změňte jeho atribut ID v pravém horním rohu na `outputMessage` a přetáhněte ho na spodní obrazovku. Odstraňte jeho text.

1. Z palety v levém horním rohu okna `activity_main.xml` přetáhněte tlačítko na prázdné místo nad textem.

1. V atributech tlačítka na pravé straně zadejte u atributu `onClick` hodnotu `onSpeechButtonClicked`. Napíšeme metodu s tímto názvem, která bude obsluhovat událost tohoto tlačítka.  Změňte jeho atribut ID v pravém horním rohu na `button`.

1. Přetáhněte prostý text do prostoru nad tlačítko; změňte jeho atribut ID na `speakText` a změňte atribut text na `Hi there!`.

1. Pomocí ikony kouzelné hůlky v horní části návrháře vymezte omezení rozložení.


    ![Snímek obrazovky s ikonou kouzelné hůlky](media/sdk/qs-java-android-10-infer-layout-constraints.png)

Text a grafická reprezentace vašeho uživatelského rozhraní by teď měla vypadat takto:

![](media/sdk/qs-java-android-11-2-tts-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/java-android/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete zdrojový soubor `MainActivity.java`. Veškerý kód v tomto souboru nahraďte následujícím kódem.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Metoda `onSpeechButtonClicked` je obslužná rutina pro kliknutí na tlačítko, jak bylo uvedeno výše. Stisknutí tlačítka spustí syntézu řeči.

1. Ve stejném souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.

1. Také řetězec `YourServiceRegion` nahraďte [oblastí](regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. Připojte k vývojovému počítači zařízení s Androidem. Nezapomeňte na tomto zařízení povolit [režim vývoje a ladění USB](https://developer.android.com/studio/debug/dev-options). Případně vytvořte emulátor pro [Android](https://developer.android.com/studio/run/emulator).

1. Stisknutím kláves Ctrl+F9 nebo volbou příkazu **Build (Sestavení)**  > **Make Project (Vytvořit projekt)** na řádku nabídek aplikaci sestavte.

1. Aplikaci spustíte stisknutím kláves Shift+F10 nebo volbou příkazu **Run (Spustit)**  >  **Run „aplikace“ (Spustit „aplikace“)** .

1. V okně cíl nasazení, které se zobrazí, vyberte zařízení s Androidem nebo emulátor.

   ![Snímek obrazovky okna s výběrem cíle nasazení](media/sdk/qs-java-android-12-deploy.png)

Zadejte text a stisknutím tlačítka v aplikaci zahajte oddíl syntézy řeči. Vyslechnete si syntetizované zvuky z výchozího mluvčího a na obrazovce se zobrazí informace o @no__t 0.

![Snímek obrazovky aplikace pro Android](media/sdk/qs-java-android-13-2-gui-on-device-tts.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zkoumání ukázek Java na GitHubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Další informace najdete v tématech

- [Přizpůsobení hlasových písem](how-to-customize-voice-font.md)
- [Záznam ukázek hlasu](record-custom-voice-samples.md)

---
title: 'Rychlý Start: syntetizace řeči, Java (Android) – Speech Service'
titleSuffix: Azure Cognitive Services
description: Naučte se, jak syntetizovat řeč v Java v Androidu pomocí sady Speech SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yulili
ms.openlocfilehash: 8f3a6c808cec28d3c2184bfd99aa2f5b1f6f88a3
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2020
ms.locfileid: "86226418"
---
## <a name="prerequisites"></a>Předpoklady

Než začnete, nezapomeňte:

> [!div class="checklist"]
> * [Vytvoření prostředku Azure Speech](../../../../get-started.md)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=android&pivots=programming-language-java)

## <a name="create-user-interface"></a>Vytvoření uživatelského rozhraní

Vytvoříme základní uživatelské rozhraní pro aplikaci. Upravte rozložení hlavní aktivity `activity_main.xml`. Ve výchozím rozložení obsahuje záhlaví název vaší aplikace a TextView obsahující text "Hello World!".

1. Klikněte na element TextView. Změňte jeho atribut ID v pravém horním rohu na a `outputMessage` přetáhněte ho na spodní obrazovku. Odstraňte jeho text.

1. Z palety v levém horním rohu okna `activity_main.xml` přetáhněte tlačítko na prázdné místo nad textem.

1. V atributech tlačítka na pravé straně zadejte u atributu `onClick` hodnotu `onSpeechButtonClicked`. Napíšeme metodu s tímto názvem, která bude obsluhovat událost tohoto tlačítka.  Změňte jeho atribut ID v pravém horním rohu na `button`.

1. Přetáhněte prostý text do prostoru nad tlačítko; změňte jeho atribut ID na a `speakText` změňte atribut text na `Hi there!` .

1. Pomocí ikony kouzelné hůlky v horní části návrháře vymezte omezení rozložení.


    ![Snímek obrazovky s ikonou kouzelné hůlky](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-10-infer-layout-constraints.png)

Text a grafická reprezentace vašeho uživatelského rozhraní by teď měla vypadat takto:

![Snímek obrazovky s uživatelským rozhraním, které by mělo vypadat](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-11-2-tts-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/text-to-speech/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete zdrojový soubor `MainActivity.java`. Veškerý kód v tomto souboru nahraďte následujícím kódem.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/text-to-speech/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Metoda `onSpeechButtonClicked` je obslužná rutina pro kliknutí na tlačítko, jak bylo uvedeno výše. Stisknutí tlačítka spustí syntézu řeči.

1. Ve stejném souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.

1. Také řetězec `YourServiceRegion` nahraďte [oblastí](~/articles/cognitive-services/Speech-Service/regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

## <a name="build-and-run-the-app"></a>Sestavte a spusťte aplikaci.

1. Připojte k vývojovému počítači zařízení s Androidem. Nezapomeňte na tomto zařízení povolit [režim vývoje a ladění USB](https://developer.android.com/studio/debug/dev-options). Případně vytvořte emulátor pro [Android](https://developer.android.com/studio/run/emulator).

1. Chcete-li sestavit aplikaci, stiskněte klávesy CTRL + F9 nebo **Build**  >  z panelu nabídek zvolte sestavit**vytvořit projekt** .

1. Chcete-li spustit aplikaci, stiskněte klávesy SHIFT + F10 nebo zvolte možnost **Spustit**  >  **běh aplikace**.

1. V okně cíl nasazení, které se zobrazí, vyberte zařízení s Androidem nebo emulátor.

   ![Snímek obrazovky okna s výběrem cíle nasazení](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

Zadejte text a stisknutím tlačítka v aplikaci zahajte oddíl syntézy řeči. Vyslechnete si syntetizované zvuky z výchozího mluvčího a na obrazovce se zobrazí `speech synthesis succeeded` informace.

![Snímek obrazovky aplikace pro Android](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-2-gui-on-device-tts.png)

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Viz také

- [Vytvoření vlastního hlasu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Zaznamenat vlastní ukázky hlasu](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)

---
title: 'Rychlý Start: rozpoznávání řeči, Java (Android) – Speech Service'
titleSuffix: Azure Cognitive Services
description: Naučte se rozpoznávat řeč v Java v Androidu pomocí sady Speech SDK
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 2fb7bc0851868f6551ed57d742c4005550caec99
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675578"
---
# <a name="quickstart-recognize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Rychlý start: Rozpoznávání řeči v Javě na Androidu s využitím sady Speech SDK

K dispozici jsou také rychlé starty pro [syntézu řeči a funkce](quickstart-text-to-speech-java-android.md) pro [virtuální asistenty hlasu First](quickstart-virtual-assistant-java-android.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku se naučíte vyvíjet aplikaci Java pro Android pomocí sady Azure Cognitive Services Speech SDK pro přepisovat řeči na text.

Aplikace je založená na balíčku sady Speech SDK Maven a Android Studio 3,3. Sada Speech SDK je aktuálně kompatibilní se zařízeními s Androidem, které mají 64 32 procesory kompatibilní s architekturou ARM a Intel x86/x64.

> [!NOTE]
> Informace o sadě Speech Devices SDK a zařízení Roobo najdete v sadě [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto rychlého startu budete potřebovat klíč předplatného služby Speech Services. Můžete ho získat zdarma. Další informace najdete v tématu [bezplatné vyzkoušení služby Speech](get-started.md).

## <a name="create-and-configure-a-project"></a>Vytvoření a konfigurace projektu

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-a-user-interface"></a>Vytvoření uživatelského rozhraní

Nyní vytvoříme pro aplikaci základní uživatelské rozhraní. Upravte rozložení hlavní aktivity `activity_main.xml`. Ve výchozím rozložení obsahuje záhlaví název vaší aplikace a TextView, který obsahuje text "Hello World!".

* Vyberte element TextView. Změňte jeho atribut ID v pravém horním rohu na `hello`.

* Z palety v levém horním rohu okna `activity_main.xml` přetáhněte tlačítko do prázdného místa nad textem.

* V atributech tlačítka na pravé straně zadejte u atributu `onClick` hodnotu `onSpeechButtonClicked`. Napíšeme metodu s tímto názvem, která bude obsluhovat událost tohoto tlačítka. Změňte jeho atribut ID v pravém horním rohu na `button`.

* Pomocí ikony kouzelné hůlky v horní části návrháře vymezte omezení rozložení.

  ![Snímek obrazovky s ikonou kouzelné hůlky](media/sdk/qs-java-android-10-infer-layout-constraints.png)

Text a grafická reprezentace vašeho uživatelského rozhraní by teď měla vypadat takto:

![Uživatelské rozhraní](media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete zdrojový soubor `MainActivity.java`. Nahraďte celý kód v tomto souboru následujícím kódem:

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Metoda `onCreate` obsahuje kód, který požádá o oprávnění k mikrofonu a internetu a inicializuje vazby nativní platformy. Konfigurace vazeb nativní platformy se vyžaduje jen jednou. Měla by se provést v ranné fázi během inicializace aplikace.

   * Metoda `onSpeechButtonClicked` je obslužná rutina pro kliknutí na tlačítko, jak bylo uvedeno výše. Stisknutí tlačítka aktivuje přepis řeči na text.

1. Ve stejném souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.

1. Nahraďte řetězec `YourServiceRegion` také [oblastí](regions.md) přidruženým k vašemu předplatnému. Například pro předplatné bezplatné zkušební verze použijte `westus`.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. Připojte k vývojovému počítači zařízení s Androidem. Ujistěte se, že jste na zařízení povolili [režim vývoje a ladění USB](https://developer.android.com/studio/debug/dev-options) .

1. Chcete-li sestavit aplikaci, vyberte CTRL + F9 nebo v řádku nabídek vyberte **sestavit**  > **vytvořit projekt** .

1. Pokud chcete aplikaci spustit, vyberte SHIFT + F10 nebo vyberte **spustit**  > **Spustit aplikaci**.

1. V okně cíl nasazení, které se zobrazí, vyberte své zařízení s Androidem.

   ![Snímek obrazovky okna s výběrem cíle nasazení](media/sdk/qs-java-android-12-deploy.png)

Výběrem tlačítka v aplikaci zahajte oddíl rozpoznávání řeči. Do služby Speech Services a přepisu se pošle další 15 sekund anglického hlasu. Výsledek se zobrazí v aplikaci pro Android a v okně logcat v Android Studiu.

![Snímek obrazovky aplikace pro Android](media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zkoumání ukázek Java na GitHubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Další informace najdete v tématech

- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)

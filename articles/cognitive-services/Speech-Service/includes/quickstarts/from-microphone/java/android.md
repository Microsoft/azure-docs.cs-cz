---
title: 'Rychlý Start: rozpoznávání řeči pomocí mikrofonu, Java (Android) – Speech Service'
titleSuffix: Azure Cognitive Services
description: Naučte se rozpoznávat řeč v Java v Androidu pomocí sady Speech SDK
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: wolfma
ms.openlocfilehash: e492381c4ab2f7ab41b5363ed70f9c5c6b12ce99
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818986"
---
## <a name="prerequisites"></a>Předpoklady

Než začnete:

> [!div class="checklist"]
> * [Vytvoření prostředku Azure Speech](../../../../get-started.md)
> * [Nastavení vývojového prostředí](../../../../quickstarts/setup-platform.md?tabs=android)
> * Ujistěte se, že máte přístup k mikrofonu pro záznam zvuku

## <a name="create-a-user-interface"></a>Vytvoření uživatelského rozhraní

Nyní vytvoříme pro aplikaci základní uživatelské rozhraní. Upravte rozložení hlavní aktivity `activity_main.xml`. Ve výchozím rozložení obsahuje záhlaví název vaší aplikace a TextView, který obsahuje text "Hello World!".

* Vyberte element TextView. Změňte jeho atribut ID v pravém horním rohu na `hello`.

* Z palety v levém horním rohu okna `activity_main.xml` přetáhněte tlačítko do prázdného místa nad textem.

* V atributech tlačítka na pravé straně zadejte u atributu `onClick` hodnotu `onSpeechButtonClicked`. Napíšeme metodu s tímto názvem, která bude obsluhovat událost tohoto tlačítka. Změňte jeho atribut ID v pravém horním rohu na `button`.

* Pomocí ikony kouzelné hůlky v horní části návrháře vymezte omezení rozložení.

  ![Snímek obrazovky s ikonou kouzelné hůlky](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-10-infer-layout-constraints.png)

Text a grafická reprezentace vašeho uživatelského rozhraní by teď měla vypadat takto:

![Uživatelské rozhraní](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete zdrojový soubor `MainActivity.java`. Nahraďte celý kód v tomto souboru následujícím kódem:

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Metoda `onCreate` obsahuje kód, který požádá o oprávnění k mikrofonu a internetu a inicializuje vazby nativní platformy. Konfigurace vazeb nativní platformy se vyžaduje jen jednou. Měla by se provést v ranné fázi během inicializace aplikace.

   * Metoda `onSpeechButtonClicked` je obslužná rutina pro kliknutí na tlačítko, jak bylo uvedeno výše. Stisknutí tlačítka aktivuje přepis řeči na text.

1. Ve stejném souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.

1. Nahraďte řetězec `YourServiceRegion` také [oblastí](~/articles/cognitive-services/Speech-Service/regions.md) přidruženým k vašemu předplatnému. Použijte například `westus` pro předplatné bezplatné zkušební verze.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. Připojte k vývojovému počítači zařízení s Androidem. Ujistěte se, že jste na zařízení povolili [režim vývoje a ladění USB](https://developer.android.com/studio/debug/dev-options) .

1. Chcete-li sestavit aplikaci, vyberte CTRL + F9 nebo v řádku nabídek vyberte **sestavit** > **vytvořit projekt** .

1. Pokud chcete aplikaci spustit, vyberte SHIFT + F10 nebo vyberte **spustit** > **Spustit aplikaci**.

1. V okně cíl nasazení, které se zobrazí, vyberte své zařízení s Androidem.

   ![Snímek obrazovky okna s výběrem cíle nasazení](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

Výběrem tlačítka v aplikaci zahajte oddíl rozpoznávání řeči. Následujících 15 sekund řeči v angličtině bude odesláno službě Speech a přepsáno. Výsledek se zobrazí v aplikaci pro Android a v okně logcat v Android Studiu.

![Snímek obrazovky aplikace pro Android](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]


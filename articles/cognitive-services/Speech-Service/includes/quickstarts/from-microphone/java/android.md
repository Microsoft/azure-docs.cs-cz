---
title: 'Úvodní příručka: Rozpoznávání řeči z mikrofonu, Java (Android) - Služba řeči'
titleSuffix: Azure Cognitive Services
description: Naučte se rozpoznávat řeč v Jazyce Java v Systému Android pomocí sady Speech SDK
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/05/2019
ms.author: wolfma
ms.openlocfilehash: 2641464e52b80c59b6c6b5a123ad1605c1d41495
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81275154"
---
## <a name="prerequisites"></a>Požadavky

Než začnete:

> [!div class="checklist"]
> * [Vytvoření řečového prostředku Azure](../../../../get-started.md)
> * [Nastavení vývojového prostředí](../../../../quickstarts/setup-platform.md?tabs=android&pivots=programming-language-java)
> * Ujistěte se, že máte přístup k mikrofonu pro snímání zvuku

## <a name="create-a-user-interface"></a>Vytvoření uživatelského rozhraní

Nyní vytvoříme základní uživatelské rozhraní pro aplikaci. Upravte rozložení hlavní aktivity `activity_main.xml`. Zpočátku rozložení obsahuje záhlaví s názvem aplikace a TextView, který obsahuje text "Hello World!".

* Vyberte element TextView. Změňte jeho atribut ID v pravém horním rohu na `hello`.

* Z palety v levém `activity_main.xml` horním rohu okna přetáhněte tlačítko do prázdného prostoru nad textem.

* V atributech tlačítka na pravé straně zadejte u atributu `onClick` hodnotu `onSpeechButtonClicked`. Napíšeme metodu s tímto názvem, která bude obsluhovat událost tohoto tlačítka. Změňte jeho atribut ID v pravém horním rohu na `button`.

* Pomocí ikony kouzelné hůlky v horní části návrháře vymezte omezení rozložení.

  ![Snímek obrazovky s ikonou kouzelné hůlky](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-10-infer-layout-constraints.png)

Text a grafické znázornění uživatelského rozhraní by teď měly vypadat takto:

![Uživatelské rozhraní](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete zdrojový soubor `MainActivity.java`. Nahraďte veškerý kód v tomto souboru následujícím:

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Metoda `onCreate` obsahuje kód, který požádá o oprávnění k mikrofonu a internetu a inicializuje vazby nativní platformy. Konfigurace vazeb nativní platformy se vyžaduje jen jednou. Měla by se provést v ranné fázi během inicializace aplikace.

   * Metoda `onSpeechButtonClicked` je obslužná rutina pro kliknutí na tlačítko, jak bylo uvedeno výše. Stisknutí tlačítka spustí přepis řeči na text.

1. Ve stejném souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.

1. Řetězec také `YourServiceRegion` nahraďte **identifikátorem oblasti** z [oblasti](https://aka.ms/speech/sdkregion) přidružené k vašemu předplatnému. Můžete například `westus` použít bezplatné zkušební předplatné.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. Připojte k vývojovému počítači zařízení s Androidem. Ujistěte se, že jste v zařízení povolili [režim vývoje a ladění USB.](https://developer.android.com/studio/debug/dev-options)

1. Chcete-li vytvořit aplikaci, vyberte Ctrl+F9 nebo z řádku nabídek vyberte **Vytvořit** > **projekt.**

1. Chcete-li aplikaci spustit, vyberte Shift+F10 nebo **možnost Spustit** > **spustit aplikaci**.

1. V okně cíle nasazení, které se zobrazí, vyberte zařízení Android.

   ![Snímek obrazovky okna s výběrem cíle nasazení](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

Vyberte tlačítko v aplikaci a začněte oddíl rozpoznávání řeči. Následujících 15 sekund řeči v angličtině bude odesláno službě Speech a přepsáno. Výsledek se zobrazí v aplikaci pro Android a v okně logcat v Android Studiu.

![Snímek obrazovky aplikace pro Android](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

---
title: 'Rychlý Start: rozpoznávání řeči na mikrofonu, objektivní-C-Speech Service'
titleSuffix: Azure Cognitive Services
description: Naučte se rozpoznávat řeč v cíli – C v iOS pomocí sady Speech SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 0d353b115acbf48bfffa6c2033b507721de501c5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503616"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-by-using-the-speech-sdk"></a>Rychlý Start: rozpoznávání řeči v cíli – C v iOS pomocí sady Speech SDK

K dispozici jsou také rychlé starty pro [syntézu řeči](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-ios.md).

V tomto článku se naučíte, jak vytvořit aplikaci pro iOS v cíli – C pomocí sady Azure Cognitive Services Speech SDK pro přepisovat řeči na text z mikrofonu nebo ze souboru se zvukovým nahrávkou.

## <a name="prerequisites"></a>Požadavky

Než začnete, budete potřebovat:

* [Klíč předplatného](~/articles/cognitive-services/Speech-Service/get-started.md) pro službu rozpoznávání řeči
* MacOS počítač s [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) nebo novějším.
* Cílová sada pro iOS verze 9,3 nebo novější.

## <a name="get-the-speech-sdk-for-ios"></a>Získání sady Speech SDK pro iOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Sada Cognitive Services Speech SDK pro iOS je aktuálně distribuována jako rozhraní pro kakao.
Dá se stáhnout z [tohoto webu](https://aka.ms/csspeech/iosbinary). Stáhněte si tento soubor do svého domovského adresáře.

## <a name="create-an-xcode-project"></a>Vytvoření projektu Xcode

Spusťte Xcode a spusťte nový projekt tak, že vyberete **soubor** > **Nový** > **projekt**.
V dialogovém okně Výběr šablony vyberte šablonu aplikace s **jedním zobrazením pro iOS** .

V následujících dialogových oknech proveďte následující výběry.

1. V dialogovém okně **Možnosti projektu** :
    1. Zadejte název aplikace pro rychlý Start, například *HelloWorld*.
    1. Zadejte název příslušné organizace a identifikátor organizace, pokud již máte účet Apple Developer. Pro účely testování použijte název jako *testorg*. K podepsání aplikace potřebujete správný zřizovací profil. Další informace najdete na [webu Apple Developer](https://developer.apple.com/).
    1. Ujistěte se, že je jako jazyk pro projekt vybrán **cíl-C** .
    1. Zrušte zaškrtnutí všech políček pro testy a základní data.

    ![Nastavení projektu](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-project-settings.png)

1. Vyberte adresář projektu:
   1. Zvolte svůj domovský adresář, do kterého se projekt umístí. Tento krok vytvoří adresář HelloWorld v domovském adresáři, který obsahuje všechny soubory projektu Xcode.
   1. Zakažte vytvoření úložiště Git pro tento ukázkový projekt.
   1. Nastavte cestu k sadě SDK na obrazovce nastavení projektu.
      1. Na kartě **Obecné** pod hlavičkou **vložená binární soubory** přidejte knihovnu SDK jako rozhraní, a to tak, že vyberete **Přidat vložené binární soubory** > **Přidat další**. Přejít do domovského adresáře a vybrat soubor `MicrosoftCognitiveServicesSpeech.framework`. Tato akce přidá knihovnu SDK do **odkazovaného rozhraní a knihoven** automaticky v hlavičce.
         rozhraní ![přidáno](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework.png)
      1. Otevřete kartu **nastavení sestavení** a vyberte nastavení **vše** .
      1. Přidejte adresář $ (SRCROOT)/.. do **cesty hledání architektury** pod hlavičkou **cesty hledání** .

      ![Nastavení cest hledání architektury](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>Nastavení uživatelského rozhraní

Ukázková aplikace má velmi jednoduché uživatelské rozhraní. Má dvě tlačítka ke spuštění rozpoznávání řeči buď ze souboru, nebo z vstupního mikrofonu a textového popisku pro zobrazení výsledku. Uživatelské rozhraní se nastavuje v části `Main.storyboard` projektu. Otevřete zobrazení XML scénáře tak, že kliknete pravým tlačítkem myši na položku `Main.storyboard` stromu projektu a vyberete **Otevřít jako** > **zdrojový kód**.

Nahraďte automaticky vygenerovaný kód XML tímto kódem:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

1. Stáhněte si [ukázkový soubor WAV](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) tak, že kliknete pravým tlačítkem na odkaz a vyberete **Uložit cíl jako**.
   Přetažením souboru wav z okna Finderu do kořenové úrovně zobrazení projektu přidejte soubor wav do projektu jako prostředek.
   V následujícím dialogovém okně vyberte **Dokončit** beze změny nastavení.
1. Obsah souboru automaticky generovaného `ViewController.m` nahraďte následujícím kódem:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/ViewController.m#code)]
1. Řetězec `YourSubscriptionKey` nahraďte klíčem předplatného.
1. Nahraďte řetězec `YourServiceRegion` [oblastí](~/articles/cognitive-services/Speech-Service/regions.md) přidruženým k vašemu předplatnému. Použijte například `westus` pro předplatné bezplatné zkušební verze.
1. Přidejte žádost o přístup k mikrofonu. Klikněte pravým tlačítkem myši na položku `Info.plist` stromu projektu a vyberte **Otevřít jako** > **zdrojový kód**. Do části `<dict>` přidejte následující řádky a potom soubor uložte.

    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

1. Zpřístupněte výstup ladění tak, že vyberete **zobrazení** > **oblast ladění** > **aktivovat konzolu**.
1. Jako cíl pro aplikaci ze seznamu v nabídce > **cílový** **produkt** vyberte buď simulátor iOS nebo zařízení s iOS připojené k vašemu vývojovému počítači.
1. Sestavte a spusťte vzorový kód v simulátoru iOS tak, že vyberete **produkt** > **Spustit** z nabídky. Můžete také vybrat tlačítko **Přehrát** .
1. Po výběru tlačítka **rozpoznat (soubor)** v aplikaci byste měli vidět obsah zvukového souboru "Co je to počasí, jako je?" „What's the weather like?“ (Jaké je počasí?).

   ![Simulovaná aplikace pro iOS](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-simulated-app.png)

1. Po výběru tlačítka **rozpoznat (mikrofon)** v aplikaci a vyslovení několika slov byste měli vidět text, který jste probrali v dolní části obrazovky.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zkoumání objektivů a ukázek v jazyce C na GitHubu](https://aka.ms/csspeech/samples)

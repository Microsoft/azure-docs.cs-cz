---
title: 'Úvodní příručka: Rozpoznávání řeči z mikrofonu, služba Objective-C - Speech Service'
titleSuffix: Azure Cognitive Services
description: Naučte se rozpoznávat řeč v objective-C v iOS pomocí sady Speech SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: chlandsi
ms.openlocfilehash: c1246b19670a18f8dadc0c5e1c64dd5af4c1b210
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75380775"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-by-using-the-speech-sdk"></a>Úvodní příručka: Rozpoznání řeči v objective-C v systému iOS pomocí sady Speech SDK

Rychlé starty jsou také k dispozici pro [syntézu řeči](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-ios.md).

V tomto článku se dozvíte, jak vytvořit aplikaci pro iOS v Objective-C pomocí Sady Azure Cognitive Services Speech SDK k přepisu řeči na text z mikrofonu nebo ze souboru se zaznamenaným zvukem.

## <a name="prerequisites"></a>Požadavky

Než začnete, budete potřebovat:

* [Klíč předplatného](~/articles/cognitive-services/Speech-Service/get-started.md) pro službu Řeč.
* Počítač s macOS s [Kódem Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) nebo novějším.
* Cíl nastavený na iOS verze 9.3 nebo novější.

## <a name="get-the-speech-sdk-for-ios"></a>Získání sady Speech SDK pro iOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Sada SDK řeči služby Cognitive Services pro iOS je aktuálně distribuována jako rámec kakaa.
To lze stáhnout z [této webové stránky](https://aka.ms/csspeech/iosbinary). Stáhněte si tento soubor do svého domovského adresáře.

## <a name="create-an-xcode-project"></a>Vytvoření projektu Xcode

Spusťte Xcode a začněte nový projekt výběrem **možnosti Soubor** > **nového** > **projektu**.
V dialogovém okně výběru šablony vyberte šablonu **aplikace pro jedno zobrazení iOS.**

V následujících dialogových oknech proveďte následující výběry.

1. V dialogovém okně **Možnosti projektu:**
    1. Zadejte název aplikace pro rychlý start, například *helloworld*.
    1. Pokud už máte vývojářský účet Apple, zadejte příslušný název organizace a identifikátor organizace. Pro účely testování použijte název jako *testorg*. Chcete-li podepsat aplikaci, potřebujete správný zřizovací profil. Další informace naleznete na [webu pro vývojáře společnosti Apple](https://developer.apple.com/).
    1. Ujistěte **se,** že cíl C je vybrán jako jazyk pro projekt.
    1. Zrušte zaškrtnutí všech políček pro testy a základní data.

    ![Nastavení projektu](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-project-settings.png)

1. Vyberte adresář projektu:
   1. Zvolte svůj domovský adresář, do kterého se projekt umístí. Tento krok vytvoří helloworld adresář ve vašem domovském adresáři, který obsahuje všechny soubory pro projekt Xcode.
   1. Zakažte vytvoření úložiště Git pro tento ukázkový projekt.
   1. Upravte cesty k sdk na obrazovce nastavení projektu.
      1. Na kartě **Obecné** v záhlaví **Vložené binární soubory** přidejte knihovnu Sady SDK jako architekturu výběrem **možnosti Přidat vložené binární soubory** > **Přidat další**. Přejděte do domovského adresáře a vyberte soubor `MicrosoftCognitiveServicesSpeech.framework`. Tato akce automaticky přidá knihovnu Sady SDK do záhlaví **Propojené rozhraní a knihovny.**
         ![Přidán rámec](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework.png)
      1. Přejděte na kartu **Nastavení sestavení** a vyberte nastavení **Vše.**
      1. Přidejte adresář $(SRCROOT)/.. k **cestě hledání v rámci** v záhlaví Cesty **hledání.**

      ![Nastavení cest hledání v rámci](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>Nastavení uživatelského rozhraní

Ukázková aplikace má velmi jednoduché ui. Má dvě tlačítka pro spuštění rozpoznávání řeči ze souboru nebo ze vstupu mikrofonu a textový popisek pro zobrazení výsledku. Uživatelské rozhraní se nastavuje v části `Main.storyboard` projektu. Otevřete zobrazení XML scénáře kliknutím pravým `Main.storyboard` tlačítkem myši na položku stromu projektu a výběrem **možnosti Otevřít jako** > **zdrojový kód**.

Nahraďte automaticky generovaný kód XML tímto kódem:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

1. Stáhněte [si ukázkový soubor wav](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) kliknutím pravým tlačítkem myši na odkaz a výběrem **možnosti Uložit cíl jako**.
   Přetažením souboru wav z okna Finderu do kořenové úrovně zobrazení projektu přidejte soubor wav do projektu jako prostředek.
   Vyberte **Dokončit** v následujícím dialogovém okně bez změny nastavení.
1. Nahraďte obsah automaticky `ViewController.m` generovaného souboru následujícím kódem:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/ViewController.m#code)]
1. Řetězec `YourSubscriptionKey` nahraďte klíčem předplatného.
1. Nahraďte `YourServiceRegion` řetězec [oblastí](~/articles/cognitive-services/Speech-Service/regions.md) přidruženou k vašemu předplatnému. Můžete například `westus` použít bezplatné zkušební předplatné.
1. Přidejte žádost o přístup k mikrofonu. Klepněte pravým `Info.plist` tlačítkem myši na položku stromu projektu a vyberte **příkaz Otevřít jako** > **zdrojový kód**. Do oddílu přidejte `<dict>` následující řádky a soubor uložte.

    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

1. Zviditelnění výstupu ladění výběrem **možnosti Zobrazit** > **zónu** > aktivace ladění **.**
1. Ze seznamu v nabídce **Product** > **Destination** vyberte simulátor iOS nebo iOS zařízení připojené k vývojovému počítači jako cíl aplikace.
1. Vytvořte a spusťte ukázkový kód v simulátoru iOS výběrem**spuštění** **produktu** > z nabídky. Můžete také vybrat tlačítko **Přehrát.**
1. Po výběru tlačítka **Rozpoznat (Soubor)** v aplikaci byste měli vidět obsah zvukového souboru "Jaké je počasí?" „What's the weather like?“ (Jaké je počasí?).

   ![Simulovaná aplikace pro iOS](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-simulated-app.png)

1. Po výběru tlačítka **Rozpoznat (mikrofon)** v aplikaci a říci pár slov, měli byste vidět text, který jste mluvili v dolní části obrazovky.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte ukázky objective-c na GitHubu](https://aka.ms/csspeech/samples)

---
title: 'Rychlý Start: rozpoznávání řeči, cíl-C-Speech Service'
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
ms.openlocfilehash: d87fd06b45d1d3636c442d962daa79cf526e74f2
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803272"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-using-the-speech-sdk"></a>Rychlý Start: rozpoznávání řeči v cíli – C v iOS pomocí sady Speech SDK

K dispozici jsou také rychlé starty pro [syntézu řeči](quickstart-text-to-speech-objectivec-ios.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku se naučíte, jak vytvořit aplikaci pro iOS v cíli – C pomocí sady Cognitive Services Speech SDK pro přepisovat řeči na text z mikrofonu nebo ze souboru se zvukovým nahrávkou.

## <a name="prerequisites"></a>Předpoklady

Než začnete, tady je seznam požadavků:

* [Klíč předplatného](get-started.md) pro službu pro rozpoznávání řeči
* MacOS počítač s [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) nebo novějším
* Cílová sada pro iOS verze 9,3 nebo novější

## <a name="get-the-speech-sdk-for-ios"></a>Získání sady Speech SDK pro iOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Sada Cognitive Services Speech SDK pro iOS je aktuálně distribuována jako rozhraní pro kakao.
Dá se stáhnout [odsud.](https://aka.ms/csspeech/iosbinary) Stáhněte si tento soubor do svého domovského adresáře.

## <a name="create-an-xcode-project"></a>Vytvoření projektu Xcode

Spusťte Xcode a kliknutím na **File** > **New** > **Project** (Soubor > Nový > Projekt) spusťte nový projekt.
V dialogovém okně pro výběr šablony zvolte šablonu iOS Single View App (Aplikace pro iOS s jedním zobrazením).

V následujících dialogových oknech proveďte následující výběry:

1. Dialogové okno Project Options (Možnosti projektu)
    1. Zadejte název aplikace Rychlý start, například `helloworld`.
    1. Pokud už máte účet Apple Developer, zadejte odpovídající název a identifikátor organizace. Pro účely testování stačí vybrat jakýkoli název, třeba `testorg`. K podepsání aplikace potřebujete správný zřizovací profil. Podrobnosti najdete na [webu pro vývojáře Apple](https://developer.apple.com/) .
    1. Ujistěte se, že jako jazyk projektu je zvolený jazyk Objective-C.
    1. Zrušte zaškrtnutí všech políček týkajících se testů a základních dat.
    ![Nastavení projektu](media/sdk/qs-objectivec-project-settings.png)
1. Vyberte adresář projektu.
    1. Zvolte svůj domovský adresář, do kterého se projekt umístí. Tím se v domovském adresáři vytvoří adresář `helloworld`, který obsahuje všechny soubory projektu Xcode.
    1. Zakažte vytvoření úložiště Git pro tento ukázkový projekt.
    1. V části *Project Settings* (Nastavení projektu) upravte cesty k sadě SDK.
        1. Na kartě **General** (Obecné) pod nadpisem **Embedded Binaries** (Vložené binární soubory) přidejte knihovnu sady SDK jako rozhraní: vyberte **Add embedded binaries** > **Add other...** (Přidat vložené binární soubory > Přidat jiné...), přejděte do svého domovského adresáře a zvolte soubor `MicrosoftCognitiveServicesSpeech.framework`. Tím se knihovna SDK přidá do **odkazovaného rozhraní a knihoven** automaticky v hlavičce.
        ![Přidané rozhraní](media/sdk/qs-objectivec-framework.png)
        1. Přejděte na kartu **Build Settings** (Nastavení sestavení) a aktivujte **všechna** nastavení.
        1. Do části *Framework Search Paths* (Cesty pro hledání rozhraní) pod nadpisem **Search Paths** (Cesty pro hledání) přidejte adresář `$(SRCROOT)/..`.
        ![Nastavení cesty pro hledání rozhraní](media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>Nastavení uživatelského rozhraní

Ukázková aplikace bude mít velmi jednoduché uživatelské rozhraní: dvě tlačítka pro spuštění rozpoznávání řeči ze souboru nebo vstupu z mikrofonu a textový popisek pro zobrazení výsledku.
Uživatelské rozhraní se nastavuje v části `Main.storyboard` projektu.
Otevřete zobrazení XML scénáře tak, že ve stromu projektu kliknete pravým tlačítkem na položku `Main.storyboard` a vyberete **Open As...**  > **Source Code** (Otevřít jako... > Zdrojový kód).
Nahraďte automaticky vygenerovaný kód XML tímto kódem:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

1. Stáhněte si [ukázkový soubor WAV](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) tak, že kliknete pravým tlačítkem na odkaz a zvolíte **Uložit cíl jako...** . Přidejte soubor WAV do projektu jako prostředek přetažením z okna Finder do kořenové úrovně zobrazení projektu.
   V následujícím dialogovém okně neměňte nastavení a klikněte na **Finish** (Dokončit).
1. Nahraďte obsah automaticky vygenerovaného souboru `ViewController.m` následujícím kódem:

   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/ViewController.m#code)]
1. Řetězec `YourSubscriptionKey` nahraďte klíčem předplatného.
1. Řetězec `YourServiceRegion` nahraďte [oblastí](regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).
1. Přidejte žádost o přístup k mikrofonu. Klikněte pravým tlačítkem myši na položku `Info.plist` stromu projektu a vyberte možnost **Otevřít jako...** **zdrojový kód** >  Do části `<dict>` přidejte následující řádky a pak soubor uložte.
    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

1. Zobrazte výstup ladění: vyberte **View** > **Debug Area** > **Activate Console** (Zobrazení > Oblast ladění > Aktivovat konzolu).
1. Vyberte buď simulátor iOS nebo zařízení s iOS připojená k vývojovému počítači jako cíl pro aplikaci ze seznamu v nabídce**cíle** **produktu** > .
1. Sestavte a spusťte ukázkový kód v simulátoru iOS výběrem možnosti **Product** > **Run** (Produkt > Spustit) v nabídce nebo kliknutím na tlačítko **Play** (Přehrát).
1. Po kliknutí na tlačítko „Recognize (File)“ (Rozpoznat – soubor) v aplikaci by se ve spodní části obrazovky měl zobrazit obsah zvukového souboru „What's the weather like?“ (Jaké je počasí?).

   ![Simulovaná aplikace pro iOS](media/sdk/qs-objectivec-simulated-app.png)

1. Po kliknutí na tlačítko „Recognize (Microphone)“ (Rozpoznat – mikrofon) v aplikaci a vyslovení několika slov by se ve spodní části obrazovky měl zobrazit vyslovený text.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zkoumání objektivů a ukázek v jazyce C na GitHubu](https://aka.ms/csspeech/samples)

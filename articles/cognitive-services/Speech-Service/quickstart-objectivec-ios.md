---
title: 'Rychlý start: Rozpoznávání řeči, jazyka Objective-C - hlasové služby'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak rozpoznávat řeč v Objective-C v iOS s využitím sady Speech SDK.
services: cognitive-services
author: chlandsi
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: chlandsi
ms.openlocfilehash: 824fa5ceb5828394fedfe7af8bf48af2980160d9
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53606312"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-using-the-speech-service-sdk"></a>Rychlý start: Rozpoznávat řeč v Objective-C v iOS pomocí sady SDK služby řeči

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku se dozvíte, jak v Objective-C vytvořit aplikaci pro iOS pro přepis zvukového souboru se záznamem řeči na text s využitím sady Cognitive Services Speech SDK.

## <a name="prerequisites"></a>Požadavky

Než začnete, tady je seznam požadovaných součástí:

* A [klíč předplatného](get-started.md) pro Speech Service
* Počítače s macOS s [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) nebo novější
* Cíl nastavena na systém iOS verze 11.4 nebo novější

## <a name="get-the-speech-sdk-for-ios"></a>Získání sady Speech SDK pro iOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Aktuální verze sady Cognitive Services Speech SDK je `1.2.0`.

Sada Cognitive Services Speech SDK pro Mac a iOS je v současné době distribuovaná jako rozhraní Cocoa.
Můžete si ho stáhnout na adrese https://aka.ms/csspeech/iosbinary. Stáhněte si tento soubor do svého domovského adresáře.

## <a name="create-an-xcode-project"></a>Vytvoření projektu Xcode

Spusťte Xcode a kliknutím na **File** > **New** > **Project** (Soubor > Nový > Projekt) spusťte nový projekt.
V dialogovém okně pro výběr šablony zvolte šablonu iOS Single View App (Aplikace pro iOS s jedním zobrazením).

V následujících dialogových oknech proveďte následující výběry:

1. Dialogové okno Project Options (Možnosti projektu)
    1. Zadejte název aplikace Rychlý start, například `helloworld`.
    1. Pokud už máte účet Apple Developer, zadejte odpovídající název a identifikátor organizace. Pro účely testování stačí vybrat jakýkoli název, třeba `testorg`. K podepsání aplikace, budete potřebovat správné zřizovacího profilu. Odkazovat [webu Apple developer](https://developer.apple.com/) podrobnosti.
    1. Ujistěte se, že jako jazyk projektu je zvolený jazyk Objective-C.
    1. Zrušte zaškrtnutí všech políček týkajících se testů a základních dat.
    ![Nastavení projektu](media/sdk/qs-objectivec-project-settings.png)
1. Vyberte adresář projektu.
    1. Zvolte svůj domovský adresář, do kterého se projekt umístí. Tím se vytvoří `helloworld` adresáře ve svém domovském adresáři, který obsahuje všechny soubory projektu Xcode.
    1. Zakažte vytvoření úložiště Git pro tento ukázkový projekt.
    1. V části *Project Settings* (Nastavení projektu) upravte cesty k sadě SDK.
        1. V **Obecné** kartu **vložených binárních souborů** záhlaví, přidání sady SDK knihovny jako rozhraní: **Přidat vložených binárních souborů** > **přidat další...**  > Přejít na domovský adresář a vyberte soubor `MicrosoftCognitiveServicesSpeech.framework`. Tento postup přidá hlavičku knihovny sady SDK **propojené rozhraní a knihovny** automaticky.
        ![Přidané rozhraní](media/sdk/qs-objectivec-framework.png)
        1. Přejděte na kartu **Build Settings** (Nastavení sestavení) a aktivujte **všechna** nastavení.
        1. Do části *Framework Search Paths* (Cesty pro hledání rozhraní) pod nadpisem **Search Paths** (Cesty pro hledání) přidejte adresář `$(SRCROOT)/..`.
        ![Nastavení cesty pro hledání rozhraní](media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>Nastavení uživatelského rozhraní

Ukázková aplikace bude mít velmi jednoduché uživatelské rozhraní: Dvě tlačítka spustit rozpoznávání řeči ze souboru nebo z vstup mikrofon a textový popisek k zobrazení výsledku.
Uživatelské rozhraní se nastavuje v části `Main.storyboard` projektu.
Otevřete zobrazení XML scénáře tak, že ve stromu projektu kliknete pravým tlačítkem na položku `Main.storyboard` a vyberete **Open As...** > **Source Code** (Otevřít jako... > Zdrojový kód).
Nahraďte automaticky generované XML s tímto kódem:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

1. Stáhněte si [ukázkový soubor wav](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) tak, že na odkaz kliknete pravým tlačítkem a zvolíte **Uložit cíl jako...**. Přetažením souboru wav z okna Finderu do kořenové úrovně zobrazení projektu přidejte soubor wav do projektu jako prostředek.
V následujícím dialogovém okně neměňte nastavení a klikněte na **Finish** (Dokončit).
1. Nahraďte obsah automaticky vygenerovaného souboru `ViewController.m` následujícím kódem:

   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/ViewController.m#code)]
1. Řetězec `YourSubscriptionKey` nahraďte klíčem předplatného.
1. Řetězec `YourServiceRegion` nahraďte [oblastí](regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).
1. Přidejte žádost o přístup k mikrofonu. Klikněte pravým tlačítkem myši `Info.plist` položka stromu projektu a vyberte **otevřít jako...**   >  **Zdrojový kód**. Do části `<dict>` přidejte následující řádky a pak soubor uložte.
    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="building-and-running-the-sample"></a>Sestavení a spuštění ukázky

1. Zobrazte výstup ladění: vyberte **View** > **Debug Area** > **Activate Console** (Zobrazení > Oblast ladění > Aktivovat konzolu).
1. V seznamu v nabídce **Product** -> **Destination** (Produkt > Cíl) zvolte jako cíl pro aplikaci simulátor iOS nebo zařízení iOS připojené k vašemu počítači pro vývoj.
1. Sestavte a spusťte ukázkový kód v simulátoru iOS výběrem možnosti **Product** -> **Run** (Produkt > Spustit) v nabídce nebo kliknutím na tlačítko **Play** (Přehrát).
Sada Speech SDK v současné době podporuje pouze 64bitové platformy iOS.
1. Po kliknutí na tlačítko „Recognize (File)“ (Rozpoznat – soubor) v aplikaci by se ve spodní části obrazovky měl zobrazit obsah zvukového souboru „What's the weather like?“ (Jaké je počasí?).

 ![Simulovaná aplikace pro iOS](media/sdk/qs-objectivec-simulated-app.png)

1. Po kliknutí na tlačítko „Recognize (Microphone)“ (Rozpoznat – mikrofon) v aplikaci a vyslovení několika slov by se ve spodní části obrazovky měl zobrazit vyslovený text.

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Vyhledejte tuto ukázku ve složce `quickstart/objectivec-ios`.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Získejte naše ukázky](speech-sdk.md#get-the-samples)

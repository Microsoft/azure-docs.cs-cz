---
title: 'Rychlý Start: rozpoznávání řeči pomocí mikrofonu, služby SWIFT – Speech'
titleSuffix: Azure Cognitive Services
description: Naučte se rozpoznávat řeč v SWIFT v systému iOS pomocí sady Speech SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: chlandsi
ms.openlocfilehash: 31f5339c70d52019400ca5f1fe873de4790a3bd6
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75380520"
---
# <a name="quickstart-recognize-speech-in-swift-on-ios-by-using-the-speech-sdk"></a>Rychlý Start: rozpoznávání řeči v SWIFT v systému iOS pomocí sady Speech SDK

K dispozici jsou také rychlé starty pro [syntézu řeči](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/swift-ios.md).

V tomto článku se naučíte, jak vytvořit aplikaci pro iOS v SWIFT pomocí sady Azure Cognitive Services Speech SDK pro přepisovat řeči zaznamenané z mikrofonu na text.

## <a name="prerequisites"></a>Požadavky

Než začnete, budete potřebovat:

* [Klíč předplatného](~/articles/cognitive-services/Speech-Service/get-started.md) pro službu rozpoznávání řeči
* MacOS počítač s nainstalovaným [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) nebo novějším a [CocoaPods](https://cocoapods.org/) .

## <a name="get-the-speech-sdk-for-ios"></a>Získání sady Speech SDK pro iOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Tento kurz nebude fungovat s verzí sady SDK starší než 1.6.0.

Sada Cognitive Services Speech SDK pro iOS je distribuována jako sada rozhraní. Dá se použít v projektech Xcode jako [CocoaPod](https://cocoapods.org/) nebo stahovat z https://aka.ms/csspeech/iosbinary a propojit ručně. Tento článek používá CocoaPod.

## <a name="create-an-xcode-project"></a>Vytvoření projektu Xcode

Spusťte Xcode a spusťte nový projekt tak, že vyberete **soubor** > **Nový** > **projekt**.
V dialogovém okně Výběr šablony vyberte šablonu aplikace s **jedním zobrazením pro iOS** .

V následujících dialogových oknech proveďte následující výběry.

1. V dialogovém okně **Možnosti projektu** :
    1. Zadejte název aplikace pro rychlý Start, například *HelloWorld*.
    1. Zadejte název příslušné organizace a identifikátor organizace, pokud už máte účet Apple Developer. Pro účely testování použijte název jako *testorg*. K podepsání aplikace potřebujete správný zřizovací profil. Další informace najdete na [webu Apple Developer](https://developer.apple.com/).
    1. Ujistěte se, že je pro projekt zvolený jazyk **SWIFT** .
    1. Zakázat zaškrtávací políčka pro použití scénářů a vytvoření aplikace založené na dokumentu. Jednoduché uživatelské rozhraní pro ukázkovou aplikaci se vytvoří programově.
    1. Zrušte zaškrtnutí všech políček pro testy a základní data.
1. Vyberte adresář projektu:
    1. Vyberte adresář, do kterého se má projekt umístit. Tento krok vytvoří adresář HelloWorld ve zvoleném adresáři, který obsahuje všechny soubory projektu Xcode.
    1. Zakažte vytvoření úložiště Git pro tento ukázkový projekt.
1. Aplikace musí také deklarovat použití mikrofonu v `Info.plist` souboru. V přehledu vyberte soubor a přidejte k rozpoznávání řeči klíč s **popisem používání mikrofonu – mikrofon** s hodnotou, jako *je třeba mikrofon*.

    ![Nastavení v souboru info. plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-info-plist.png)

1. Zavřete projekt Xcode. Jinou instanci můžete použít později po nastavení CocoaPods.

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

1. Do adresáře HelloWorld v rámci projektu HelloWorld umístěte `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` nový hlavičkový soubor s názvem. Vložte do něj následující kód:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]

1. Přidejte relativní cestu `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` k přemostění hlavičky do nastavení projektu SWIFT pro cíl HelloWorld v poli **Hlavička-přemostění v cíli C** .

   ![Vlastnosti záhlaví](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-bridging-header.png)

1. Obsah automaticky generovaného `AppDelegate.swift` souboru nahraďte následujícím kódem:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/AppDelegate.swift#code)]
1. Obsah automaticky generovaného `ViewController.swift` souboru nahraďte následujícím kódem:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/ViewController.swift#code)]
1. V `ViewController.swift`nahraďte řetězec `YourSubscriptionKey` pomocí vašeho klíče předplatného.
1. Nahraďte řetězec `YourServiceRegion` [oblastí](~/articles/cognitive-services/Speech-Service/regions.md) , která je přidružená k vašemu předplatnému. Použijte `westus` například pro předplatné bezplatné zkušební verze.

## <a name="install-the-sdk-as-a-cocoapod"></a>Instalace sady SDK jako CocoaPod

1. Nainstalujte správce závislostí CocoaPod, jak je popsáno v [pokynech k instalaci](https://guides.cocoapods.org/using/getting-started.html).
1. Přejít do adresáře ukázkové aplikace, což je HelloWorld. Umístěte textový soubor s názvem *souboru podfile* a následujícím obsahem v tomto adresáři:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/Podfile)]
1. Přejděte do adresáře HelloWorld v terminálu a spusťte příkaz `pod install`. Tento příkaz vygeneruje `helloworld.xcworkspace` pracovní prostor Xcode, který obsahuje ukázkovou aplikaci a sadu Speech SDK jako závislost. Tento pracovní prostor se používá v následujících krocích.

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

1. Otevřete pracovní prostor `helloworld.xcworkspace` v Xcode.
1. Zpřístupněte výstup ladění tak, že vyberete **Zobrazit** > **oblast** > ladění**aktivovat konzolu**.
1. Jako cíl pro aplikaci ze seznamu v nabídce**cílový** **produkt** > vyberte buď simulátor iOS nebo zařízení s iOS připojené k vývojovému počítači.
1. Sestavte a spusťte vzorový kód v simulátoru iOS tak, že v nabídce vyberete**Spustit** **produkt** > . Můžete také vybrat tlačítko **Přehrát** .
1. Po výběru tlačítka **rozpoznat** v aplikaci a vyslovení několika slov byste měli vidět text, který jste probrali v dolní části obrazovky.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte naše ukázky na GitHubu](https://aka.ms/csspeech/samples)

---
title: 'Úvodní příručka: Syntetizační řeč, Swift - Řečová služba'
titleSuffix: Azure Cognitive Services
description: Naučte se syntetizovat řeč v Swiftu v systému iOS pomocí sady Speech SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: 5fd7f125037777c55b748b45df49f8f5cd2d0409
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975886"
---
# <a name="quickstart-synthesize-speech-in-swift-on-ios-using-the-speech-sdk"></a>Úvodní příručka: Syntetizovat řeč v Swiftu v systému iOS pomocí sady Speech SDK

V tomto článku se dozvíte, jak vytvořit aplikaci pro iOS v Swiftpomocí sady Cognitive Services Speech SDK k syntéze řeči z textu.

## <a name="prerequisites"></a>Požadavky

Než začnete, tady je seznam předpokladů:

* [Klíč předplatného](~/articles/cognitive-services/Speech-Service/get-started.md) pro službu Řeč.
* Počítač s macOS s [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) nebo novějším a nainstalovanými [kakaopody.](https://cocoapods.org/)

## <a name="get-the-speech-sdk-for-ios"></a>Získání sady Speech SDK pro iOS

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Všimněte si, že tento kurz nebude fungovat s verzí sady SDK starší než 1.7.0.

Sada SDK řeči služby Cognitive Services pro iOS je distribuována jako sada architektury.
To může být použit v xcode projektech jako [CocoaPod](https://cocoapods.org/), nebo stáhnout z https://aka.ms/csspeech/macosbinary a souvisí ručně. Tato příručka používá Kakaový pod.

## <a name="create-an-xcode-project"></a>Vytvoření projektu Xcode

Spusťte Xcode a začněte nový projekt klepnutím na **položku Soubor** > **nového** > **projektu**.
V dialogovém okně pro výběr šablony zvolte šablonu iOS Single View App (Aplikace pro iOS s jedním zobrazením).

V následujících dialogových oknech proveďte následující výběry:

1. Dialogové okno Project Options (Možnosti projektu)
    1. Zadejte název aplikace Rychlý start, například `helloworld`.
    1. Pokud už máte vývojářský účet Apple, zadejte příslušný název organizace a identifikátor organizace. Pro účely testování stačí vybrat jakýkoli název, třeba `testorg`. Chcete-li podepsat aplikaci, potřebujete správný zřizovací profil. Podrobnosti najdete na [webu pro vývojáře společnosti Apple.](https://developer.apple.com/)
    1. Ujistěte se, že swift je vybrán jako jazyk pro projekt.
    1. Zaškrtávací políčka chcete použít scénáře a vytvořit aplikaci založenou na dokumentu. Jednoduché ui pro ukázkovou aplikaci bude vytvořeno programově.
    1. Zrušte zaškrtnutí všech políček týkajících se testů a základních dat.
1. Vyberte adresář projektu.
    1. Zvolte adresář, do který chcete projekt vložit. Tím se `helloworld` vytvoří adresář ve zvoleném adresáři, který obsahuje všechny soubory pro projekt Xcode.
    1. Zakažte vytvoření úložiště Git pro tento ukázkový projekt.
1. Zavřete projekt Xcode. Po nastavení kakaopodů budete později použít jinou instanci.

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

1. Umístěte nový soubor záhlaví `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` s `helloworld` názvem do adresáře uvnitř projektu Helloworld a vložte do něj následující kód:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/text-to-speech/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Přidání relativní `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` cesty k přemostění záhlaví do nastavení projektu Swift pro cíl helloworld ve vlastnostech záhlaví pole ![ *Přemostění cíle Cíle C*](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-bridging-header.png)
1. Nahraďte obsah automaticky vygenerovaného souboru `AppDelegate.swift` následujícím kódem:  
   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/text-to-speech/helloworld/helloworld/AppDelegate.swift#code)]
1. Nahraďte obsah automaticky vygenerovaného souboru `ViewController.swift` následujícím kódem:  
   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/text-to-speech/helloworld/helloworld/ViewController.swift#code)]
1. V `ViewController.swift`aplikaci `YourSubscriptionKey` nahraďte řetězec klíčem předplatného.
1. Řetězec `YourServiceRegion` nahraďte [oblastí](~/articles/cognitive-services/Speech-Service/regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

## <a name="install-the-sdk-as-a-cocoapod"></a>Instalace sady SDK jako kakaového podu

1. Nainstalujte Správce závislostí CocoaPod, jak je popsáno v [jeho pokynech k instalaci](https://guides.cocoapods.org/using/getting-started.html).
1. Přejděte do adresáře ukázkové aplikace (`helloworld`). Do adresáře umístěte `Podfile` textový soubor s názvem a následujícím obsahem:  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/text-to-speech/helloworld/Podfile)]
1. Přejděte `helloworld` do adresáře v terminálu a spusťte příkaz `pod install`. Tím se `helloworld.xcworkspace` vygeneruje pracovní prostor Xcode obsahující ukázkovou aplikaci i sadu Speech SDK jako závislost. Tento pracovní prostor bude použit v následujícím textu.

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

1. Otevřete `helloworld.xcworkspace` pracovní prostor v Xcode.
1. Zviditelnit ladicí výstup **(Zobrazit** > **konzolu** > **aktivace**ladění).
1. Ze seznamu v nabídce **Product** > **Destination** vyberte simulátor iOS nebo iOS zařízení připojené k vývojovému počítači jako cíl aplikace.
1. Vytvořte a spusťte ukázkový kód v simulátoru iOS výběrem **produktu** > **spustit** z nabídky nebo kliknutím na tlačítko **Přehrát.**
1. Po zadání textu a kliknutí na tlačítko v aplikaci byste měli slyšet přehráno syntetizovaný zvuk.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prohlédněte si naše ukázky na GitHubu](https://aka.ms/csspeech/samples)

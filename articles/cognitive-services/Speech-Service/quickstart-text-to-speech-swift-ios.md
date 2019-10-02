---
title: 'Rychlý Start: syntetizace řeči, SWIFT-Speech Service'
titleSuffix: Azure Cognitive Services
description: Naučte se, jak pomocí sady Speech SDK syntetizovat řeč v SWIFT v systému iOS.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 1cede1ced4d1973a5c1da59c5e53f888c54f22b9
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803954"
---
# <a name="quickstart-synthesize-speech-in-swift-on-ios-using-the-speech-sdk"></a>Rychlý Start: syntetizace řeči v SWIFT v systému iOS pomocí sady Speech SDK

K dispozici jsou také rychlé starty pro [rozpoznávání řeči](quickstart-swift-ios.md).

V tomto článku se naučíte, jak vytvořit aplikaci pro iOS v SWIFT pomocí sady Cognitive Services Speech SDK pro syntetizaci řeči z textu.

## <a name="prerequisites"></a>Předpoklady

Než začnete, tady je seznam požadavků:

* [Klíč předplatného](get-started.md) pro službu rozpoznávání řeči
* MacOS počítač s nainstalovaným [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) nebo novějším a [CocoaPods](https://cocoapods.org/) .

## <a name="get-the-speech-sdk-for-ios"></a>Získání sady Speech SDK pro iOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Všimněte si, že tento kurz nebude fungovat s verzí sady SDK starší než 1.7.0.

Sada Cognitive Services Speech SDK pro iOS je distribuována jako sada rozhraní.
Dá se použít v projektech Xcode jako [CocoaPod](https://cocoapods.org/)nebo stahovat z https://aka.ms/csspeech/macosbinary a propojit ručně. Tato příručka používá CocoaPod.

## <a name="create-an-xcode-project"></a>Vytvoření projektu Xcode

Spusťte Xcode a kliknutím na **File** > **New** > **Project** (Soubor > Nový > Projekt) spusťte nový projekt.
V dialogovém okně pro výběr šablony zvolte šablonu iOS Single View App (Aplikace pro iOS s jedním zobrazením).

V následujících dialogových oknech proveďte následující výběry:

1. Dialogové okno Project Options (Možnosti projektu)
    1. Zadejte název aplikace Rychlý start, například `helloworld`.
    1. Zadejte název příslušné organizace a identifikátor organizace, pokud již máte účet Apple Developer. Pro účely testování stačí vybrat jakýkoli název, třeba `testorg`. K podepsání aplikace potřebujete správný zřizovací profil. Podrobnosti najdete na [webu pro vývojáře Apple](https://developer.apple.com/) .
    1. Ujistěte se, že je pro projekt zvolený jazyk SWIFT.
    1. Pokud chcete použít scénáře a vytvořit aplikaci založenou na dokumentu, zakažte zaškrtávací políčka. Jednoduché uživatelské rozhraní pro ukázkovou aplikaci se vytvoří programově.
    1. Zrušte zaškrtnutí všech políček týkajících se testů a základních dat.
1. Vyberte adresář projektu.
    1. Vyberte adresář, do kterého se má projekt umístit. Tím se vytvoří adresář `helloworld` ve zvoleném adresáři, který obsahuje všechny soubory projektu Xcode.
    1. Zakažte vytvoření úložiště Git pro tento ukázkový projekt.
1. Zavřete projekt Xcode. Později po nastavení CocoaPods budete používat jinou instanci tohoto programu.

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

1. Do adresáře `helloworld` v rámci projektu HelloWorld umístěte nový hlavičkový soubor s názvem `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` a vložte do něj následující kód:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/swift-ios/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Přidejte relativní cestu `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` do hlavičky přemostění do nastavení projektu SWIFT pro cíl HelloWorld v poli *hlavičky pro přemostění v cíli C* ![Header vlastnosti @ no__t-3.
1. Nahraďte obsah automaticky vygenerovaného souboru `AppDelegate.swift` následujícím kódem:  
   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/swift-ios/helloworld/helloworld/AppDelegate.swift#code)]
1. Nahraďte obsah automaticky vygenerovaného souboru `ViewController.swift` následujícím kódem:  
   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/swift-ios/helloworld/helloworld/ViewController.swift#code)]
1. V `ViewController.swift` nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.
1. Řetězec `YourServiceRegion` nahraďte [oblastí](regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

## <a name="install-the-sdk-as-a-cocoapod"></a>Instalace sady SDK jako CocoaPod

1. Nainstalujte správce závislostí CocoaPod, jak je popsáno v [pokynech k instalaci](https://guides.cocoapods.org/using/getting-started.html).
1. Přejděte do adresáře ukázkové aplikace (`helloworld`). Umístěte textový soubor s názvem `Podfile` a následujícím obsahem v tomto adresáři:  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/swift-ios/helloworld/Podfile)]
1. V terminálu přejděte do adresáře `helloworld` a spusťte příkaz `pod install`. Tím se vygeneruje Xcode pracovní prostor `helloworld.xcworkspace` obsahující ukázkovou aplikaci a sadu Speech SDK jako závislost. Tento pracovní prostor bude použit v následujících.

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

1. Otevřete pracovní prostor `helloworld.xcworkspace` v Xcode.
1. Zobrazte výstup ladění: vyberte **View** > **Debug Area** > **Activate Console** (Zobrazení > Oblast ladění > Aktivovat konzolu).
1. Vyberte buď simulátor iOS nebo zařízení s iOS připojená k vývojovému počítači jako cíl pro aplikaci ze seznamu v nabídce**cíle** **produktu** > .
1. Sestavte a spusťte ukázkový kód v simulátoru iOS výběrem možnosti **Product** > **Run** (Produkt > Spustit) v nabídce nebo kliknutím na tlačítko **Play** (Přehrát).
1. Po zadání nějakého textu a kliknutí na tlačítko v aplikaci byste měli slyšet syntetizované zvuky.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte naše ukázky na GitHubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Další informace najdete v tématech

- [Přizpůsobení hlasových písem](how-to-customize-voice-font.md)
- [Záznam ukázek hlasu](record-custom-voice-samples.md)

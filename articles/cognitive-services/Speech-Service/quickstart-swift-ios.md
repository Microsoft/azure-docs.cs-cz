---
title: 'Rychlý Start: rozpoznávání řeči a SWIFT-Speech Service'
titleSuffix: Azure Cognitive Services
description: Naučte se rozpoznávat řeč v SWIFT v systému iOS pomocí sady Speech SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 0a62e670587f271eb9a1beba034886c5f95976a7
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327750"
---
# <a name="quickstart-recognize-speech-in-swift-on-ios-by-using-the-speech-sdk"></a>Rychlý Start: rozpoznávání řeči v SWIFT v systému iOS pomocí sady Speech SDK

K dispozici jsou také rychlé starty pro [syntézu řeči](quickstart-text-to-speech-swift-ios.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku se naučíte, jak vytvořit aplikaci pro iOS v SWIFT pomocí sady Azure Cognitive Services Speech SDK pro přepisovat řeči zaznamenané z mikrofonu na text.

## <a name="prerequisites"></a>Předpoklady

Než začnete, budete potřebovat:

* [Klíč předplatného](get-started.md) pro službu rozpoznávání řeči
* MacOS počítač s nainstalovaným [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) nebo novějším a [CocoaPods](https://cocoapods.org/) .

## <a name="get-the-speech-sdk-for-ios"></a>Získání sady Speech SDK pro iOS

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Tento kurz nebude fungovat s verzí sady SDK starší než 1.6.0.

Sada Cognitive Services Speech SDK pro iOS je distribuována jako sada rozhraní. Dá se použít v projektech Xcode jako [CocoaPod](https://cocoapods.org/) nebo stažených z https://aka.ms/csspeech/macosbinary a propojených ručně. Tento článek používá CocoaPod.

## <a name="create-an-xcode-project"></a>Vytvoření projektu Xcode

Spusťte Xcode a spusťte nový projekt tak, že vyberete **soubor** > **Nový** **projekt** > .
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
1. Aplikace musí také deklarovat použití mikrofonu v souboru `Info.plist`. V přehledu vyberte soubor a přidejte k rozpoznávání řeči klíč s **popisem používání mikrofonu – mikrofon** s hodnotou, jako *je třeba mikrofon*.

    ![Nastavení v souboru info. plist](media/sdk/qs-swift-ios-info-plist.png)

1. Zavřete projekt Xcode. Jinou instanci můžete použít později po nastavení CocoaPods.

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

1. Umístěte nový hlavičkový soubor s názvem `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` do adresáře HelloWorld v rámci projektu HelloWorld. Vložte do něj následující kód:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]

1. Přidejte relativní cestu `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` do hlavičky přemostění do nastavení projektu SWIFT pro cíl HelloWorld v poli **hlavičky přemostění cíle** v jazyce C.

   ![Vlastnosti záhlaví](media/sdk/qs-swift-ios-bridging-header.png)

1. Obsah automaticky generovaného souboru `AppDelegate.swift` nahraďte následujícím kódem:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/AppDelegate.swift#code)]
1. Obsah automaticky generovaného souboru `ViewController.swift` nahraďte následujícím kódem:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/ViewController.swift#code)]
1. V `ViewController.swift` nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.
1. Nahraďte řetězec `YourServiceRegion` [oblastí](regions.md) , která je přidružená k vašemu předplatnému. Například pro předplatné bezplatné zkušební verze použijte `westus`.

## <a name="install-the-sdk-as-a-cocoapod"></a>Instalace sady SDK jako CocoaPod

1. Nainstalujte správce závislostí CocoaPod, jak je popsáno v [pokynech k instalaci](https://guides.cocoapods.org/using/getting-started.html).
1. Přejít do adresáře ukázkové aplikace, což je HelloWorld. Umístěte textový soubor s názvem *souboru podfile* a následujícím obsahem v tomto adresáři:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/Podfile)]
1. Přejděte do adresáře HelloWorld v terminálu a spusťte příkaz `pod install`. Tento příkaz vygeneruje Xcode pracovní prostor `helloworld.xcworkspace`, který jako závislost obsahuje ukázkovou aplikaci i sadu Speech SDK. Tento pracovní prostor se používá v následujících krocích.

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

1. Otevřete pracovní prostor `helloworld.xcworkspace` v Xcode.
1. Zpřístupněte výstup ladění tak, že vyberete **zobrazení** > **oblast ladění** > **aktivovat konzolu**.
1. Vyberte buď simulátor iOS nebo zařízení s iOS připojená k vývojovému počítači jako cíl pro aplikaci ze seznamu v nabídce**cíle** **produktu** > .
1. Sestavte a spusťte vzorový kód v simulátoru iOS výběrem možnosti **produkt** > **Spustit** z nabídky. Můžete také vybrat tlačítko **Přehrát** .
1. Po výběru tlačítka **rozpoznat** v aplikaci a vyslovení několika slov byste měli vidět text, který jste probrali v dolní části obrazovky.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte naše ukázky na GitHubu](https://aka.ms/csspeech/samples)

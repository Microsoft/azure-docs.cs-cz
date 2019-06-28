---
title: 'Rychlý start: Swift – rozpoznání řeči, hlasové služby'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak rozpoznávání řeči ve Swiftu v Iosu pomocí sady SDK pro řeč
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/26/2019
ms.author: chlandsi
ms.openlocfilehash: feebca23e38596cff67e07f64e7acf1f7b6252e9
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67467429"
---
# <a name="quickstart-recognize-speech-in-swift-on-ios-using-the-speech-sdk"></a>Rychlý start: Rozpoznávání řeči ve Swiftu v Iosu pomocí sady SDK pro řeč

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku se dozvíte, jak vytvořit aplikaci pro iOS ve Swiftu přepisy řeči poznamenali z mikrofon na text pomocí Cognitive Services SDK řeči.

## <a name="prerequisites"></a>Požadavky

Než začnete, tady je seznam požadovaných součástí:

* A [klíč předplatného](get-started.md) pro službu rozpoznávání řeči.
* Počítače s macOS s [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) nebo novější a [CocoaPods](https://cocoapods.org/) nainstalované.

## <a name="get-the-speech-sdk-for-ios"></a>Získání sady Speech SDK pro iOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Aktuální verze sady Cognitive Services Speech SDK je `1.6.0`. Všimněte si, že v tomto kurzu nebudou fungovat beze změn pro všechny předchozí verze sady SDK.

Cognitive Services řeči SDK pro iOS se distribuuje jako sada framework.
Je možné v projekty Xcode jako [CocoaPod](https://cocoapods.org/), nebo stažených z https://aka.ms/csspeech/macosbinary a propojení ručně. Tato příručka používá CocoaPod.

## <a name="create-an-xcode-project"></a>Vytvoření projektu Xcode

Spusťte Xcode a kliknutím na **File** > **New** > **Project** (Soubor > Nový > Projekt) spusťte nový projekt.
V dialogovém okně pro výběr šablony zvolte šablonu iOS Single View App (Aplikace pro iOS s jedním zobrazením).

V následujících dialogových oknech proveďte následující výběry:

1. Dialogové okno Project Options (Možnosti projektu)
    1. Zadejte název aplikace Rychlý start, například `helloworld`.
    1. Zadejte název příslušné organizace a identifikátor organizace, pokud už máte účet pro vývojáře Apple. Pro účely testování stačí vybrat jakýkoli název, třeba `testorg`. K podepsání aplikace, budete potřebovat správné zřizovacího profilu. Odkazovat [webu Apple developer](https://developer.apple.com/) podrobnosti.
    1. Ujistěte se, že je vybrán Swift jako jazyk pro projekt.
    1. Zakážete zaškrtávací políčka použití scénářů a vytvořit aplikaci založenou na dokumentech. Jednoduché uživatelské rozhraní pro ukázkovou aplikaci bude vytvořen prostřednictvím kódu programu.
    1. Zrušte zaškrtnutí všech políček týkajících se testů a základních dat.
1. Vyberte adresář projektu.
    1. Vyberte adresář se umístí projektu. Tím se vytvoří `helloworld` ve zvolený adresář, který obsahuje všechny soubory projektu Xcode.
    1. Zakažte vytvoření úložiště Git pro tento ukázkový projekt.
1. Aplikace také musí deklarovat použití mikrofonu `Info.plist` souboru. Klikněte na soubor v přehledu a přidejte klíč "Popis použití mikrofonu – ochrana osobních údajů", s hodnotou jako "Mikrofonu je potřeba pro rozpoznávání řeči".
    ![Nastavení v souboru Info.plist](media/sdk/qs-swift-ios-info-plist.png)
1. Zavření projektu Xcode. Použijete ji později po nastavení CocoaPods jiné instance.

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

1. Nový soubor záhlaví s názvem `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` do `helloworld` adresáře uvnitř projektu helloworld a vložte následující kód do ní: [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Relativní cesta `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` na hlavičku přemostění na Swift nastavení pro cíl helloworld v projektu *hlavičky přemostění jazyka Objective-C* pole ![vlastnosti záhlaví](media/sdk/qs-swift-ios-bridging-header.png)
1. Nahraďte jeho obsah automaticky generované `AppDelegate.swift` souboru podle: [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/AppDelegate.swift#code)]
1. Nahraďte jeho obsah automaticky generované `ViewController.swift` souboru podle: [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/ViewController.swift#code)]
1. V `ViewController.swift`, nahraďte řetězec `YourSubscriptionKey` s klíči předplatného.
1. Řetězec `YourServiceRegion` nahraďte [oblastí](regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

## <a name="install-the-sdk-as-a-cocoapod"></a>Nainstalujte sadu SDK jako CocoaPod

1. Instalace správce závislostí CocoaPod, jak je popsáno v jeho [pokyny k instalaci](https://guides.cocoapods.org/using/getting-started.html).
1. Přejděte do adresáře ukázkovou aplikaci (`helloworld`). Umístit textový soubor s názvem `Podfile` a následující obsah v tomto adresáři: [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/Podfile)]
1. Přejděte `helloworld` adresář, v terminálu a spusťte tento příkaz `pod install`. Tím se vygeneruje `helloworld.xcworkspace` pracovní prostor Xcode, která obsahuje ukázkovou aplikaci a sadou SDK pro řeč jako závislost. Tento pracovní prostor se použije v následujících tématech.

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

1. Otevřít `helloworld.xcworkspace` pracovní prostor v Xcode.
1. Zobrazte výstup ladění: vyberte **View** > **Debug Area** > **Activate Console** (Zobrazení > Oblast ladění > Aktivovat konzolu).
1. Zvolte simulátoru iOS nebo zařízení s Iosem, která jsou připojená do svého vývojového počítače jako cíl pro aplikaci v seznamu **produktu** > **cílové** nabídky.
1. Sestavte a spusťte ukázkový kód v simulátoru iOS výběrem možnosti **Product** > **Run** (Produkt > Spustit) v nabídce nebo kliknutím na tlačítko **Play** (Přehrát).
1. Poté, co klikněte na tlačítko "Rozpoznávat" v aplikaci a Řekněme, že pár slov, byste měli vidět text, který je slyšet ve spodní části obrazovky.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte naše ukázky na Githubu](https://aka.ms/csspeech/samples)


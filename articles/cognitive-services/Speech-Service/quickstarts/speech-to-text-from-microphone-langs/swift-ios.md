---
title: 'Úvodní příručka: Rozpoznávání řeči z mikrofonu, Swift - Služba řeči'
titleSuffix: Azure Cognitive Services
description: Naučte se rozpoznávat řeč v Swiftu v iOS pomocí sady Speech SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: chlandsi
ms.openlocfilehash: 31f5339c70d52019400ca5f1fe873de4790a3bd6
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75380520"
---
# <a name="quickstart-recognize-speech-in-swift-on-ios-by-using-the-speech-sdk"></a>Úvodní příručka: Rozpoznání řeči v swiftu v systému iOS pomocí sady Speech SDK

Rychlé starty jsou také k dispozici pro [syntézu řeči](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/swift-ios.md).

V tomto článku se dozvíte, jak vytvořit aplikaci pro iOS v Swiftpomocí Azure Cognitive Services řeči SDK přepsat řeči zaznamenané z mikrofonu na text.

## <a name="prerequisites"></a>Požadavky

Než začnete, budete potřebovat:

* [Klíč předplatného](~/articles/cognitive-services/Speech-Service/get-started.md) pro službu Řeč.
* Počítač s macOS s [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) nebo novějším a nainstalovanými [kakaopody.](https://cocoapods.org/)

## <a name="get-the-speech-sdk-for-ios"></a>Získání sady Speech SDK pro iOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Tento kurz nebude fungovat s verzí sady SDK starší než 1.6.0.

Sada SDK řeči služby Cognitive Services pro iOS je distribuována jako sada architektury. Může být použit v xcode projektech jako [CocoaPod](https://cocoapods.org/) nebo stáhnout z https://aka.ms/csspeech/iosbinary a propojit ručně. Tento článek používá Kakaový pod.

## <a name="create-an-xcode-project"></a>Vytvoření projektu Xcode

Spusťte Xcode a začněte nový projekt výběrem **možnosti Soubor** > **nového** > **projektu**.
V dialogovém okně výběru šablony vyberte šablonu **aplikace pro jedno zobrazení iOS.**

V následujících dialogových oknech proveďte následující výběry.

1. V dialogovém okně **Možnosti projektu:**
    1. Zadejte název aplikace pro rychlý start, například *helloworld*.
    1. Pokud už máte vývojářský účet Apple, zadejte příslušný název organizace a identifikátor organizace. Pro účely testování použijte název jako *testorg*. Chcete-li podepsat aplikaci, potřebujete správný zřizovací profil. Další informace naleznete na [webu pro vývojáře společnosti Apple](https://developer.apple.com/).
    1. Ujistěte se, že **swift** je vybrán jako jazyk pro projekt.
    1. Zaškrtávací políčka zaškrtávat a vytvořit aplikaci založenou na dokumentu. Jednoduché ui pro ukázkovou aplikaci se vytvoří programově.
    1. Zrušte zaškrtnutí všech políček pro testy a základní data.
1. Vyberte adresář projektu:
    1. Zvolte adresář, do který chcete projekt vložit. Tento krok vytvoří helloworld adresář ve zvoleném adresáři, který obsahuje všechny soubory pro projekt Xcode.
    1. Zakažte vytvoření úložiště Git pro tento ukázkový projekt.
1. Aplikace také musí deklarovat použití `Info.plist` mikrofonu v souboru. Vyberte soubor v přehledu a přidejte klíč **Popis využití mikrofonu – ochrana osobních údajů** s hodnotou, jako je *mikrofon, je potřebný pro rozpoznávání řeči*.

    ![Nastavení v souboru Info.plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-info-plist.png)

1. Zavřete projekt Xcode. Po nastavení kakaových podů později použijete jinou instanci.

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

1. Umístěte nový soubor záhlaví `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` s názvem do adresáře helloworld uvnitř projektu Helloworld. Vložte do něj následující kód:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]

1. Přidejte relativní `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` cestu k přemostění záhlaví do nastavení projektu Swift pro cíl helloworld v poli **Cíl-C Přemostění záhlaví.**

   ![Vlastnosti záhlaví](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-bridging-header.png)

1. Nahraďte obsah automaticky `AppDelegate.swift` generovaného souboru následujícím kódem:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/AppDelegate.swift#code)]
1. Nahraďte obsah automaticky `ViewController.swift` generovaného souboru následujícím kódem:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/ViewController.swift#code)]
1. V `ViewController.swift`aplikaci `YourSubscriptionKey` nahraďte řetězec klíčem předplatného.
1. Nahraďte `YourServiceRegion` řetězec [oblastí](~/articles/cognitive-services/Speech-Service/regions.md) přidruženou k vašemu předplatnému. Můžete například `westus` použít bezplatné zkušební předplatné.

## <a name="install-the-sdk-as-a-cocoapod"></a>Instalace sady SDK jako kakaového podu

1. Nainstalujte Správce závislostí CocoaPod, jak je popsáno v [jeho pokynech k instalaci](https://guides.cocoapods.org/using/getting-started.html).
1. Přejděte do adresáře ukázkové aplikace, což je helloworld. Umístěte textový soubor s názvem *Podfile* a následujícím obsahem do tohoto adresáře:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/Podfile)]
1. Přejděte do adresáře helloworld v terminálu a spusťte příkaz `pod install`. Tento příkaz generuje `helloworld.xcworkspace` pracovní prostor Xcode, který obsahuje ukázkovou aplikaci i sadu Speech SDK jako závislost. Tento pracovní prostor se používá v následujících krocích.

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

1. Otevřete pracovní `helloworld.xcworkspace` prostor v Xcode.
1. Zviditelnění výstupu ladění výběrem **možnosti Zobrazit** > **zónu** > aktivace ladění **.**
1. Ze seznamu v nabídce **Product** > **Destination** vyberte simulátor iOS nebo iOS zařízení připojené k vývojovému počítači jako cíl aplikace.
1. Vytvořte a spusťte ukázkový kód v simulátoru iOS výběrem**spuštění** **produktu** > z nabídky. Můžete také vybrat tlačítko **Přehrát.**
1. Po výběru tlačítka **Rozpoznat** v aplikaci a vyslovení několika slov byste měli vidět text, který jste vyslovili v dolní části obrazovky.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prohlédněte si naše ukázky na GitHubu](https://aka.ms/csspeech/samples)

---
title: 'Rychlý Start: syntetizace řeči, objektivní-C-Speech Service'
titleSuffix: Azure Cognitive Services
description: Naučte se, jak syntetizovat řeč v cíli – C v iOS pomocí sady Speech SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: 1f5a569f9c3bfa91b78c836e37a22642238674be
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74975937"
---
# <a name="quickstart-synthesize-speech-in-objective-c-on-ios-using-the-speech-sdk"></a>Rychlý Start: syntetizace řeči v cíli – C v iOS pomocí sady Speech SDK

V tomto článku se naučíte, jak vytvořit aplikaci pro iOS v cíli – C pomocí sady Cognitive Services Speech SDK pro syntetizaci řeči z textu.

## <a name="prerequisites"></a>Požadavky

Než začnete, tady je seznam požadavků:

* [Klíč předplatného](~/articles/cognitive-services/Speech-Service/get-started.md) pro službu pro rozpoznávání řeči
* MacOS počítač s [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) nebo novějším
* Cílová sada pro iOS verze 9,3 nebo novější

## <a name="get-the-speech-sdk-for-ios"></a>Získání sady Speech SDK pro iOS

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Všimněte si, že tento kurz nebude fungovat s verzí sady SDK starší než 1.7.0.

Sada Cognitive Services Speech SDK pro iOS je aktuálně distribuována jako rozhraní pro kakao.
Dá se použít v projektech Xcode jako [CocoaPod](https://cocoapods.org/), nebo stahovat z https://aka.ms/csspeech/iosbinary a propojit ručně. Tato příručka používá CocoaPod.

## <a name="create-an-xcode-project"></a>Vytvoření projektu Xcode

Spusťte Xcode a spusťte nový projekt kliknutím na **soubor** > **Nový** > **projekt**.
V dialogovém okně pro výběr šablony zvolte šablonu iOS Single View App (Aplikace pro iOS s jedním zobrazením).

V následujících dialogových oknech proveďte následující výběry:

1. Dialogové okno Project Options (Možnosti projektu)
    1. Zadejte název aplikace Rychlý start, například `helloworld`.
    1. Pokud už máte účet Apple Developer, zadejte odpovídající název a identifikátor organizace. Pro účely testování stačí vybrat jakýkoli název, třeba `testorg`. K podepsání aplikace potřebujete správný zřizovací profil. Podrobnosti najdete na [webu pro vývojáře Apple](https://developer.apple.com/) .
    1. Ujistěte se, že jako jazyk projektu je zvolený jazyk Objective-C.
    1. Zrušte zaškrtnutí všech políček týkajících se testů a základních dat.
    ![Nastavení projektu](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-project-settings.png)
1. Vyberte adresář projektu.
    1. Zvolte svůj domovský adresář, do kterého se projekt umístí. Tím se vytvoří `helloworld` adresář v domovském adresáři, který obsahuje všechny soubory projektu Xcode.
    1. Zakažte vytvoření úložiště Git pro tento ukázkový projekt.

## <a name="install-the-sdk-as-a-cocoapod"></a>Instalace sady SDK jako CocoaPod

1. Nainstalujte správce závislostí CocoaPod, jak je popsáno v [pokynech k instalaci](https://guides.cocoapods.org/using/getting-started.html).
1. Přejděte do adresáře ukázkové aplikace (`helloworld`). Umístěte textový soubor s názvem `Podfile` a následujícím obsahem v tomto adresáři:  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/Podfile)]
1. Přejděte do `helloworld` adresáře v terminálu a spusťte příkaz `pod install`. Tím se vygeneruje `helloworld.xcworkspace` pracovní prostor Xcode obsahující ukázkovou aplikaci a sadu Speech SDK jako závislost. Tento pracovní prostor bude použit v následujících.

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

1. Otevřete `helloworld.xcworkspace` pracovní prostor v Xcode.
1. Nahraďte obsah automaticky vygenerovaného souboru `AppDelegate.m` následujícím kódem:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/helloworld/AppDelegate.m#code)]
1. Nahraďte obsah automaticky vygenerovaného souboru `ViewController.m` následujícím kódem:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/helloworld/ViewController.m#code)]
1. Řetězec `YourSubscriptionKey` nahraďte klíčem předplatného.
1. Řetězec `YourServiceRegion` nahraďte [oblastí](~/articles/cognitive-services/Speech-Service/regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

1. Nastavte výstup ladění jako viditelný (**Zobrazit** >  > **konzolu pro aktivaci****oblasti ladění**).
1. Jako cíl pro aplikaci ze seznamu v nabídce**cílový** **produkt** > vyberte buď simulátor iOS nebo zařízení s iOS připojené k vývojovému počítači.
1. Sestavte a spusťte ukázkový kód v simulátoru iOS, a to tak, že v nabídce vyberete**Spustit** **produkt** > nebo kliknete na tlačítko **Přehrát** .

   ![Simulovaná aplikace pro iOS](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-simulated-app-tts.png)

1. Po zadání nějakého textu a kliknutí na tlačítko v aplikaci byste měli slyšet syntetizované zvuky.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zkoumání objektivů a ukázek v jazyce C na GitHubu](https://aka.ms/csspeech/samples)


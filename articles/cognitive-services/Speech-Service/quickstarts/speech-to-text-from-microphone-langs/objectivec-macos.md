---
title: 'Rychlý Start: rozpoznávání řeči na mikrofonu, objektivní-C-Speech Service'
titleSuffix: Azure Cognitive Services
description: Naučte se rozpoznávat řeč v cíli-C v macOS pomocí sady Speech SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: chlandsi
ms.openlocfilehash: c2f0fbe66b26c6eca6e0c0b2530efacba9bae958
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75380588"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-by-using-the-speech-sdk"></a>Rychlý Start: rozpoznávání řeči v cíli-C v macOS pomocí sady Speech SDK

K dispozici jsou také rychlé starty pro [syntézu řeči](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-macos.md).

V tomto článku se dozvíte, jak vytvořit aplikaci macOS v cíli-C pomocí sady Azure Cognitive Services Speech SDK pro přepisovat řeči zaznamenané z mikrofonu na text.

## <a name="prerequisites"></a>Požadavky

Než začnete, budete potřebovat:

* [Klíč předplatného](~/articles/cognitive-services/Speech-Service/get-started.md) pro službu rozpoznávání řeči
* MacOS počítač s [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) nebo novějším a MacOS 10,13 nebo novějším.

## <a name="get-the-speech-sdk-for-macos"></a>Získat sadu Speech SDK pro macOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Sada Cognitive Services Speech SDK for Mac je distribuována jako sada rozhraní. Dá se použít v projektech Xcode jako [CocoaPod](https://cocoapods.org/) nebo stahovat z https://aka.ms/csspeech/macosbinary a propojit ručně. Tento článek používá CocoaPod.

## <a name="create-an-xcode-project"></a>Vytvoření projektu Xcode

Spusťte Xcode a spusťte nový projekt tak, že vyberete **soubor** > **Nový** > **projekt**. V dialogovém okně Výběr šablony vyberte šablonu aplikace pro **kakao** .

V následujících dialogových oknech proveďte následující výběry.

1. V dialogovém okně **Možnosti projektu** :
    1. Zadejte název aplikace pro rychlý Start, například *HelloWorld*.
    1. Zadejte název příslušné organizace a identifikátor organizace, pokud už máte účet Apple Developer. Pro účely testování použijte název jako *testorg*. K podepsání aplikace potřebujete správný zřizovací profil. Další informace najdete na [webu Apple Developer](https://developer.apple.com/).
    1. Ujistěte se, že je jako jazyk pro projekt vybrán **cíl-C** .
    1. Zrušte zaškrtnutí políček pro použití scénářů a vytvoření aplikace založené na dokumentu. Jednoduché uživatelské rozhraní pro ukázkovou aplikaci se vytvoří programově.
    1. Zrušte zaškrtnutí všech políček pro testy a základní data.

    ![Nastavení projektu](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-project-settings.png)

1. Vyberte adresář projektu:
    1. Vyberte adresář, do kterého se má projekt umístit. Tento krok vytvoří adresář HelloWorld v domovském adresáři, který obsahuje všechny soubory projektu Xcode.
    1. Zakažte vytvoření úložiště Git pro tento ukázkový projekt.
1. Nastavte oprávnění pro přístup k síti a mikrofonu. Pokud se chcete dostat do konfigurace aplikace, vyberte v prvním řádku přehledu vlevo název aplikace. Pak vyberte kartu **Možnosti** .
    1. Povolte nastavení **izolovaného prostoru aplikace** pro aplikaci.
    1. Zaškrtněte políčka pro **odchozí připojení** a přístup **přes mikrofon** .

    ![Nastavení izolovaného prostoru](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-sandbox.png)

1. Aplikace musí také deklarovat použití mikrofonu v `Info.plist` souboru. V přehledu vyberte soubor a přidejte k rozpoznávání řeči klíč s **popisem používání mikrofonu – mikrofon** s hodnotou, jako *je třeba mikrofon*.

    ![Nastavení v souboru info. plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-info-plist.png)

1. Zavřete projekt Xcode. Jinou instanci můžete použít později po nastavení CocoaPods.

## <a name="install-the-sdk-as-a-cocoapod"></a>Instalace sady SDK jako CocoaPod

1. Nainstalujte správce závislostí CocoaPod, jak je popsáno v [pokynech k instalaci](https://guides.cocoapods.org/using/getting-started.html).
1. Přejít do adresáře ukázkové aplikace, což je HelloWorld. Umístěte textový soubor s názvem *souboru podfile* a následujícím obsahem v tomto adresáři:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/Podfile)]
1. Přejděte do adresáře HelloWorld v terminálu a spusťte příkaz `pod install`. Tento příkaz vygeneruje `helloworld.xcworkspace` pracovní prostor Xcode, který obsahuje ukázkovou aplikaci a sadu Speech SDK jako závislost. Tento pracovní prostor se používá v následujících krocích.

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

1. Otevřete pracovní prostor `helloworld.xcworkspace` v Xcode.
1. Obsah automaticky generovaného `AppDelegate.m` souboru nahraďte následujícím kódem:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/helloworld/AppDelegate.m#code)]
1. Řetězec `YourSubscriptionKey` nahraďte klíčem předplatného.
1. Nahraďte řetězec `YourServiceRegion` [oblastí](~/articles/cognitive-services/Speech-Service/regions.md) , která je přidružená k vašemu předplatnému. Použijte `westus` například pro předplatné bezplatné zkušební verze.

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

1. Zpřístupněte výstup ladění tak, že vyberete **Zobrazit** > **oblast** > ladění**aktivovat konzolu**.
1. Sestavte a spusťte ukázkový kód tak, že v nabídce vyberete**Spustit** **produkt** > . Můžete také vybrat možnost **Přehrát**.
1. Po výběru tlačítka a vyslovení několika slov byste se měli podívat na text, který jste v dolní části obrazovky promluví. Při prvním spuštění aplikace by se vám měla zobrazit výzva k poskytnutí přístupu aplikace k mikrofonu vašeho počítače.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zkoumání objektivů a ukázek v jazyce C na GitHubu](https://aka.ms/csspeech/samples)

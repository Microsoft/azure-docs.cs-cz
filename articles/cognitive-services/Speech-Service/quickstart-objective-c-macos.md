---
title: 'Rychlý Start: rozpoznávání řeči, cíl-C-Speech Service'
titleSuffix: Azure Cognitive Services
description: Naučte se rozpoznávat řeč v cíli-C v macOS pomocí sady Speech SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 6955fc5dd98b65d2eb94914ea39685f1f69a46ac
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327798"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-by-using-the-speech-sdk"></a>Rychlý Start: rozpoznávání řeči v cíli-C v macOS pomocí sady Speech SDK

K dispozici jsou také rychlé starty pro [syntézu řeči](quickstart-text-to-speech-objectivec-macos.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku se dozvíte, jak vytvořit aplikaci macOS v cíli-C pomocí sady Azure Cognitive Services Speech SDK pro přepisovat řeči zaznamenané z mikrofonu na text.

## <a name="prerequisites"></a>Předpoklady

Než začnete, budete potřebovat:

* [Klíč předplatného](get-started.md) pro službu rozpoznávání řeči
* MacOS počítač s [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) nebo novějším a MacOS 10,13 nebo novějším.

## <a name="get-the-speech-sdk-for-macos"></a>Získat sadu Speech SDK pro macOS

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Sada Cognitive Services Speech SDK for Mac je distribuována jako sada rozhraní. Dá se použít v projektech Xcode jako [CocoaPod](https://cocoapods.org/) nebo stažených z https://aka.ms/csspeech/macosbinary a propojených ručně. Tento článek používá CocoaPod.

## <a name="create-an-xcode-project"></a>Vytvoření projektu Xcode

Spusťte Xcode a spusťte nový projekt tak, že vyberete **soubor** > **Nový** **projekt** > . V dialogovém okně Výběr šablony vyberte šablonu aplikace pro **kakao** .

V následujících dialogových oknech proveďte následující výběry.

1. V dialogovém okně **Možnosti projektu** :
    1. Zadejte název aplikace pro rychlý Start, například *HelloWorld*.
    1. Zadejte název příslušné organizace a identifikátor organizace, pokud už máte účet Apple Developer. Pro účely testování použijte název jako *testorg*. K podepsání aplikace potřebujete správný zřizovací profil. Další informace najdete na [webu Apple Developer](https://developer.apple.com/).
    1. Ujistěte se, že je jako jazyk pro projekt vybrán **cíl-C** .
    1. Zrušte zaškrtnutí políček pro použití scénářů a vytvoření aplikace založené na dokumentu. Jednoduché uživatelské rozhraní pro ukázkovou aplikaci se vytvoří programově.
    1. Zrušte zaškrtnutí všech políček pro testy a základní data.

    ![Nastavení projektu](media/sdk/qs-objectivec-macos-project-settings.png)

1. Vyberte adresář projektu:
    1. Vyberte adresář, do kterého se má projekt umístit. Tento krok vytvoří adresář HelloWorld v domovském adresáři, který obsahuje všechny soubory projektu Xcode.
    1. Zakažte vytvoření úložiště Git pro tento ukázkový projekt.
1. Nastavte oprávnění pro přístup k síti a mikrofonu. Pokud se chcete dostat do konfigurace aplikace, vyberte v prvním řádku přehledu vlevo název aplikace. Pak vyberte kartu **Možnosti** .
    1. Povolte nastavení **izolovaného prostoru aplikace** pro aplikaci.
    1. Zaškrtněte políčka pro **odchozí připojení** a přístup **přes mikrofon** .

    ![Nastavení izolovaného prostoru](media/sdk/qs-objectivec-macos-sandbox.png)

1. Aplikace musí také deklarovat použití mikrofonu v souboru `Info.plist`. V přehledu vyberte soubor a přidejte k rozpoznávání řeči klíč s **popisem používání mikrofonu – mikrofon** s hodnotou, jako *je třeba mikrofon*.

    ![Nastavení v souboru info. plist](media/sdk/qs-objectivec-macos-info-plist.png)

1. Zavřete projekt Xcode. Jinou instanci můžete použít později po nastavení CocoaPods.

## <a name="install-the-sdk-as-a-cocoapod"></a>Instalace sady SDK jako CocoaPod

1. Nainstalujte správce závislostí CocoaPod, jak je popsáno v [pokynech k instalaci](https://guides.cocoapods.org/using/getting-started.html).
1. Přejít do adresáře ukázkové aplikace, což je HelloWorld. Umístěte textový soubor s názvem *souboru podfile* a následujícím obsahem v tomto adresáři:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-macos/helloworld/Podfile)]
1. Přejděte do adresáře HelloWorld v terminálu a spusťte příkaz `pod install`. Tento příkaz vygeneruje Xcode pracovní prostor `helloworld.xcworkspace`, který jako závislost obsahuje ukázkovou aplikaci i sadu Speech SDK. Tento pracovní prostor se používá v následujících krocích.

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

1. Otevřete pracovní prostor `helloworld.xcworkspace` v Xcode.
1. Obsah automaticky generovaného souboru `AppDelegate.m` nahraďte následujícím kódem:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-macos/helloworld/helloworld/AppDelegate.m#code)]
1. Řetězec `YourSubscriptionKey` nahraďte klíčem předplatného.
1. Nahraďte řetězec `YourServiceRegion` [oblastí](regions.md) , která je přidružená k vašemu předplatnému. Například pro předplatné bezplatné zkušební verze použijte `westus`.

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

1. Zpřístupněte výstup ladění tak, že vyberete **zobrazení** > **oblast ladění** > **aktivovat konzolu**.
1. Sestavte a spusťte ukázkový kód výběrem **produktu** > **Spustit** z nabídky. Můžete také vybrat možnost **Přehrát**.
1. Po výběru tlačítka a vyslovení několika slov byste se měli podívat na text, který jste v dolní části obrazovky promluví. Při prvním spuštění aplikace by se vám měla zobrazit výzva k poskytnutí přístupu aplikace k mikrofonu vašeho počítače.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zkoumání objektivů a ukázek v jazyce C na GitHubu](https://aka.ms/csspeech/samples)

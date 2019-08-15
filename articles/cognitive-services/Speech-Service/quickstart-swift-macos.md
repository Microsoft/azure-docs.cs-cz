---
title: 'Rychlý start: Rozpoznávání řeči, SWIFT-Speech Service'
titleSuffix: Azure Cognitive Services
description: Naučte se rozpoznávat řeč v SWIFT v macOS pomocí sady Speech SDK
services: cognitive-services
author: cbasoglu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/28/2019
ms.author: cbasoglu
ms.openlocfilehash: c1407e8a133013054ef33d4fe55fb8ecb46de654
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035722"
---
# <a name="quickstart-recognize-speech-in-swift-on-macos-using-the-speech-sdk"></a>Rychlý start: Rozpoznávání řeči v SWIFT v macOS pomocí sady Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku se naučíte, jak vytvořit aplikaci v macOS v SWIFT pomocí sady Cognitive Services Speech SDK pro přepisovat řeči zaznamenané z mikrofonu na text.

## <a name="prerequisites"></a>Požadavky

Než začnete, tady je seznam požadovaných součástí:

* [Klíč](get-started.md) předplatného pro službu rozpoznávání řeči
* MacOS počítač s nainstalovaným [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) nebo novějším a [CocoaPods](https://cocoapods.org/) .

## <a name="get-the-speech-sdk-for-macos"></a>Získat sadu Speech SDK pro macOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Aktuální verze sady Cognitive Services Speech SDK je `1.6.0`. Všimněte si, že tento kurz nebude fungovat bez změn v žádné předchozí verzi sady SDK.

Sada Cognitive Services Speech SDK pro macOS je distribuována jako sada rozhraní.
Dá se použít v projektech Xcode jako [CocoaPod](https://cocoapods.org/), nebo stahovat z https://aka.ms/csspeech/macosbinary a propojit ručně. Tato příručka používá CocoaPod.

## <a name="create-an-xcode-project"></a>Vytvoření projektu Xcode

Spusťte Xcode a kliknutím na **File** > **New** > **Project** (Soubor > Nový > Projekt) spusťte nový projekt.
V dialogovém okně Výběr šablony vyberte šablonu aplikace pro kakao.

V následujících dialogových oknech proveďte následující výběry:

1. Dialogové okno Project Options (Možnosti projektu)
    1. Zadejte název aplikace Rychlý start, například `helloworld`.
    1. Zadejte název příslušné organizace a identifikátor organizace, pokud již máte účet Apple Developer. Pro účely testování stačí vybrat jakýkoli název, třeba `testorg`. K podepsání aplikace potřebujete správný zřizovací profil. Podrobnosti najdete na [webu pro vývojáře Apple](https://developer.apple.com/) .
    1. Ujistěte se, že je pro projekt zvolený jazyk SWIFT.
    1. Pokud chcete použít scénáře a vytvořit aplikaci založenou na dokumentu, zakažte zaškrtávací políčka. Jednoduché uživatelské rozhraní pro ukázkovou aplikaci se vytvoří programově.
    1. Zrušte zaškrtnutí všech políček týkajících se testů a základních dat.
1. Vyberte adresář projektu.
    1. Vyberte adresář, do kterého se má projekt umístit. Tím se vytvoří `helloworld` adresář ve zvoleném adresáři, který obsahuje všechny soubory projektu Xcode.
    1. Zakažte vytvoření úložiště Git pro tento ukázkový projekt.
1. Nastavte oprávnění pro přístup k síti a mikrofonu. Kliknutím na název aplikace v prvním řádku v přehledu vlevo získáte konfiguraci aplikace a pak zvolíte kartu Možnosti.
    1. Povolte pro aplikaci nastavení "Sandbox aplikace".
    1. Zaškrtněte políčka pro přístup odchozí připojení a mikrofon.
    ![Nastavení izolovaného prostoru](media/sdk/qs-swift-macos-sandbox.png)
1. Aplikace musí také deklarovat použití mikrofonu v `Info.plist` souboru. V přehledu klikněte na soubor a přidejte klíč "" soukromí – mikrofon použití popisu "s hodnotou jako" mikrofon je potřeba pro rozpoznávání řeči ".
    ![Nastavení v souboru info. plist](media/sdk/qs-swift-macos-info-plist.png)
1. Zavřete projekt Xcode. Později po nastavení CocoaPods budete používat jinou instanci tohoto programu.

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

1. Umístěte nový hlavičkový soubor s názvem `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` `helloworld` do adresáře uvnitř projektu HelloWorld a vložte do něj následující kód:[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-macos/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Přidejte relativní cestu `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` k přemostění hlavičky do nastavení projektu SWIFT pro cíl HelloWorld ve vlastnostech záhlaví pole ![s hlavičkou přemostění v *cíli C* .](media/sdk/qs-swift-macos-bridging-header.png)
1. Obsah automaticky generovaného `AppDelegate.swift` souboru nahraďte:[!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-macos/helloworld/helloworld/AppDelegate.swift#code)]
1. V `AppDelegate.swift`nahraďte řetězec `YourSubscriptionKey` pomocí vašeho klíče předplatného.
1. Nahraďte řetězec `YourServiceRegion` oblastí, která je přidružená k vašemu předplatnému `westus` (například pro předplatné bezplatné zkušební verze).

## <a name="install-the-sdk-as-a-cocoapod"></a>Instalace sady SDK jako CocoaPod

1. Nainstalujte správce závislostí CocoaPod, jak je popsáno v pokynech k [instalaci](https://guides.cocoapods.org/using/getting-started.html).
1. Přejděte do adresáře ukázkové aplikace (`helloworld`). Umístěte textový soubor s názvem `Podfile` a následujícím obsahem v tomto adresáři:[!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-macos/helloworld/Podfile)]
1. Přejděte do `helloworld` adresáře v terminálu a spusťte příkaz `pod install`. Tím se vygeneruje `helloworld.xcworkspace` pracovní prostor Xcode obsahující ukázkovou aplikaci a sadu Speech SDK jako závislost. Tento pracovní prostor bude použit v následujících.

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

1. `helloworld.xcworkspace` Otevřete pracovní prostor v Xcode.
1. Zobrazte výstup ladění: vyberte **View** > **Debug Area** > **Activate Console** (Zobrazení > Oblast ladění > Aktivovat konzolu).
1. Sestavte a spusťte ukázkový kód tak, že v nabídce vyberete**Spustit** **produkt** > nebo kliknete na tlačítko **Přehrát** .
1. Po kliknutí na tlačítko rozpoznat v aplikaci a vyslovení několika slov byste se měli podívat na text, který jste promluví v dolní části okna aplikace.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte naše ukázky na GitHubu](https://aka.ms/csspeech/samples)


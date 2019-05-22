---
title: 'Rychlý start: Rozpoznávání řeči, jazyka Objective-C - hlasové služby'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak rozpoznávat řeč v Objective-C v systému macOS pomocí sady SDK pro řeč
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/03/2019
ms.author: chlandsi
ms.openlocfilehash: 67f2531b24796de1e00505fdc757f3c2244c5054
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002338"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-using-the-speech-sdk"></a>Rychlý start: Rozpoznávání řeči v Objective-C v systému macOS pomocí sady SDK pro řeč

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku se dozvíte, jak k vytvoření aplikace pro macOS v Objective-C pomocí Cognitive Services SDK řeči přepisy řeči poznamenali z mikrofon na text.

## <a name="prerequisites"></a>Požadavky

Než začnete, tady je seznam požadovaných součástí:

* A [klíč předplatného](get-started.md) pro Speech Service
* Počítače s macOS s [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) nebo novější a macOS 10.13 nebo novější

## <a name="get-the-speech-sdk-for-macos"></a>Získat sadou SDK pro řeč pro macOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Aktuální verze sady Cognitive Services Speech SDK je `1.5.1`.

Cognitive Services sadou SDK pro řeč pro Mac se distribuuje jako sada framework.
Je možné v projekty Xcode jako [CocoaPod](https://cocoapods.org/), nebo stažených z https://aka.ms/csspeech/macosbinary a propojení ručně. Tato příručka používá CocoaPod.

## <a name="create-an-xcode-project"></a>Vytvoření projektu Xcode

Spusťte Xcode a kliknutím na **File** > **New** > **Project** (Soubor > Nový > Projekt) spusťte nový projekt.
V šabloně dialogu pro výběr výběr šablony "Aplikace Cocoa".

V následujících dialogových oknech proveďte následující výběry:

1. Dialogové okno Project Options (Možnosti projektu)
    1. Zadejte název aplikace Rychlý start, například `helloworld`.
    1. Zadejte název příslušné organizace a identifikátor organizace, pokud už máte účet pro vývojáře Apple. Pro účely testování stačí vybrat jakýkoli název, třeba `testorg`. K podepsání aplikace, budete potřebovat správné zřizovacího profilu. Odkazovat [webu Apple developer](https://developer.apple.com/) podrobnosti.
    1. Ujistěte se, že jako jazyk projektu je zvolený jazyk Objective-C.
    1. Zakážete zaškrtávací políčka použití scénářů a vytvořit aplikaci založenou na dokumentech. Jednoduché uživatelské rozhraní pro ukázkovou aplikaci bude vytvořen prostřednictvím kódu programu.
    1. Zrušte zaškrtnutí všech políček týkajících se testů a základních dat.
    ![Nastavení projektu](media/sdk/qs-objectivec-macos-project-settings.png)
1. Vyberte adresář projektu.
    1. Vyberte adresář se umístí projektu. Tím se vytvoří `helloworld` adresáře ve svém domovském adresáři, který obsahuje všechny soubory projektu Xcode.
    1. Zakažte vytvoření úložiště Git pro tento ukázkový projekt.
1. Nastavte oprávnění pro přístup k síti a mikrofon. Klikněte na název aplikace na prvním řádku v přehledu na levé straně na konfiguraci aplikace a pak vyberte kartu "Funkce".
    1. Povolte nastavení "Izolovaný prostor aplikace" pro aplikaci.
    1. Zaškrtněte políčka pro přístup k "Odchozí připojení" a "Mikrofon".
    ![Nastavení karantény](media/sdk/qs-objectivec-macos-sandbox.png)
1. Aplikace také musí deklarovat použití mikrofonu `Info.plist` souboru. Klikněte na soubor v přehledu a přidejte klíč "Popis použití mikrofonu – ochrana osobních údajů", s hodnotou jako "Mikrofonu je potřeba pro rozpoznávání řeči".
    ![Nastavení v souboru Info.plist](media/sdk/qs-objectivec-macos-info-plist.png)
1. Zavření projektu Xcode. Použijete ji později po nastavení CocoaPods jiné instance.

## <a name="install-the-sdk-as-a-cocoapod"></a>Nainstalujte sadu SDK jako CocoaPod

1. Instalace správce závislostí CocoaPod, jak je popsáno v jeho [pokyny k instalaci](https://guides.cocoapods.org/using/getting-started.html).
1. Přejděte do adresáře ukázkovou aplikaci (`helloworld`). Umístit textový soubor s názvem `Podfile` a následující obsah v tomto adresáři:
    ```
    target 'helloworld' do
        platform :osx, '10.13'
        pod 'MicrosoftCognitiveServicesSpeech-macOS', '~> 1.5.1'
    end
    ```
1. Přejděte `helloworld` adresář, v terminálu a spusťte tento příkaz `pod install`. Tím se vygeneruje `helloworld.xcworkspace` pracovní prostor Xcode, která obsahuje ukázkovou aplikaci a sadou SDK pro řeč jako závislost. Tento pracovní prostor se použije v následujících tématech.

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

1. Otevřít `helloworld.xcworkspace` pracovní prostor v Xcode.
1. Nahraďte obsah automaticky vygenerovaného souboru `AppDelegate.m` následujícím kódem:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-macos/helloworld/helloworld/AppDelegate.m#code)]
1. Řetězec `YourSubscriptionKey` nahraďte klíčem předplatného.
1. Řetězec `YourServiceRegion` nahraďte [oblastí](regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

1. Zobrazte výstup ladění: vyberte **View** > **Debug Area** > **Activate Console** (Zobrazení > Oblast ladění > Aktivovat konzolu).
1. Sestavit a spustit ukázkový kód tak, že vyberete **produktu** -> **spustit** z nabídky nebo kliknutím **Přehrát** tlačítko.
1. Poté, co klikněte na tlačítko a Řekněme, že pár slov, byste měli vidět text, který je slyšet ve spodní části obrazovky. Při prvním spuštění aplikace, by měla poskytnout přístup k aplikaci do počítače mikrofon výzva.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte ukázky jazyka Objective-C na Githubu](https://aka.ms/csspeech/samples)


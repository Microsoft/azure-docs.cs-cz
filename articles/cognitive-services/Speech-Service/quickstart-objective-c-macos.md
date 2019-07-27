---
title: 'Rychlý start: Rozpoznávání řeči, cíl-C-Speech Service'
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
ms.openlocfilehash: 45cd1210ee6af3c456171a427729f6e16caf2d58
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559357"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-using-the-speech-sdk"></a>Rychlý start: Rozpoznávání řeči v cíli – C v macOS pomocí sady Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku se naučíte, jak vytvořit aplikaci macOS v cíli-C pomocí sady Cognitive Services Speech SDK pro přepisovat řeči zaznamenané z mikrofonu na text.

## <a name="prerequisites"></a>Požadavky

Než začnete, tady je seznam požadovaných součástí:

* A [klíč předplatného](get-started.md) pro Speech Service
* MacOS počítač s [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) nebo novějším a MacOS 10,13 nebo novějším

## <a name="get-the-speech-sdk-for-macos"></a>Získat sadu Speech SDK pro macOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Aktuální verze sady Cognitive Services Speech SDK je `1.6.0`.

Sada Cognitive Services Speech SDK for Mac je distribuována jako sada rozhraní.
Dá se použít v projektech Xcode jako [CocoaPod](https://cocoapods.org/), nebo stahovat z https://aka.ms/csspeech/macosbinary a propojit ručně. Tato příručka používá CocoaPod.

## <a name="create-an-xcode-project"></a>Vytvoření projektu Xcode

Spusťte Xcode a kliknutím na **File** > **New** > **Project** (Soubor > Nový > Projekt) spusťte nový projekt.
V dialogovém okně Výběr šablony vyberte šablonu aplikace pro kakao.

V následujících dialogových oknech proveďte následující výběry:

1. Dialogové okno Project Options (Možnosti projektu)
    1. Zadejte název aplikace Rychlý start, například `helloworld`.
    1. Zadejte název příslušné organizace a identifikátor organizace, pokud již máte účet Apple Developer. Pro účely testování stačí vybrat jakýkoli název, třeba `testorg`. K podepsání aplikace potřebujete správný zřizovací profil. Podrobnosti najdete na [webu pro vývojáře Apple](https://developer.apple.com/) .
    1. Ujistěte se, že jako jazyk projektu je zvolený jazyk Objective-C.
    1. Pokud chcete použít scénáře a vytvořit aplikaci založenou na dokumentu, zakažte zaškrtávací políčka. Jednoduché uživatelské rozhraní pro ukázkovou aplikaci se vytvoří programově.
    1. Zrušte zaškrtnutí všech políček týkajících se testů a základních dat.
    ![Nastavení projektu](media/sdk/qs-objectivec-macos-project-settings.png)
1. Vyberte adresář projektu.
    1. Vyberte adresář, do kterého se má projekt umístit. Tím se vytvoří `helloworld` adresář v domovském adresáři, který obsahuje všechny soubory projektu Xcode.
    1. Zakažte vytvoření úložiště Git pro tento ukázkový projekt.
1. Nastavte oprávnění pro přístup k síti a mikrofonu. Kliknutím na název aplikace v prvním řádku v přehledu vlevo získáte konfiguraci aplikace a pak zvolíte kartu Možnosti.
    1. Povolte pro aplikaci nastavení "Sandbox aplikace".
    1. Zaškrtněte políčka pro přístup odchozí připojení a mikrofon.
    ![Nastavení izolovaného prostoru](media/sdk/qs-objectivec-macos-sandbox.png)
1. Aplikace musí také deklarovat použití mikrofonu v `Info.plist` souboru. V přehledu klikněte na soubor a přidejte klíč "" soukromí – mikrofon použití popisu "s hodnotou jako" mikrofon je potřeba pro rozpoznávání řeči ".
    ![Nastavení v souboru info. plist](media/sdk/qs-objectivec-macos-info-plist.png)
1. Zavřete projekt Xcode. Později po nastavení CocoaPods budete používat jinou instanci tohoto programu.

## <a name="install-the-sdk-as-a-cocoapod"></a>Instalace sady SDK jako CocoaPod

1. Nainstalujte správce závislostí CocoaPod, jak je popsáno v pokynech k [instalaci](https://guides.cocoapods.org/using/getting-started.html).
1. Přejděte do adresáře ukázkové aplikace (`helloworld`). Umístěte textový soubor s názvem `Podfile` a následujícím obsahem v tomto adresáři:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-macos/helloworld/Podfile)]
1. Přejděte do `helloworld` adresáře v terminálu a spusťte příkaz `pod install`. Tím se vygeneruje `helloworld.xcworkspace` pracovní prostor Xcode obsahující ukázkovou aplikaci a sadu Speech SDK jako závislost. Tento pracovní prostor bude použit v následujících.

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

1. `helloworld.xcworkspace` Otevřete pracovní prostor v Xcode.
1. Nahraďte obsah automaticky vygenerovaného souboru `AppDelegate.m` následujícím kódem:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-macos/helloworld/helloworld/AppDelegate.m#code)]
1. Řetězec `YourSubscriptionKey` nahraďte klíčem předplatného.
1. Řetězec `YourServiceRegion` nahraďte [oblastí](regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

1. Zobrazte výstup ladění: vyberte **View** > **Debug Area** > **Activate Console** (Zobrazení > Oblast ladění > Aktivovat konzolu).
1. Sestavte a spusťte ukázkový kód tak, že v nabídce vyberete**Spustit** **produkt** > nebo kliknete na tlačítko **Přehrát** .
1. Po kliknutí na tlačítko a vyslovení několika slov byste se měli podívat na text, který jste promluví v dolní části obrazovky. Při prvním spuštění aplikace by se vám měla zobrazit výzva k poskytnutí přístupu aplikace k mikrofonu vašeho počítače.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zkoumání objektivů a ukázek v jazyce C na GitHubu](https://aka.ms/csspeech/samples)

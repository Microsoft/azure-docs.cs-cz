---
title: 'Rychlý start: Rozpoznávat řeč v Objective-C v iOS pomocí Cognitive Services SDK řeči'
titleSuffix: Microsoft Cognitive Services
description: Zjistěte, jak rozpoznávat řeč v Objective-C v iOS pomocí Cognitive Services SDK řeči
services: cognitive-services
author: chlandsi
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 09/24/2018
ms.author: chlandsi
ms.openlocfilehash: e21348ccd694baf6b7eccf2787ec0a9f21a73b11
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46985629"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-using-the-cognitive-services-speech-sdk"></a>Rychlý start: Rozpoznávat řeč v Objective-C v iOS pomocí Cognitive Services SDK řeči

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku se dozvíte, jak vytvořit aplikaci pro iOS v Objective-C pomocí Cognitive Services SDK řeči přepisy zvukový soubor s zaznamenané převod řeči na text.

## <a name="prerequisites"></a>Požadavky

* Klíč předplatného pro službu rozpoznávání řeči. Zobrazit [službu řeči si můžete vyzkoušet zdarma](get-started.md).
* Počítač Mac s Xcode 9.4.1 nainstalována jako vývojové prostředí s Iosem. V tomto kurzu, zaměřuje iOS verze 11.4. Pokud nemáte Xcode, ale můžete ho nainstalovat [App Store](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12).

## <a name="get-the-speech-sdk-for-ios"></a>Získat sadou SDK pro řeč pro iOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Aktuální verze sady SDK pro řeč Cognitive Services je `1.0.0`.

Cognitive Services sadou SDK pro řeč pro Mac OS a iOS je momentálně distribuován jako Cocoa Framework.
Můžete ho stáhnout z https://aka.ms/csspeech/iosbinary. Stáhněte soubor do svého domovského adresáře.


## <a name="create-an-xcode-project"></a>Vytvoření projektu Xcode 

Spuštění Xcode, a spusťte nový projekt kliknutím **souboru** > **nový** > **projektu**.
V dialogovém okně Výběr šablony, zvolte "iOS jedním zobrazení aplikace" šablony.

V dialogových oknech, která řídí, proveďte následující výběr:

1. Dialogové okno Možnosti projektu
    1. Zadejte například název aplikace rychlý Start `helloworld`.
    1. Zadejte název organizace, jako například `TestOrg`a identifikátor organizace, jako `testorg`.
    1. Zajistěte, aby že Objective-C je vybrán jako jazyk pro projekt.
    1. Zakážete všechna zaškrtávací políčka pro testy a základních dat.
    ![Nastavení projektu](media/sdk/qs-objectivec-project-settings.png)
1. Vyberte adresář projektu
    1. Zvolte umístění projektu do domovského adresáře. Tím se vytvoří `helloworld` adresáře ve svém domovském adresáři, který obsahuje všechny soubory projektu Xcode.
    1. Zakážete vytvoření úložiště Git pro tento příklad projektu.
    1. Nastavení cesty k sadě SDK v *nastavení projektu*.
        1. V **Obecné** kartu **propojené architektury a knihovny** záhlaví, přidání sady SDK knihovny jako rozhraní: **přidat framework** > **přidat Další...**  > Přejít na domovský adresář a vyberte soubor `MicrosoftCognitiveServicesSpeech.framework`.
        ![Přidání rozhraní Framework](media/sdk/qs-objectivec-framework.png)
        1. Přejděte **nastavení sestavení** kartu a aktivovat **všechny** nastavení.
        1. Přidejte adresář `$(SRCROOT)/..` k *cesty pro hledání rozhraní* pod **cesty pro hledání** záhlaví.
        ![Nastavení cesty pro hledání rámce](media/sdk/qs-objectivec-framework-search-paths.png)
        1. Přidejte adresář `$(SRCROOT)/..` k *cesty pro hledání Runpath* pod **propojování** záhlaví.
        ![Nastavení cesty pro hledání Runpath](media/sdk/qs-objectivec-runpaths.png)


## <a name="set-up-the-ui"></a>Nastavení uživatelského rozhraní

Ukázková aplikace bude mít velmi jednoduché uživatelské rozhraní: tlačítko pro spuštění zpracování souboru a textový popisek k zobrazení výsledku.
Nastaveno v nastavení uživatelského rozhraní `Main.storyboard` součástí projektu.
Otevření zobrazení XML scénáře kliknutím pravým tlačítkem myši `Main.storyboard` položku projektu stromu a vyberete **otevřít jako...**   >  **Zdrojový kód**.
Nahraďte automaticky generované XML s tímto:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>Přidejte ukázkový kód

1. Stáhněte si [ukázkový soubor wav](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) tak, že kliknete pravým tlačítkem na odkaz a zvolíte **Uložit cíl jako...** . Přidáte soubor wav do projektu jako prostředek jeho přetažením z Finderu okna na kořenové úrovni zobrazení projektu.
Klikněte na tlačítko **Dokončit** v následujícím dialogovém beze změny nastavení.
1. Nahraďte jeho obsah automaticky generované `ViewController.m` souboru podle:

   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/ViewController.m#code)]
1. Nahraďte řetězec `YourSubscriptionKey` s klíči předplatného.

1. Nahraďte řetězec `YourServiceRegion` s [oblasti](regions.md) přidružených k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).


## <a name="building-and-running-the-sample"></a>Vytváření a spouštění vzorku

1. Zviditelnit výstupu ladění (**zobrazení** > **ladění oblasti** > **aktivovat konzoly**).
1. Sestavit a spustit ukázkový kód v simulátoru iOS tak, že vyberete **produktu** -> **spustit** z nabídky nebo kliknutím **Přehrát** tlačítko.
1. Po kliknutí na "Rozpoznávat!" tlačítko v aplikaci, by se měla zobrazit obsah zvukového souboru "Jak se počasí, třeba?" ve spodní části obrazovky s Simulovaná.

 ![Simulované aplikace pro iOS](media/sdk/qs-objectivec-simulated-app.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Hledat v této ukázce `quickstart/objectivec-ios` složky.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Získat naše ukázky](speech-sdk.md#get-the-samples)

---
title: 'Úvodní příručka: Syntetizační řeč, Objective-C - Služba řeči'
titleSuffix: Azure Cognitive Services
description: Naučte se syntetizovat řeč v objective-C na macOS pomocí sady Speech SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: 37eed03ed839411f1acf5d963d4118a3c6d2c379
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975920"
---
# <a name="quickstart-synthesize-speech-in-objective-c-on-macos-using-the-speech-sdk"></a>Úvodní příručka: Syntetizovat řeč v objective-C na macOS pomocí sady Speech SDK

V tomto článku se dozvíte, jak vytvořit aplikaci pro macOS v Objective-C pomocí sady Cognitive Services Speech SDK k syntéze řeči z textu a přehrání s výchozím zvukovým výstupem.

## <a name="prerequisites"></a>Požadavky

Než začnete, tady je seznam předpokladů:

* [Klíč předplatného](~/articles/cognitive-services/Speech-Service/get-started.md) pro službu Řeč
* Počítač s macOS s [Kódem Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) nebo novějším a macOS 10.13 nebo novějším

## <a name="get-the-speech-sdk-for-macos"></a>Získání sady Speech SDK pro macOS

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Všimněte si, že tento kurz nebude fungovat s verzí sady SDK starší než 1.7.0.

Sada SDK pro rozpoznávání řeči služeb Cognitive Services pro Mac je distribuována jako sada architektury.
To může být použit v xcode projektech jako [CocoaPod](https://cocoapods.org/), nebo stáhnout z https://aka.ms/csspeech/macosbinary a souvisí ručně. Tato příručka používá Kakaový pod.

## <a name="create-an-xcode-project"></a>Vytvoření projektu Xcode

Spusťte Xcode a začněte nový projekt klepnutím na **položku Soubor** > **nového** > **projektu**.
V dialogovém okně výběru šablony zvolte šablonu "Kakaová aplikace".

V následujících dialogových oknech proveďte následující výběry:

1. Dialogové okno Project Options (Možnosti projektu)
    1. Zadejte název aplikace Rychlý start, například `helloworld`.
    1. Pokud už máte vývojářský účet Apple, zadejte příslušný název organizace a identifikátor organizace. Pro účely testování stačí vybrat jakýkoli název, třeba `testorg`. Chcete-li podepsat aplikaci, potřebujete správný zřizovací profil. Podrobnosti najdete na [webu pro vývojáře společnosti Apple.](https://developer.apple.com/)
    1. Ujistěte se, že jako jazyk projektu je zvolený jazyk Objective-C.
    1. Zaškrtávací políčka chcete použít scénáře a vytvořit aplikaci založenou na dokumentu. Jednoduché ui pro ukázkovou aplikaci bude vytvořeno programově.
    1. Zrušte zaškrtnutí všech políček týkajících se testů a základních dat.
    ![Nastavení projektu](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-project-settings.png)
1. Vyberte adresář projektu.
    1. Zvolte adresář, do který chcete projekt vložit. Tím se `helloworld` vytvoří adresář v domovském adresáři, který obsahuje všechny soubory pro projekt Xcode.
    1. Zakažte vytvoření úložiště Git pro tento ukázkový projekt.
1. Nastavte nároky pro přístup k síti. Kliknutím na název aplikace v prvním řádku v přehledu vlevo přejděte ke konfiguraci aplikace a pak zvolte kartu Možnosti.
    1. Povolte nastavení "Izolovaného prostoru aplikace" pro aplikaci.
    1. Povolte zaškrtávací políčka pro přístup "Odchozí připojení".
    ![Nastavení izolovaného prostoru](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-sandbox-tts.png)
1. Zavřete projekt Xcode. Po nastavení kakaopodů budete později použít jinou instanci.

## <a name="install-the-sdk-as-a-cocoapod"></a>Instalace sady SDK jako kakaového podu

1. Nainstalujte Správce závislostí CocoaPod, jak je popsáno v [jeho pokynech k instalaci](https://guides.cocoapods.org/using/getting-started.html).
1. Přejděte do adresáře ukázkové aplikace (`helloworld`). Do adresáře umístěte `Podfile` textový soubor s názvem a následujícím obsahem:  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/text-to-speech/helloworld/Podfile)]
1. Přejděte `helloworld` do adresáře v terminálu a spusťte příkaz `pod install`. Tím se `helloworld.xcworkspace` vygeneruje pracovní prostor Xcode obsahující ukázkovou aplikaci i sadu Speech SDK jako závislost. Tento pracovní prostor bude použit v následujícím textu.

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

1. Otevřete `helloworld.xcworkspace` pracovní prostor v Xcode.
1. Nahraďte obsah automaticky vygenerovaného souboru `AppDelegate.m` následujícím kódem:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/text-to-speech/helloworld/helloworld/AppDelegate.m#code)]
1. Řetězec `YourSubscriptionKey` nahraďte klíčem předplatného.
1. Řetězec `YourServiceRegion` nahraďte [oblastí](~/articles/cognitive-services/Speech-Service/regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

1. Zviditelnit ladicí výstup **(Zobrazit** > **konzolu** > **aktivace**ladění).
1. Vytvořte a spusťte ukázkový kód tak, že v nabídce vyberete **Spustit produkt** -> **Run** nebo kliknete na tlačítko **Přehrát.**
1. Po zadání textu a kliknutí na tlačítko v aplikaci byste měli slyšet přehráno syntetizovaný zvuk.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte ukázky objective-c na GitHubu](https://aka.ms/csspeech/samples)


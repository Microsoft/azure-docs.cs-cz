---
title: 'Úvodní příručka: Rozpoznávání řeči z mikrofonu, služba Objective-C - Speech Service'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak rozpoznat řeč v objective-C na macOS pomocí sady Speech SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: chlandsi
ms.openlocfilehash: c2f0fbe66b26c6eca6e0c0b2530efacba9bae958
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75380588"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-by-using-the-speech-sdk"></a>Úvodní příručka: Rozpoznání řeči v objective-C v macOS pomocí sady Speech SDK

Rychlé starty jsou také k dispozici pro [syntézu řeči](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-macos.md).

V tomto článku se dozvíte, jak vytvořit aplikaci macOS v Objective-C pomocí Azure Cognitive Services řeči SDK přepsat řeči zaznamenané z mikrofonu na text.

## <a name="prerequisites"></a>Požadavky

Než začnete, budete potřebovat:

* [Klíč předplatného](~/articles/cognitive-services/Speech-Service/get-started.md) pro službu Řeč.
* Počítač s macOS s [Kódem 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) nebo novějším a macOS 10.13 nebo novějším.

## <a name="get-the-speech-sdk-for-macos"></a>Získání sady Speech SDK pro macOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Sada SDK pro rozpoznávání řeči služeb Cognitive Services pro Mac je distribuována jako sada architektury. Může být použit v xcode projektech jako [CocoaPod](https://cocoapods.org/) nebo stáhnout z https://aka.ms/csspeech/macosbinary a propojit ručně. Tento článek používá Kakaový pod.

## <a name="create-an-xcode-project"></a>Vytvoření projektu Xcode

Spusťte Xcode a začněte nový projekt výběrem **možnosti Soubor** > **nového** > **projektu**. V dialogovém okně výběru šablony vyberte šablonu **Aplikace pro kakao.**

V následujících dialogových oknech proveďte následující výběry.

1. V dialogovém okně **Možnosti projektu:**
    1. Zadejte název aplikace pro rychlý start, například *helloworld*.
    1. Pokud už máte vývojářský účet Apple, zadejte příslušný název organizace a identifikátor organizace. Pro účely testování použijte název jako *testorg*. Chcete-li podepsat aplikaci, potřebujete správný zřizovací profil. Další informace naleznete na [webu pro vývojáře společnosti Apple](https://developer.apple.com/).
    1. Ujistěte **se,** že cíl C je vybrán jako jazyk pro projekt.
    1. Zrušte zaškrtnutí políček, chcete-li použít scénáře a vytvořit aplikaci založenou na dokumentu. Jednoduché ui pro ukázkovou aplikaci se vytvoří programově.
    1. Zrušte zaškrtnutí všech políček pro testy a základní data.

    ![Nastavení projektu](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-project-settings.png)

1. Vyberte adresář projektu:
    1. Zvolte adresář, do který chcete projekt vložit. Tento krok vytvoří helloworld adresář ve vašem domovském adresáři, který obsahuje všechny soubory pro projekt Xcode.
    1. Zakažte vytvoření úložiště Git pro tento ukázkový projekt.
1. Nastavte nároky pro přístup k síti a mikrofonu. Vyberte název aplikace v prvním řádku v přehledu vlevo, abyste se dostali ke konfiguraci aplikace. Pak vyberte kartu **Možnosti.**
    1. Povolte nastavení **izolovaného prostoru aplikace** pro aplikaci.
    1. Zaškrtněte políčka **U odchozích připojení** a přístupu k **mikrofonu.**

    ![Nastavení izolovaného prostoru](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-sandbox.png)

1. Aplikace také musí deklarovat použití `Info.plist` mikrofonu v souboru. Vyberte soubor v přehledu a přidejte klíč **Popis využití mikrofonu – ochrana osobních údajů** s hodnotou, jako je *mikrofon, je potřebný pro rozpoznávání řeči*.

    ![Nastavení v souboru Info.plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-info-plist.png)

1. Zavřete projekt Xcode. Po nastavení kakaových podů později použijete jinou instanci.

## <a name="install-the-sdk-as-a-cocoapod"></a>Instalace sady SDK jako kakaového podu

1. Nainstalujte Správce závislostí CocoaPod, jak je popsáno v [jeho pokynech k instalaci](https://guides.cocoapods.org/using/getting-started.html).
1. Přejděte do adresáře ukázkové aplikace, což je helloworld. Umístěte textový soubor s názvem *Podfile* a následujícím obsahem do tohoto adresáře:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/Podfile)]
1. Přejděte do adresáře helloworld v terminálu a spusťte příkaz `pod install`. Tento příkaz generuje `helloworld.xcworkspace` pracovní prostor Xcode, který obsahuje ukázkovou aplikaci i sadu Speech SDK jako závislost. Tento pracovní prostor se používá v následujících krocích.

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

1. Otevřete pracovní `helloworld.xcworkspace` prostor v Xcode.
1. Nahraďte obsah automaticky `AppDelegate.m` generovaného souboru následujícím kódem:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/helloworld/AppDelegate.m#code)]
1. Řetězec `YourSubscriptionKey` nahraďte klíčem předplatného.
1. Nahraďte `YourServiceRegion` řetězec [oblastí](~/articles/cognitive-services/Speech-Service/regions.md) přidruženou k vašemu předplatnému. Můžete například `westus` použít bezplatné zkušební předplatné.

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

1. Zviditelnění výstupu ladění výběrem **možnosti Zobrazit** > **zónu** > aktivace ladění **.**
1. Vytvořte a spusťte ukázkový kód výběrem**položky Spustit** **produkt** > z nabídky. Můžete také vybrat **možnost Přehrát**.
1. Po výběru tlačítka a vyslovení několika slov byste měli vidět text, který jste vyslovili, ve spodní části obrazovky. Při prvním spuštění aplikace byste měli být vyzváni k poskytnutí přístupu k mikrofonu počítače.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte ukázky objective-c na GitHubu](https://aka.ms/csspeech/samples)

---
title: 'Rychlý start: Rozpoznávání řeči, C++ (Windows) – služba Speech'
titleSuffix: Azure Cognitive Services
description: Naučte se rozpoznávat řeč ve C++ Windows desktopu pomocí sady Speech SDK
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: wolfma
ms.openlocfilehash: a9c43e1d27a396a2c3e9123ce5ce538296c6870c
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70381938"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Rychlý start: Rozpoznávání řeči v C++ systému Windows pomocí sady Speech SDK

K dispozici jsou také rychlé starty pro [syntézu řeči](quickstart-text-to-speech-cpp-windows.md) a [Překlad řeči](quickstart-translate-speech-cpp-windows.md).

Pokud chcete, vyberte jiný programovací jazyk a prostředí:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku vytvoříte konzolovou aplikaci pro Windows v jazyce C++. Použijete sadu Cognitive Services [Speech SDK](speech-sdk.md) k přepisu řeči na text v reálném čase z mikrofonu počítače. Aplikace je sestavená pomocí [balíčku NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a Microsoft Visual Studio 2019 (libovolná edice).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu budete potřebovat klíč předplatného služby Speech Services. Můžete ho získat zdarma. Další informace najdete v tématu o [bezplatném pokusu o hlasové služby](get-started.md) .

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete zdrojový soubor **helloworld.cpp**.

1. Nahraďte celý kód následujícím fragmentem kódu:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. Ve stejném souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.

1. Řetězec `YourServiceRegion` nahraďte [oblastí](regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

1. V řádku nabídek vyberte **soubor** > **Uložit vše**.

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

1. V řádku nabídek vyberte **sestavit** > **sestavení řešení** a sestavte aplikaci. Kód by se teď měl zkompilovat bez chyb.

1. Zvolte **ladění** > **Spustit ladění** (nebo stiskněte klávesu **F5**) a spusťte aplikaci **HelloWorld** .

1. Vyslovte anglickou frázi nebo větu. Aplikace přenáší váš hlas do služeb rozpoznávání řeči, které transcribes text a pošle je zpátky do aplikace k zobrazení.

   ![Výstup na konzole po úspěšném rozpoznání](media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Další postup

Další ukázky, například jak číst řeč ze zvukového souboru, jsou k dispozici na GitHubu.

> [!div class="nextstepaction"]
> [Prozkoumejte C++ ukázky na GitHubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Viz také:

- [Výuka modelu pro Custom Speech](how-to-custom-speech-train-model.md)

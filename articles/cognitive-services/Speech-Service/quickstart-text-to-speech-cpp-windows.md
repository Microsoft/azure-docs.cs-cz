---
title: 'Rychlý start: Syntetizace řeči, C++ (Windows) – služba Speech'
titleSuffix: Azure Cognitive Services
description: Naučte se, jak na ploše C++ Windows syntetizovat řeč pomocí sady Speech SDK
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/24/2019
ms.author: yinhew
ms.openlocfilehash: 686b21d3e02266af77687778c32f0d1ca6d55154
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383074"
---
# <a name="quickstart-synthesize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Rychlý start: Používání sady Speech SDK C++ v systému Windows syntetiz řeči

K dispozici jsou také rychlé starty pro [rozpoznávání řeči](quickstart-cpp-windows.md) a [Překlad řeči](quickstart-translate-speech-cpp-windows.md).

V tomto článku vytvoříte konzolovou aplikaci pro Windows v jazyce C++. [Sada Cognitive Services Speech SDK](speech-sdk.md) slouží k syntetizování řeči z textu v reálném čase a přehrávání řeči v mluvčím počítače. Aplikace je sestavená pomocí [balíčku NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a Microsoft Visual Studio 2019 (libovolná edice).

Úplný seznam jazyků a hlasů dostupných pro syntézu řeči najdete v tématu [Podpora jazyků](language-support.md#text-to-speech).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu budete potřebovat klíč předplatného služby Speech Services. Můžete ho získat zdarma. Další informace najdete v tématu o [bezplatném pokusu o hlasové služby](get-started.md) .

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete zdrojový soubor **helloworld.cpp**.

1. Nahraďte celý kód následujícím fragmentem kódu:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/cpp-windows/helloworld/helloworld.cpp#code)]

1. Ve stejném souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.

1. Řetězec `YourServiceRegion` nahraďte [oblastí](regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

1. V řádku nabídek vyberte **soubor** > **Uložit vše**.

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

1. V řádku nabídek vyberte **sestavit** > **sestavení řešení** a sestavte aplikaci. Kód by se teď měl zkompilovat bez chyb.

1. Zvolte **ladění** > **Spustit ladění** (nebo stiskněte klávesu **F5**) a spusťte aplikaci **HelloWorld** .

1. Zadejte anglickou frázi nebo větu. Aplikace přenáší váš text na hlasové služby, které do aplikace odesílají syntetizované rozpoznávání řeči, aby bylo možné na svém mluvčím hrát.

   ![Výstup na konzole po úspěšném Shrnutí řeči](media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>Další postup

Další ukázky, jako je například ukládání řeči do zvukového souboru, jsou k dispozici na GitHubu.

> [!div class="nextstepaction"]
> [Prozkoumejte C++ ukázky na GitHubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Viz také:

- [Vytvoření vlastního hlasu](how-to-custom-voice-create-voice.md)
- [Zaznamenat vlastní ukázky hlasu](record-custom-voice-samples.md)

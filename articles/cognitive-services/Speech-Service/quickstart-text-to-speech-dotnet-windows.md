---
title: 'Rychlý start: Rozpoznávání řeči, .NET Framework (Windows) – služba Speech'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto průvodce můžete vytvořit konzolovou aplikaci pro převod textu na řeč pomocí rozhraní .NET Framework pro Windows a sady Speech SDK. Až budete hotovi, můžete řeč z textu a slyšet řeč na mluvčím v reálném čase.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/27/2019
ms.author: yinhew
ms.openlocfilehash: 1a411455e4a6dea22e092cdfc8e70ee23b656435
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327456"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Rychlý start: Vysyntetizujte řeč pomocí sady Speech SDK pro .NET Framework (Windows)

K dispozici jsou také rychlé starty pro [rozpoznávání řeči](quickstart-csharp-dotnet-windows.md) a [Překlad řeči](quickstart-translate-speech-dotnetframework-windows.md).

Pomocí tohoto průvodce můžete vytvořit konzolovou aplikaci pro převod textu na řeč pomocí rozhraní .NET Framework pro Windows a sady Speech SDK. Až budete hotovi, můžete řeč z textu a slyšet řeč na mluvčím v reálném čase.

Pro rychlou ukázku (bez sestavování projektu sady Visual Studio, jak je popsáno v tomto článku), získáte nejnovější [ukázky sady Cognitive Services Speech SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk) z GitHubu.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto projektu budete potřebovat:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Klíč předplatného pro službu rozpoznávání řeči [Získejte je zdarma](get-started.md).
* K dispozici je mluvčí (nebo náhlavní souprava).

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete **program.cs** a nahraďte automaticky generovaný kód touto ukázkou:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Vyhledejte řetězec `YourSubscriptionKey` a nahraďte ho klíčem předplatného služby Speech Services.

1. Vyhledejte řetězec `YourServiceRegion` a nahraďte ho [oblastí](regions.md) , která je přidružená k vašemu předplatnému. Pokud například používáte bezplatné zkušební předplatné, oblast je `westus`.

1. V řádku nabídek vyberte **soubor** > **Uložit vše**.

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

1. V řádku nabídek vyberte **sestavení** **řešení**  >  pro sestavení aplikace. Kód by se teď měl zkompilovat bez chyb.

1. Zvolte možnost **ladění** > **Spustit ladění** (nebo vyberte **F5**) a spusťte aplikaci **HelloWorld** .

1. Zadejte anglickou frázi nebo větu. Aplikace přenáší váš text na hlasové služby, které do aplikace odesílají syntetizované rozpoznávání řeči, aby bylo možné na svém mluvčím hrát.

   ![Uživatelské rozhraní syntézy řeči](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte C# ukázky na GitHubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Viz také:

- [Vytvoření vlastního hlasu](how-to-custom-voice-create-voice.md)
- [Zaznamenat vlastní ukázky hlasu](record-custom-voice-samples.md)

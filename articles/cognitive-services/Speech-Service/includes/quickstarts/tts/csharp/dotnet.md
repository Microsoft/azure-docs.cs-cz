---
title: 'Rychlý Start: syntetizace řeči C# , (Windows) – služba Speech'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto průvodce můžete vytvořit konzolovou aplikaci pro převod textu na řeč pomocí rozhraní .NET Framework pro Windows a sady Speech SDK. Až budete hotovi, můžete řeč z textu a slyšet řeč na mluvčím v reálném čase.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/27/2019
ms.author: yinhew
ms.openlocfilehash: b615ba8085650e9aa686fb4a229d9752c4f6e2ce
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925892"
---
## <a name="prerequisites"></a>Předpoklady

Než začnete, nezapomeňte:

> [!div class="checklist"]
> * [Vytvoření prostředku Azure Speech](../../../../get-started.md)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=dotnet)

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete **program.cs** a nahraďte automaticky generovaný kód touto ukázkou:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/text-to-speech/helloworld/Program.cs#code)]

1. Vyhledejte řetězec `YourSubscriptionKey`a nahraďte ho klíčovým předplatným služby Speech.

1. Vyhledejte řetězec `YourServiceRegion`a nahraďte ho [oblastí](~/articles/cognitive-services/Speech-Service/regions.md) , která je přidružená k vašemu předplatnému. Pokud například používáte bezplatné zkušební předplatné, oblast je `westus`.

1. V řádku nabídek vyberte možnost **soubor** > **Uložit vše**.

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

1. V řádku nabídek výběrem možnosti **sestavit** > **Sestavit řešení** sestavte aplikaci. Kód by se teď měl zkompilovat bez chyb.

1. Zvolte **ladění** > **Spustit ladění** (nebo vyberte **F5**) a spusťte aplikaci **HelloWorld** .

1. Zadejte anglickou frázi nebo větu. Aplikace přenáší váš text do služby pro rozpoznávání řeči, která odešle do aplikace syntetizované rozpoznávání řeči, aby bylo možné na svém mluvčím hrát.

   ![Uživatelské rozhraní syntézy řeči](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Viz také

- [Vytvoření vlastního hlasu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Zaznamenat vlastní ukázky hlasu](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)

---
title: 'Rychlý Start: syntetizace řeči, C# (Windows) – služba pro rozpoznávání řeči'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto průvodce můžete vytvořit konzolovou aplikaci pro převod textu na řeč pomocí rozhraní .NET Framework pro Windows a sady Speech SDK. Až budete hotovi, můžete řeč z textu a slyšet řeč na mluvčím v reálném čase.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 89f491aae91787ec6b67b9aa2daee262f3bab62e
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81275301"
---
## <a name="prerequisites"></a>Požadavky

Než začnete, nezapomeňte:

> [!div class="checklist"]
> * [Vytvoření prostředku Azure Speech](../../../../get-started.md)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete **program.cs** a nahraďte automaticky generovaný kód touto ukázkou:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/text-to-speech/helloworld/Program.cs#code)]

1. Vyhledejte řetězec `YourSubscriptionKey`a nahraďte ho klíčovým předplatným služby Speech.

1. Vyhledejte řetězec `YourServiceRegion`a nahraďte ho [oblastí](~/articles/cognitive-services/Speech-Service/regions.md) , která je přidružená k vašemu předplatnému. Pokud například používáte bezplatné zkušební předplatné, je `westus`tato oblast.

1. V řádku nabídek vyberte **soubor** > **Uložit vše**.

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

1. V řádku nabídek vyberte **sestavení** > sestavit**řešení** a sestavte aplikaci. Kód by se teď měl zkompilovat bez chyb.

1. Zvolte **ladění** > **Spustit ladění** (nebo vyberte **F5**) a spusťte aplikaci **HelloWorld** .

1. Zadejte anglickou frázi nebo větu. Aplikace přenáší váš text do služby pro rozpoznávání řeči, která odešle do aplikace syntetizované rozpoznávání řeči, aby bylo možné na svém mluvčím hrát.

   ![Uživatelské rozhraní syntézy řeči](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Viz také

- [Vytvoření vlastního hlasu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Zaznamenat vlastní ukázky hlasu](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)

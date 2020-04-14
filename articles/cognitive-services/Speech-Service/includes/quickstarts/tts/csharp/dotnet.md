---
title: 'Úvodní příručka: Syntetizovat řeč, C# (Windows) - služba řeči'
titleSuffix: Azure Cognitive Services
description: Tato příručka slouží k vytvoření konzoly pro převod textu na řeč pomocí rozhraní .NET framework pro systém Windows a sady Speech SDK. Po dokončení můžete syntetizovat řeč z textu a slyšet řeč na reproduktoru v reálném čase.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 89f491aae91787ec6b67b9aa2daee262f3bab62e
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81275301"
---
## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

> [!div class="checklist"]
> * [Vytvoření řečového prostředku Azure](../../../../get-started.md)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete **Program.cs** a nahraďte automaticky generovaný kód touto ukázkou:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/text-to-speech/helloworld/Program.cs#code)]

1. Najděte `YourSubscriptionKey`řetězec a nahraďte ho klíčem předplatného služby Speech.

1. Najděte `YourServiceRegion`řetězec a nahraďte ho [oblastí](~/articles/cognitive-services/Speech-Service/regions.md) přidruženou k vašemu předplatnému. Pokud například používáte bezplatné zkušební předplatné, oblast `westus`je .

1. Na řádku nabídek zvolte **Soubor** > **Uložit vše**.

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

1. Na řádku nabídek zvolte **Sestavení** > **sestavení řešení** k sestavení aplikace. Kód by se teď měl zkompilovat bez chyb.

1. Zvolte **Ladění** > **start ladění** (nebo vyberte **F5**) pro spuštění aplikace **helloworld.**

1. Zadejte anglickou frázi nebo větu. Aplikace přenáší váš text do služby Řeč, která odesílá syntetizovaný projev do aplikace k přehrávání na reproduktoru.

   ![Uživatelské rozhraní pro syntézu řeči](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Viz také

- [Vytvoření vlastního hlasu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Záznam vlastních hlasových ukázek](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)

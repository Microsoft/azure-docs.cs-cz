---
title: 'Rychlý Start: syntetizace řeči C# , (.NET Core) – služba Speech'
titleSuffix: Azure Cognitive Services
description: Naučte se, jak v C# rámci .NET Core v systému Windows syntetizovat řeč pomocí sady Speech SDK
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: ba6a373f678f8d34b34a669b3fbb3b570c90ed0d
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925361"
---
> [!NOTE]
> .NET Core je Open Source multiplatformní rozhraní .NET, které implementuje specifikace rozhraní [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

## <a name="prerequisites"></a>Předpoklady

Než začnete, nezapomeňte:

> [!div class="checklist"]
> * [Vytvoření prostředku Azure Speech](../../../../get-started.md)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=dotnetcore)

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete soubor `Program.cs` a veškerý kód nahraďte následujícím kódem.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnetcore/text-to-speech/helloworld/Program.cs#code)]

1. Ve stejném souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.

1. Také řetězec `YourServiceRegion` nahraďte [oblastí](~/articles/cognitive-services/Speech-Service/regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

1. Uložte změny do projektu.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. Sestavte aplikaci. V řádku nabídek zvolte **Sestavení** > **Sestavit řešení**. Kód by se měl zkompilovat bez chyb.

    ![Snímek obrazovky aplikace Visual Studio s zvýrazněnou možností řešení sestavení](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Úspěšné sestavení")

1. Spusťte aplikaci. V řádku nabídek zvolte **Ladit** > **Spustit ladění** nebo stiskněte klávesu **F5**.

    ![Snímek obrazovky aplikace Visual Studio s zvýrazněnou možností spustit ladění](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Spustit aplikaci do ladění")

1. Zobrazí se okno konzoly, které vás vyzve k zadání textu. Zadejte několik slov nebo větu. Text, který jste zadali, se přenáší do služby pro rozpoznávání řeči a syntetizuje se na řeč, který hraje na svém mluvčím.

    ![Snímek obrazovky s výstupem konzoly po úspěšném Shrnutí](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "Výstup na konzole po úspěšném Shrnutí")

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Viz také

- [Vytvoření vlastního hlasu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Zaznamenat vlastní ukázky hlasu](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)

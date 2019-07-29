---
title: 'Rychlý start: Rozpoznávání řeči, C# (.NET Core) – služba Speech'
titleSuffix: Azure Cognitive Services
description: Naučte se, jak v C# rámci .NET Core v systému Windows syntetizovat řeč pomocí sady Speech SDK
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: ccb6ec5b6f29c57a6136c571558d4126d0607710
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607688"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-core"></a>Rychlý start: Sada Speech SDK pro .NET Core syntetizuje řeč

K dispozici jsou také rychlé starty pro [rozpoznávání řeči](quickstart-csharp-dotnetcore-windows.md) a [Překlad řeči](quickstart-translate-speech-dotnetcore-windows.md).

V tomto článku vytvoříte konzolovou aplikaci v jazyce C# pro .NET Core ve Windows s využitím sady Cognitive Services [Speech SDK](speech-sdk.md). Rozpoznávání řeči od textu v reálném čase do mluvčího počítače. Aplikace je sestavená pomocí [balíčku NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a Microsoft Visual Studio 2017 nebo novější (libovolná edice).

> [!NOTE]
> .NET Core je Open Source multiplatformní rozhraní .NET, které implementuje specifikace rozhraní [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

K dokončení tohoto rychlého startu budete potřebovat klíč předplatného služby Speech Services. Můžete ho získat zdarma. Další informace najdete v tématu o bezplatném pokusu o [hlasové služby](get-started.md) .

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* [Sada .NET Core SDK](https://dotnet.microsoft.com/download)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) nebo novější
* Klíč předplatného Azure pro službu Speech Service. [Získejte je zdarma](get-started.md).

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-dotnetcore-create-proj.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete soubor `Program.cs` a veškerý kód nahraďte následujícím kódem.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnetcore/helloworld/Program.cs#code)]

1. Ve stejném souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.

1. Také řetězec `YourServiceRegion` nahraďte [oblastí](regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

1. Uložte změny do projektu.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. Sestavte aplikaci. V řádku nabídek zvolte **Sestavení** > **Sestavit řešení**. Kód by se měl zkompilovat bez chyb.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Sestavit řešení](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Úspěšné sestavení")

1. Spusťte aplikaci. V řádku nabídek zvolte **Ladit** > **Spustit ladění** nebo stiskněte klávesu **F5**.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Spustit ladění](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Spuštění aplikace s laděním")

1. Zobrazí se okno konzoly, které vás vyzve k zadání textu. Zadejte několik slov nebo větu. Text, který jste zadali, se přenáší do služeb řeči a syntetizuje se na řeč, který hraje na reproduktoru.

    ![Snímek obrazovky s výstupem konzoly po úspěšném Shrnutí](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "Výstup na konzole po úspěšném Shrnutí")

## <a name="next-steps"></a>Další kroky

Na GitHubu jsou k dispozici další ukázky, například jak syntetizovat řeč ke zvukovému souboru.

> [!div class="nextstepaction"]
> [Prozkoumejte C# ukázky na GitHubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Viz také:

- [Přizpůsobení hlasových písem](how-to-customize-voice-font.md)
- [Záznam ukázek hlasu](record-custom-voice-samples.md)

---
title: 'Rychlý start: Syntetizace řeči, C# (.NET Core) – hlasové služby'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak tak, aby odpovídaly řeči v C# v .NET Core ve Windows s použitím sadou SDK pro řeč
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: 7b4a018e38ca625e38dc1658a95d3ce0e677f711
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67467422"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-core"></a>Rychlý start: Syntetizace řeči se sadou SDK pro řeč pro .NET Core

Rychlí průvodci jsou také k dispozici pro [rozpoznávání řeči](quickstart-csharp-dotnetcore-windows.md) a [překlad řeči](quickstart-translate-speech-dotnetcore-windows.md).

V tomto článku vytvoříte konzolovou aplikaci v jazyce C# pro .NET Core ve Windows s využitím sady Cognitive Services [Speech SDK](speech-sdk.md). Můžete syntetizace řeč z textu v reálném čase k vašemu počítači mluvčího. Tato aplikace je vytvořená pomocí [balíčku NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a sady Microsoft Visual Studio 2017 (libovolné edice).

> [!NOTE]
> .NET Core je Open Source multiplatformní rozhraní .NET, které implementuje specifikace rozhraní [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

Klíč předplatného hlasové služby k dokončení tohoto rychlého startu potřebujete. Můžete ho získat zdarma. Zobrazit [hlasové služby si můžete vyzkoušet zdarma](get-started.md) podrobnosti.

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* [Sada .NET Core SDK](https://dotnet.microsoft.com/download)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Klíč předplatného pro službu rozpoznávání řeči. [Získat zdarma](get-started.md).

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

1. Zobrazí se okno konzoly vás vyzve k zadání nějaký text. Zadejte pár slov nebo věty. Text, který jste zadali se přenášejí do služeb řeči a syntetizovat do mluvené řeči, který přehraje na reproduktoru.

    ![Snímek obrazovky výstup na konzole po úspěšné syntézu](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "výstupu konzoly po úspěšné syntézu")

## <a name="next-steps"></a>Další postup

Další ukázky, jako je například postup syntetizace převod řeči na zvukový soubor, jsou k dispozici na Githubu.

> [!div class="nextstepaction"]
> [Prozkoumejte C# ukázky na Githubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Další informace najdete v tématech

- [Přizpůsobení hlasová písma](how-to-customize-voice-font.md)
- [Záznam hlasu ukázky](record-custom-voice-samples.md)

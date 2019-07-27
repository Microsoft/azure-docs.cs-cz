---
title: 'Rychlý start: Rozpoznávání řeči, C# (.NET Core) – služba Speech'
titleSuffix: Azure Cognitive Services
description: Naučte se rozpoznávat řeč v C# části .NET Core ve Windows nebo MacOS pomocí sady Speech SDK.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 3ba82f2bb3d8325b7cf77f357471dc4f25382ff6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559523"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-core"></a>Rychlý start: Rozpoznávání řeči pomocí sady Speech SDK pro .NET Core

K dispozici jsou také rychlé starty pro převod [textu na řeč](quickstart-text-to-speech-dotnetcore.md) a [Převod řeči](quickstart-translate-speech-dotnetcore-windows.md).

V případě potřeby vyberte jiný programovací jazyk nebo prostředí:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku vytvoříte C# konzolovou aplikaci pro .NET Core ve Windows nebo MacOS pomocí sady Cognitive Services [Speech SDK](speech-sdk.md). V reálném čase přepíšete řeč z mikrofonu počítače na text. Tato aplikace je vytvořená pomocí [balíčku NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a sady Microsoft Visual Studio 2017 (libovolné edice).

> [!NOTE]
> .NET Core je Open Source multiplatformní rozhraní .NET, které implementuje specifikace rozhraní [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

K dokončení tohoto rychlého startu budete potřebovat klíč předplatného služby Speech Services. Můžete ho získat zdarma. Další informace najdete v tématu o bezplatném pokusu o [hlasové služby](get-started.md) .

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* [Sada .NET Core SDK](https://dotnet.microsoft.com/download)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Klíč předplatného Azure pro službu Speech Service. [Získejte je zdarma](get-started.md).

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-dotnetcore-create-proj.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete soubor `Program.cs` a veškerý kód nahraďte následujícím kódem.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore/helloworld/Program.cs#code)]

1. Ve stejném souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.

1. Také řetězec `YourServiceRegion` nahraďte [oblastí](regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

1. Uložte změny do projektu.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. Sestavte aplikaci. V řádku nabídek zvolte **Sestavení** > **Sestavit řešení**. Kód by se měl zkompilovat bez chyb.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Sestavit řešení](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Úspěšné sestavení")

1. Spusťte aplikaci. V řádku nabídek zvolte **Ladit** > **Spustit ladění** nebo stiskněte klávesu **F5**.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Spustit ladění](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Spuštění aplikace s laděním")

1. Zobrazí se okno konzoly a vyzve vás, abyste něco řekli. Vyslovte anglickou frázi nebo větu. Váš hlas se přenáší do služby Speech Services a přepisu na text, který se zobrazí ve stejném okně.

    ![Snímek obrazovky s výstupem konzoly po úspěšném rozpoznání](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "Výstup konzoly po úspěšném rozpoznání")

## <a name="next-steps"></a>Další postup

Další ukázky, například jak číst řeč ze zvukového souboru, jsou k dispozici na GitHubu.

> [!div class="nextstepaction"]
> [Prozkoumejte C# ukázky na GitHubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Viz také:

- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)

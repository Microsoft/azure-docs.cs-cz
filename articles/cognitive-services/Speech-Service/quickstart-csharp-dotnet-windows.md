---
title: 'Rychlý start: Rozpoznávání řeči v C# v rozhraní .NET Framework ve Windows s využitím sady Cognitive Services Speech SDK'
titleSuffix: Microsoft Cognitive Services
description: Naučte se rozpoznávat řeč v C# v rozhraní .NET Framework ve Windows s využitím sady Cognitive Services Speech SDK.
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 32b484451c4ee2264c25cca92b1d03d91b955a29
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47053992"
---
# <a name="quickstart-recognize-speech-in-c-under-net-framework-on-windows-by-using-the-speech-sdk"></a>Rychlý start: Rozpoznávání řeči v C# v rozhraní .NET Framework ve Windows s využitím sady Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku vytvoříte konzolovou aplikaci C# pro .NET Framework ve Windows s využitím sady [Speech SDK](speech-sdk.md). V reálném čase přepíšete řeč z mikrofonu počítače na text. Tato aplikace je vytvořená pomocí [balíčku NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a sady Microsoft Visual Studio 2017 (libovolné edice).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu potřebujete klíč předplatného služby Speech. Můžete ho získat zdarma. Podrobnosti najdete v článku [Vyzkoušejte si službu Speech zdarma](get-started.md).

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

[!INCLUDE [Create project ](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete soubor `Program.cs` a veškerý kód nahraďte následujícím kódem.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Ve stejném souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného služby Speech.

1. Také řetězec `YourServiceRegion` nahraďte [oblastí](regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

1. Uložte změny do projektu.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. Sestavte aplikaci. V řádku nabídek vyberte **Sestavení** > **Sestavit řešení**. Kód by se teď měl zkompilovat bez chyb.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Sestavit řešení](media/sdk/qs-csharp-dotnet-windows-08-build.png "Úspěšné sestavení")

1. Spusťte aplikaci. V řádku nabídek vyberte **Ladit** > **Spustit ladění** nebo stiskněte klávesu **F5**.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Spustit ladění](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Spuštění aplikace s laděním")

1. Zobrazí se okno konzoly a vyzve vás, abyste něco řekli. Vyslovte anglickou frázi nebo větu. Vyslovená řeč se přenese do služby Speech a přepíše na text, který se zobrazí ve stejném okně.

    ![Snímek obrazovky s výstupem konzoly po úspěšném rozpoznání](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "Výstup konzoly po úspěšném rozpoznání")

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Vyhledejte tuto ukázku ve složce `quickstart/csharp-dotnet-windows`.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Rozpoznávání záměrů z řeči pomocí sady Speech SDK pro C#](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>Viz také

- [Překlad řeči](how-to-translate-speech-csharp.md)
- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)

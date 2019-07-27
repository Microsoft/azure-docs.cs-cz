---
title: 'Rychlý start: Převod řeči, C# (.NET Framework Windows) – služba Speech'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu vytvoříte jednoduchou .NET Framework aplikaci pro zaznamenání uživatelského rozpoznávání řeči, překladu na jiný jazyk a výstup textu do příkazového řádku. Tato příručka je určená pro uživatele Windows.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: e62756ab428135ca3d5d9107f37e61813097d21f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553471"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-net-framework"></a>Rychlý start: Převod řeči pomocí sady Speech SDK pro .NET Framework

K dispozici jsou také rychlé starty pro [Převod řeči na text](quickstart-csharp-dotnet-windows.md) a [Převod textu na řeč](quickstart-text-to-speech-dotnet-windows.md).

V tomto rychlém startu vytvoříte jednoduchou .NET Framework aplikaci, která zachycuje řeč uživatele z mikrofonu vašeho počítače, převede řeč a transcribes přeložený text do příkazového řádku v reálném čase. Tato aplikace je určená ke spuštění v 64 Windows a je sestavená pomocí [balíčku NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a Microsoft Visual Studio 2017.

Úplný seznam jazyků dostupných pro překlad řeči najdete v tématu [Podpora jazyků](language-support.md).

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Klíč předplatného Azure pro službu Speech Service. [Získejte je zdarma](get-started.md).

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete soubor `Program.cs` a veškerý kód nahraďte následujícím kódem.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Ve stejném souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.

1. Také řetězec `YourServiceRegion` nahraďte [oblastí](regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

1. Uložte změny do projektu.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. Sestavte aplikaci. V řádku nabídek zvolte **Sestavení** > **Sestavit řešení**. Kód by se měl zkompilovat bez chyb.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Sestavit řešení](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Úspěšné sestavení")

1. Spusťte aplikaci. V řádku nabídek zvolte **Ladit** > **Spustit ladění** nebo stiskněte klávesu **F5**.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Spustit ladění](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Spuštění aplikace s laděním")

1. Zobrazí se okno konzoly a vyzve vás, abyste něco řekli. Vyslovte anglickou frázi nebo větu. Rozpoznávání řeči se přenáší do služby Speech, přeložená a přepisu na text, který se zobrazí ve stejném okně.

    ![Snímek obrazovky s výstupem konzoly po úspěšném překladu](media/sdk/qs-translate-csharp-dotnetcore-windows-output.png "Výstup na konzole po úspěšném překladu")

## <a name="next-steps"></a>Další postup

Na GitHubu jsou k dispozici další ukázky, jako je například čtení řeči ze zvukového souboru a výstup přeloženého textu jako syntetizované řeči.

> [!div class="nextstepaction"]
> [Prozkoumejte C# ukázky na GitHubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Viz také:

- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)

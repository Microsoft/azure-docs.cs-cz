---
title: 'Rychlý start: Rozpoznávat řeč v jazyce C# v .NET Core ve Windows pomocí Cognitive Services SDK řeči'
titleSuffix: Microsoft Cognitive Services
description: Zjistěte, jak rozpoznávat řeč v jazyce C# v .NET Core ve Windows pomocí Cognitive Services SDK řeči
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 7790920b6553ba0e2738d693710bfc3a1d3b4f89
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325097"
---
# <a name="quickstart-recognize-speech-in-c-under-net-core-on-windows-using-the-speech-sdk"></a>Rychlý start: Rozpoznávat řeč v jazyce C# v .NET Core ve Windows pomocí sady SDK pro řeč

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku se dozvíte, jak vytvořit konzolovou aplikaci C# pro .NET Core ve Windows pomocí Cognitive Services SDK řeči přepisy převod řeči na text.
Aplikace je sestavená s [Microsoft Cognitive Services řeči balíčku sady SDK NuGet](https://aka.ms/csspeech/nuget) a Microsoft Visual Studio 2017.

> [!NOTE]
> .NET core je open source, napříč platformami platforma .NET implementace [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard) specifikace.

## <a name="prerequisites"></a>Požadavky

* Klíč předplatného pro službu rozpoznávání řeči. Zobrazit [službu řeči si můžete vyzkoušet zdarma](get-started.md).
* Windows PC s pracovní mikrofon.
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/), edice Community nebo vyšší.
* **Vývoj pro různé platformy .NET Core** úlohy v sadě Visual Studio. Můžete ho povolit **nástroje** \> **stažení nástrojů a funkcí**.

  ![Povolit vývoj pro různé platformy .NET Core](media/sdk/vs-enable-net-core-workload.png)

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

1. V sadě Visual Studio 2017 vytvořte nový Visual C# Core konzolové aplikace .NET. V **nový projekt** dialogové okno, v levém podokně rozbalte **nainstalováno** \> **Visual C#** \> **.NET Core**a pak vyberte **Konzolová aplikace (.NET Core)**. Název projektu zadejte *helloworld*.

    ![Vytvořte aplikaci konzoly Visual C# (.NET Core)](media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "vytvořit Visual C# Konzolová aplikace (.NET Core)")

1. Instalaci a odkazují [balíček NuGet sady SDK pro řeč](https://aka.ms/csspeech/nuget). V Průzkumníku řešení klikněte pravým tlačítkem myši na řešení a vyberte **spravovat balíčky NuGet pro řešení**.

    ![Klikněte pravým tlačítkem na spravovat balíčky NuGet pro řešení](media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "spravovat balíčky NuGet pro řešení")

1. V pravém horním rohu v **zdroj balíčku** pole, vyberte **Nuget.org**. Vyhledejte a nainstalujte `Microsoft.CognitiveServices.Speech` balíček a nainstalujte ho do **helloworld** projektu.

    ![Nainstalujte balíček NuGet Microsoft.CognitiveServices.Speech](media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-0.5.0.png "balíček Nuget nainstalovat")

1. Přijměte licenci v zobrazeném dialogu.

    ![Přijměte licenci](media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "přijměte licenci")

## <a name="add-the-sample-code"></a>Přidejte ukázkový kód

1. Otevřít `Program.cs` v aplikaci Visual Studio projektu a veškerý kód v tomto souboru nahraďte následujícím kódem.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore-windows/helloworld/Program.cs#code)]

1. Nahraďte řetězec `YourSubscriptionKey` s klíči předplatného.

1. Nahraďte řetězec `YourServiceRegion` s [oblasti](regions.md) přidružených k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

1. Uložte změny do projektu.

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

1. Sestavení aplikace. Na panelu nabídek zvolte **sestavení** > **sestavit řešení**. Kód by měl nyní kompilovat bez chyb.

    ![Úspěšné sestavení](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "úspěšné sestavení")

1. Spusťte aplikaci. Na panelu nabídek zvolte **ladění** > **spustit ladění**, nebo stiskněte klávesu **F5**.

    ![Spusťte aplikaci do ladění](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "spusťte aplikaci do ladění")

1. Zobrazí se okno konzoly výzvou řekl něco (v angličtině). Textové rozpoznaných se pak objeví ve stejném okně.

    ![Po úspěšném rozpoznávání výstupu konzoly](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "výstupu konzoly po úspěšné rozpoznávání")

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Hledat v této ukázce `quickstart/csharp-dotnetcore-windows` složky.

## <a name="next-steps"></a>Další postup

- [Překlad řeči](how-to-translate-speech-csharp.md)
- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)

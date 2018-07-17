---
title: 'Rychlý start: Rozpoznávat řeč v jazyce C# v .NET Core ve Windows pomocí Cognitive Services SDK řeči | Dokumentace Microsoftu'
titleSuffix: Microsoft Cognitive Services
description: Zjistěte, jak rozpoznávat řeč v jazyce C# v .NET Core ve Windows pomocí Cognitive Services SDK řeči
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: f00653d0c58f52c73126693422ab0a485540dd7a
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39072560"
---
# <a name="quickstart-recognize-speech-in-c-under-net-core-on-windows-using-the-speech-sdk"></a>Rychlý start: Rozpoznávat řeč v jazyce C# v .NET Core ve Windows pomocí sady SDK pro řeč

V tomto článku se dozvíte, jak vytvořit konzolovou aplikaci C# pro .NET Core ve Windows pomocí Cognitive Services SDK řeči přepisy převod řeči na text.
Aplikace je založena na [Microsoft Cognitive Services řeči balíčku sady SDK NuGet](https://aka.ms/csspeech/nuget) a Microsoft Visual Studio 2017.

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

1. Na obrazovce licenci, která se otevře přijměte licenci:

    ![Přijměte licenci](media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "přijměte licenci")

## <a name="add-the-sample-code"></a>Přidejte ukázkový kód

1. V `Program.cs` soubor v projektu sady Visual Studio, nahraďte celý kód následujícím kódem:

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore-windows/helloworld/Program.cs#code)]

1. Nahraďte řetězec `YourSubscriptionKey` s klíči předplatného.

1. Nahraďte řetězec `YourServiceRegion` s [oblasti](regions.md) přidružených k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

1. Uložte změny do projektu.

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

1. Sestavení aplikace. V panelu nabídky vyberte **sestavení** > **sestavit řešení**. Kód by měl nyní zkompiluje bez chyb:

    ![Úspěšné sestavení](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "úspěšné sestavení")

1. Spusťte aplikaci. V panelu nabídky vyberte **ladění** > **spustit ladění**, nebo stiskněte klávesu **F5**.

    ![Spusťte aplikaci do ladění](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "spusťte aplikaci do ladění")

1. Okna konzoly se zobrazí, výzvou řekl něco (v angličtině).
Na obrazovce se zobrazí výsledky rozpoznávání.

    ![Po úspěšném rozpoznávání výstupu konzoly](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "výstupu konzoly po úspěšné rozpoznávání")

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Hledat v této ukázce `quickstart/csharp-dotnetcore-windows` složky.

## <a name="next-steps"></a>Další postup

- [Překlad řeči](how-to-translate-speech.md)
- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)

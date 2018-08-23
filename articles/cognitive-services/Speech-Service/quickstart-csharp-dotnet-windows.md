---
title: 'Rychlý start: Rozpoznávat řeč v jazyce C# v rozhraní .NET Framework na Windows s využitím Cognitive Services SDK řeči'
titleSuffix: Microsoft Cognitive Services
description: Zjistěte, jak rozpoznávat řeč v jazyce C# v rozhraní .NET Framework na Windows s využitím Cognitive Services SDK řeči
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: a625ee4d8d672ddfd42128a9dd871dcfcc013060
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "41988137"
---
# <a name="quickstart-recognize-speech-in-c-under-net-framework-on-windows-using-the-speech-sdk"></a>Rychlý start: Rozpoznávat řeč v jazyce C# v rozhraní .NET Framework ve Windows pomocí sady SDK pro řeč

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku se dozvíte, jak vytvořit konzolovou aplikaci C# pro rozhraní .NET Framework na Windows přepisy převod řeči na text pomocí Cognitive Services SDK řeči.
Aplikace je sestavená s [Microsoft Cognitive Services řeči balíčku sady SDK NuGet](https://aka.ms/csspeech/nuget) a Microsoft Visual Studio 2017.

## <a name="prerequisites"></a>Požadavky

* Klíč předplatného pro službu rozpoznávání řeči. Zobrazit [službu řeči si můžete vyzkoušet zdarma](get-started.md).
* Windows PC s pracovní mikrofon.
* Visual Studio 2017, edice Community nebo vyšší.
* **Vývoj desktopových aplikací .NET** úlohy v sadě Visual Studio. Můžete ho povolit **nástroje** \> **stažení nástrojů a funkcí**.

  ![Povolit vývoj desktopových aplikací .NET](media/sdk/vs-enable-net-desktop-workload.png)

  ![Povolit vývoj pro různé platformy .NET Core](media/sdk/vs-enable-net-desktop-workload.png)

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

1. V sadě Visual Studio 2017 vytvoření nového Visual C# konzolové aplikace. V **nový projekt** dialogové okno, v levém podokně rozbalte **nainstalováno** \> **Visual C#** \> **Windows Desktop** a pak vyberte **Konzolová aplikace (.NET Framework)**. Název projektu zadejte *helloworld*.

    ![Vytvoření aplikace Visual C# Konzolová aplikace (.NET Framework)](media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "vytvořit Visual C# Konzolová aplikace (.NET Framework)")

1. Instalaci a odkazují [balíček NuGet sady SDK pro řeč](https://aka.ms/csspeech/nuget). V Průzkumníku řešení klikněte pravým tlačítkem myši na řešení a vyberte **spravovat balíčky NuGet pro řešení**.

    ![Klikněte pravým tlačítkem na spravovat balíčky NuGet pro řešení](media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "spravovat balíčky NuGet pro řešení")

1. V pravém horním rohu v **zdroj balíčku** pole, vyberte **Nuget.org**. Hledat `Microsoft.CognitiveServices.Speech` balíček a nainstalujte ho do **helloworld** projektu.

    ![Nainstalujte balíček NuGet Microsoft.CognitiveServices.Speech](media/sdk/qs-csharp-dotnet-windows-03-nuget-install-0.5.0.png "balíček Nuget nainstalovat")

1. Přijměte licenci na tento zobrazené.

    ![Přijměte licenci](media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "přijměte licenci")

1. Následující řádek výstupu se zobrazí v konzole Správce balíčků.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 0.6.0' to helloworld
   ```

## <a name="create-a-platform-configuration-matching-your-pc-architecture"></a>Vytvořit konfiguraci platformy tak odpovídající vaší architektury PC

V této části přidáte novou platformu na konfiguraci, která odpovídá architektuře procesoru.

1. Spuštění nástroje Configuration Manager. Vyberte **sestavení** > **nástroje Configuration Manager**.

    ![Spusťte Správce konfigurace](media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "spuštění nástroje configuration manager")

1. V **nástroje Configuration Manager** dialogovém okně Přidat novou platformu. Z **platformou aktivního řešení** rozevíracího seznamu vyberte **nový**.

    ![Přidat novou platformu pod oknem configuration Manageru](media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "přidat novou platformu pod oknem configuration Manageru")

1. Pokud používáte systém Windows 64-bit, vytvořte novou konfiguraci platformy tak s názvem `x64`. Pokud používáte systém Windows 32-bit, vytvořit novou konfiguraci platformy s názvem `x86`. V tomto článku vytvoříte `x64` konfiguraci platformy.

    ![Na Windows 64-bit, přidejte novou platformu s názvem "x64"](media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "přidat x64 platformy")

## <a name="add-the-sample-code"></a>Přidejte ukázkový kód

1. Otevřít `Program.cs` a nahraďte kód v ji následujícím kódem.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Nahraďte řetězec `YourSubscriptionKey` s klíči předplatného.

1. Nahraďte řetězec `YourServiceRegion` s [oblasti](regions.md) přidružených k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

1. Uložte změny do projektu.

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

1. Sestavení aplikace. V panelu nabídky vyberte **sestavení** > **sestavit řešení**. Kód by měl nyní kompilovat bez chyb.

    ![Úspěšné sestavení](media/sdk/qs-csharp-dotnet-windows-08-build.png "úspěšné sestavení")

1. Spusťte aplikaci. V panelu nabídky vyberte **ladění** > **spustit ladění**, nebo stiskněte klávesu **F5**.

    ![Spusťte aplikaci do ladění](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "spusťte aplikaci do ladění")

1. Zobrazí se okno konzoly výzvou řekl něco (v angličtině). Textové rozpoznaných se pak objeví ve stejném okně.

    ![Po úspěšném rozpoznávání výstupu konzoly](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "výstupu konzoly po úspěšné rozpoznávání")

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Hledat v této ukázce `quickstart/csharp-dotnet-windows` složky.

## <a name="next-steps"></a>Další postup

- [Překlad řeči](how-to-translate-speech-csharp.md)
- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)

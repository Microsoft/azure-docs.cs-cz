---
title: 'Rychlý úvod: Rozpoznávat řeč pomocí kognitivní služby řeči C# SDK pro Windows | Microsoft Docs'
description: Zjistěte, jak rozpoznávat řeč pomocí sady SDK jazyka C# pro rozpoznávání řeči služby.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: 14e5110385667d0f2135251eef53ff20ada08444
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37111248"
---
# <a name="quickstart-recognize-speech-using-the-cognitive-services-speech-c-sdk"></a>Rychlý úvod: Rozpoznávat řeč pomocí kognitivní služby řeči C# sady SDK

V tomto článku zjistěte, jak k vytvoření konzolové aplikace jazyka C# v systému Windows pomocí kognitivní Services SDK řeči transcribe řeči na text.

## <a name="prerequisites"></a>Požadavky

* Klíč předplatného pro službu řeči. V tématu [vyzkoušení služby řeči zdarma](get-started.md).
* Visual Studio 2017, Community Edition nebo vyšší.
* **Vývoj aplikací .NET** úloh v sadě Visual Studio. Můžete povolit v **nástroje** \> **funkcí a nástrojů pro získání**. 

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

1. Ve Visual Studio 2017 vytvořte nové Visual C# aplikaci konzoly. V **nový projekt** dialogové okno, v levém podokně rozbalte **nainstalovaná** a pak vyberte **konzolovou aplikaci (rozhraní .NET Framework)**. Název projektu zadejte *CsharpHelloSpeech*.

    ![Vytvoření aplikace konzoly Visual C# (rozhraní .NET Framework)](media/sdk/speechsdk-05-vs-cs-new-console-app.png "Vytvořte konzolovou aplikaci Visual C#")

2. Instalace a odkazovat [balíček NuGet sady SDK řeči](https://aka.ms/csspeech/nuget). V Průzkumníku řešení klikněte pravým tlačítkem na řešení a vyberte **spravovat balíčky NuGet pro řešení**.

    ![Pravým tlačítkem klikněte na spravovat balíčky NuGet pro řešení](media/sdk/speechsdk-06-vs-cs-manage-nuget-packages.png "spravovat balíčky NuGet pro řešení")

3. V pravém horním rohu v **zdroj balíčku** pole, vyberte **Nuget.org**. Vyhledat a nainstalovat `Microsoft.CognitiveServices.Speech` balíček a nainstalujte ho do **CsharpHelloSpeech** projektu.

    ![Nainstalujte balíček NuGet Microsoft.CognitiveServices.Speech](media/sdk/speechsdk-08-vs-cs-nuget-install.png "Nuget nainstalujte balíček")

4. Na obrazovce licenci, která se objeví přijměte tyto licenční:

    ![Přijmout tyto licenční](media/sdk/speechsdk-09-vs-cs-nuget-license.png "přijmout tyto licenční")

## <a name="create-a-platform-configuration-matching-your-pc-architecture"></a>Vytvoření konfigurace platformy odpovídající vaší architektury počítačů

V této části přidáte nová platforma konfiguraci, která odpovídá architektuře procesoru.

1. Spusťte Správce konfigurace. Vyberte **sestavení** > **nástroje Configuration Manager**.

    ![Spusťte Správce konfigurace](media/sdk/speechsdk-12-vs-cs-cfg-manager-click.png "spuštění nástroje configuration manager")

2. V **nástroje Configuration Manager** dialogové okno pole, přidejte nová platforma. Z **platforma Active řešení** rozevíracího seznamu vyberte **nový**.

    ![Přidat nová platforma pod okno nástroje configuration manager](media/sdk/speechsdk-14-vs-cs-cfg-manager-new.png "přidat nová platforma pod okno nástroje configuration manager")

3. Pokud používáte Windows 64-bit, vytvořte novou konfiguraci platformy s názvem `x64`. Pokud používáte 32bitový systém Windows, vytvořte novou konfiguraci platformy s názvem `x86`. V tomto článku vytvoříte `x64` konfiguraci platform. 

    ![Na 64bitovém systému Windows, přidejte nová platforma s názvem "x64"](media/sdk/speechsdk-15-vs-cs-cfg-manager-add-x64.png "přidat x64 platformy")

## <a name="add-the-sample-code"></a>Přidejte ukázkový kód

1. V `Program.cs` souboru v projektu sady Visual Studio, nahraďte text `Program` třídy následujícím kódem. Zkontrolujte, zda nahradit vlastní klíč předplatného a změnit [oblast](regions.md) na ten, který má přidruženou k odběru (například `westus` pro bezplatné předplatné zkušební verze).

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-csharp/Program.cs#code)]

2. Po vložení kódu, `Main()` metoda musí vypadat, jak je znázorněno na následujícím snímku obrazovky:

    ![Hlavní metoda po vložení kódu](media/sdk/speechsdk-17-vs-cs-paste-code.png "konečné Main – metoda")

3. Visual Studio IntelliSense označuje odkazy na sadu SDK řeči třídy, které nebylo možné přeložit. Chcete-li tuto chybu opravit, přidejte následující `using` příkaz na začátek kódu (buď ručně, nebo pomocí sady Visual Studio [rychlé akce](https://docs.microsoft.com/visualstudio/ide/quick-actions)).

    [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-csharp/Program.cs#usingstatement)]

    ![Přidejte chybějící pomocí rychlé akce pomocí příkazu](media/sdk/speechsdk-18-vs-cs-add-using.png "problémy vyřešte IntelliSense")

4. Ujistěte se, že IntelliSense zvýraznění vyřešen a uložit změny do projektu.

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

1. Sestavení aplikace. V řádku nabídek vyberte **sestavení** > **sestavit řešení**. Kód by měl kompilovat bez chyb nyní:

    ![Úspěšné sestavení](media/sdk/speechsdk-20-vs-cs-build.png "úspěšném sestavení")

2. Spusťte aplikaci. V řádku nabídek vyberte **ladění** > **spustit ladění**, nebo stiskněte klávesu **F5**. 

    ![Spusťte aplikaci do ladění](media/sdk/speechsdk-21-vs-cs-f5.png "spusťte aplikaci do ladění")

3. Do okna konzoly se zobrazí, vás vyzve k vyslovení něco (v angličtině).
Výsledek rozpoznávání se zobrazí na obrazovce.

    ![Výstup konzoly po úspěšné rozpoznávání](media/sdk/speechsdk-22-cs-vs-console-output.png "konzole výstup po úspěšné rozpoznávání")

## <a name="download-code"></a>Stáhněte si kód

Nejnovější sadu vzorků, najdete v článku [úložiště GitHub SDK ukázka kognitivní služby řeči](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Další postup

- [Převede řeči](how-to-translate-speech.md)
- [Přizpůsobení akustickými modely](how-to-customize-acoustic-models.md)
- [Přizpůsobit jazyk modely](how-to-customize-language-model.md)

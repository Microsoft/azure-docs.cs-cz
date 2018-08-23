---
title: 'Rychlý start: Rozpoznávat řeč v jazyce C++ na ploše Windows pomocí Cognitive Services SDK řeči'
titleSuffix: Microsoft Cognitive Services
description: Zjistěte, jak rozpoznávat řeč v jazyce C++ na ploše Windows pomocí Cognitive Services SDK řeči
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 59c70d33d537742a5918dc1ba55ab8ac1f84e12c
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "41987558"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-desktop-using-the-speech-sdk"></a>Rychlý start: Rozpoznávat řeč v jazyce C++ na ploše Windows pomocí sady SDK pro řeč

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Zjistíte, jak vytvořit založených na C++ konzolovou aplikaci pro Windows Desktop, který využívá sadou SDK pro řeč.
Aplikace je založena na [Microsoft Cognitive Services řeči balíčku sady SDK NuGet](https://aka.ms/csspeech/nuget) a Microsoft Visual Studio 2017.

## <a name="prerequisites"></a>Požadavky

* Klíč předplatného pro službu rozpoznávání řeči. Zobrazit [službu řeči si můžete vyzkoušet zdarma](get-started.md).
* Windows PC s pracovní mikrofon.
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/), edice Community nebo vyšší.
* **Vývoj desktopových aplikací pomocí C++** úlohy v sadě Visual Studio a **Správce balíčků NuGet** komponenty v sadě Visual Studio.
  Můžete povolit v **nástroje** \> **stažení nástrojů a funkcí**v části **úlohy** a **jednotlivé komponenty** karty , v uvedeném pořadí:

  ![Povolit vývoj desktopových aplikací pomocí úlohy pro C++](media/sdk/vs-enable-cpp-workload.png)

  ![Povolit správce balíčků NuGet v sadě Visual Studio ](media/sdk/vs-enable-nuget-package-manager.png)

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

V sadě Visual Studio 2017 vytvořte novou aplikaci Visual C++ Windows Desktop Windows konzolovou aplikaci. V **nový projekt** dialogové okno, v levém podokně rozbalte **nainstalováno** \> **Visual C++** \> **Windows Desktop** a pak vyberte **Konzolová aplikace Windows**. Název projektu zadejte *helloworld*.

![Vytvoření konzolové aplikace klasické pracovní plochy Windows Visual C++ Windows](media/sdk/qs-cpp-windows-01-new-console-app.png)

Pokud máte spuštěný na instalaci Windows 64-bit, Volitelně můžete přepnout platforma sestavení k `x64`:

![Přepnout na x64 platforma sestavení](media/sdk/qs-cpp-windows-02-switch-to-x64.png)

## <a name="install-and-reference-the-speech-sdk-nuget-package"></a>Instalace a odkázat na balíček NuGet sady SDK pro řeč

V Průzkumníku řešení klikněte pravým tlačítkem myši na řešení a klikněte na **spravovat balíčky NuGet pro řešení**.

![Klikněte pravým tlačítkem na spravovat balíčky NuGet pro řešení](media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

V pravém horním rohu v **zdroj balíčku** zvolte "Nuget.org".
Z **Procházet** kartu, vyhledejte balíček "Microsoft.CognitiveServices.Speech", vyberte ho a zkontrolujte **projektu** a **helloworld** na pravým tlačítkem a vyberte políčka **Nainstalovat** k její instalaci do projektu helloworld.

> [!NOTE]
> Aktuální verze sady SDK pro řeč Cognitive Services je `0.6.0`.

![Nainstalujte balíček NuGet Microsoft.CognitiveServices.Speech](media/sdk/qs-cpp-windows-04-nuget-install-0.5.0.png)

Na obrazovce licenci, která se otevře přijměte licenci:

![Přijměte licenci](media/sdk/qs-cpp-windows-05-nuget-license.png)

## <a name="add-the-sample-code"></a>Přidejte ukázkový kód

1. Vaše výchozí počáteční kód nahraďte tímto:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. Nahraďte řetězec `YourSubscriptionKey` s klíči předplatného.

1. Nahraďte řetězec `YourServiceRegion` s [oblasti](regions.md) přidružených k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

1. Uložte změny do projektu.

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

1. Sestavení aplikace. V panelu nabídky vyberte **sestavení** > **sestavit řešení**. Kód by měl nyní zkompiluje bez chyb:

   ![Úspěšné sestavení](media/sdk/qs-cpp-windows-06-build.png)

1. Spusťte aplikaci. V panelu nabídky vyberte **ladění** > **spustit ladění**, nebo stiskněte klávesu **F5**.

   ![Spusťte aplikaci do ladění](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Okna konzoly se zobrazí, výzvou řekl něco (v angličtině).
   Výsledek rozpoznávání zobrazí na obrazovce.

   ![Výstup na konzole po úspěšné rozpoznávání](media/sdk/qs-cpp-windows-08-console-output-release.png)

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Hledat v této ukázce `quickstart/cpp-windows` složky.

## <a name="next-steps"></a>Další postup

* [Získat naše ukázky](speech-sdk.md#get-the-samples)

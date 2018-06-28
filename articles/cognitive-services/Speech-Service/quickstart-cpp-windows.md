---
title: Rychlý start SDK řeči pro C++ a Windows | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Get informace a ukázky kódu můžete rychle začít používat sadu SDK řeči s Windows a C++ v kognitivní služby.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 0bcdc3c4357cb8985fad16c607957bffad4a2b8c
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049226"
---
# <a name="quickstart-for-c-and-windows"></a>Rychlý start pro C++ a Windows

Aktuální verze sady SDK kognitivní řeči služby je `0.4.0`.

Jsme popisují, jak vytvořit na základě C++ konzolové aplikace pro Windows Desktop, který využívá řeči SDK.
Aplikace je založena na [balíček NuGet sady SDK kognitivní služby Microsoft](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech) a Microsoft Visual Studio 2017.

> [!NOTE]
> Pokud hledáte rychlý start pro C++ a Linux, přejděte [zde](quickstart-cpp-linux.md).<br>
> Pokud hledáte rychlý start pro C# a systému Windows, přejděte [zde](quickstart-csharp-windows.md).

> [!NOTE]
> Tento rychlý start vyžaduje počítač s mikrofon práci.<br>
> Ukázku, který rozpoznává řeči z daného zvuk vstupní soubor najdete [ukázka](speech-to-text-sample.md#speech-recognition-from-a-file).

> [!NOTE]
> Ujistěte se, že zahrnuje instalace Visual Studia **vývoj aplikací s jazykem C++** zatížení.
> Pokud si nejste jistí, pomocí těchto kroků zkontrolujte a opravte: V aplikaci Visual Studio 2017, vyberte **nástroje** \> **funkcí a nástrojů pro získání** a potvrdit výzvy řízení uživatelských účtů výběrem **Ano**.
> V **úlohy** kartě, pokud **vývoj aplikací s jazykem C++** neobsahovala sadu zaškrtávací políčko vedle sebe, nastavte ho a klikněte na **upravit** uložte změny.

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

## <a name="creating-an-empty-console-application-project"></a>Vytvoření projektu aplikace prázdné konzole

V aplikaci Visual Studio 2017 vytvořte novou Visual C++ Windows Desktop Windows konzolovou aplikaci s názvem "CppHelloSpeech":

![Vytvoření konzolové aplikace pro Desktop Windows Visual C++ Windows](media/sdk/speechsdk-05-vs-cpp-new-console-app.png)

Pokud spouštíte na 64bitové instalaci Windows, volitelně přepínač vaši platformu sestavení do `x64`:

![Přepnout na x64 platformy sestavení](media/sdk/speechsdk-07-vs-cpp-switch-to-x64.png)

## <a name="install-and-reference-the-speech-sdk-nuget-package"></a>Instalaci a odkazují na balíček NuGet sady SDK pro rozpoznávání řeči

> [!NOTE]
> Zkontrolujte, zda že je povoleno Správce balíčků NuGet pro Visual Studio 2017 instalaci.
> V aplikaci Visual Studio 2017, vyberte **nástroje** \> **funkcí a nástrojů pro získání** a potvrdit výzvy řízení uživatelských účtů výběrem **Ano**. Vyberte **jednotlivých součástí** kartě a vyhledejte **Správce balíčků NuGet** pod **Code nástroje**.
> Pokud políčko na levé straně, není nastavena, je třeba ho nastavit a klikněte na **upravit** uložte změny.
>
> ![Povolit správce balíčků NuGet v sadě Visual Studio ](media/sdk/speechsdk-05-vs-enable-nuget-package-manager.png)

V Průzkumníku řešení klikněte pravým tlačítkem na řešení a klikněte na **spravovat balíčky NuGet pro řešení**.

![Pravým tlačítkem klikněte na spravovat balíčky NuGet pro řešení](media/sdk/speechsdk-09-vs-cpp-manage-nuget-packages.png)

V pravém horním rohu v **zdroj balíčku** pole, zvolte "Nuget.org".
Z **Procházet** kartě, vyhledejte "Microsoft.CognitiveServices.Speech" balíčku, vyberte ho a zkontrolujte **projektu** a **CppHelloSpeech** polí na pravé straně, a Vyberte **nainstalovat** k její instalaci do projektu CppHelloSpeech.

![Nainstalujte balíček NuGet Microsoft.CognitiveServices.Speech](media/sdk/speechsdk-11-vs-cpp-manage-nuget-install.png)

Na obrazovce licenci, která se objeví přijměte tyto licenční:

![Přijmout tyto licenční](media/sdk/speechsdk-12-vs-cpp-manage-nuget-license.png)

## <a name="add-the-sample-code"></a>Přidejte ukázkový kód

Nahradíte výchozí úvodní kód následující:

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-cpp/CppHelloSpeech.cpp#code)]

> [!IMPORTANT]
> Nahraďte klíč předplatného ten, který jste získali. <br>
> Nahraďte vaší oblasti z oblasti [rozhraní API REST služby řeči](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis), například nahradit 'westus'.

![Přidejte svůj klíč předplatného](media/sdk/sub-key-recognize-speech-cpp.png)

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

Kód by měl kompilovat bez chyb nyní:

![Úspěšné sestavení](media/sdk/speechsdk-16-vs-cpp-build.png)

Spusťte program v ladicím programu s tlačítko spustit nebo pomocí klávesové zkratky F5:

![Spusťte aplikaci do ladění](media/sdk/speechsdk-17-vs-cpp-f5.png)

Okno konzoly měl by se zobrazit, vás vyzve k vyslovení něco (v angličtině).
Výsledek rozpoznávání se zobrazí na obrazovce.

![Výstup konzoly po úspěšné rozpoznávání](media/sdk/speechsdk-18-vs-cpp-console-output-release.png)

## <a name="downloading-the-sample"></a>Stažení ukázky

Nejnovější sadu vzorků, najdete v článku [úložiště GitHub SDK ukázka kognitivní služby řeči](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Další postup

* Přejděte [ukázky stránky](samples.md) Další ukázky.

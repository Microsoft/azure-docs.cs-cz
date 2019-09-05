---
title: 'Rychlý start: Rozpoznávání řeči, C# (UWP) – služba Speech'
titleSuffix: Azure Cognitive Services
description: V tomto článku vytvoříte C# aplikace univerzální platformy Windows (UPW) s využitím Cognitive Services SDK řeči. V reálném čase přepíšete řeč z mikrofonu zařízení na text. Aplikace je sestavená pomocí balíčku NuGet sady Speech SDK a Microsoft Visual Studio 2019.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: fe5ff376a7895e2ca5246c0b9eb575752b07c7a1
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382283"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Rychlý start: Rozpoznávání řeči v aplikaci UWP pomocí sady Speech SDK

K dispozici jsou také rychlé starty pro [syntézu řeči](quickstart-text-to-speech-csharp-uwp.md), [Překlad řeči a funkce](quickstart-translate-speech-uwp.md)pro [virtuální asistenty hlasu First](quickstart-virtual-assistant-csharp-uwp.md).

Pokud chcete, vyberte jiný programovací jazyk nebo prostředí:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku vyvíjíte aplikaci C# Univerzální platforma Windows (UWP) pomocí sady Cognitive Services [Speech SDK](speech-sdk.md). Program transcribes hlas do textu v reálném čase z mikrofonu vašeho zařízení. Aplikace je sestavená pomocí [balíčku NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a Microsoft Visual Studio 2019 (libovolná edice).

> [!NOTE]
> Univerzální platforma Windows umožňuje vyvíjet aplikace, které běží na všech zařízeních podporujících Windows 10 včetně počítačů, Xboxu, Surface Hubu a dalších.

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Klíč předplatného Azure pro službu Speech Service. [Získejte je zdarma](get-started.md).

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

Nyní přidejte kód jazyka XAML, který definuje uživatelské rozhraní aplikace, a přidejte implementaci kódu na C# pozadí.

1. V **Průzkumník řešení**otevřete `MainPage.xaml`.

1. V zobrazení jazyka XAML návrháře vložte následující fragment kódu XAML do značky **Grid** (mezi `<Grid>` a `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. V **Průzkumník řešení**otevřete zdrojový soubor `MainPage.xaml.cs`kódu na pozadí. (Je seskupena pod `MainPage.xaml`.)

1. Nahraďte veškerý kód v něm následujícím fragmentem kódu:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. V `SpeechRecognitionFromMicrophone_ButtonClicked` obslužné rutině zdrojového souboru vyhledejte řetězec `YourSubscriptionKey`a nahraďte ho klíčem předplatného.

1. V obslužné rutině Najděte řetězec `YourServiceRegion`a nahraďte ho oblastí, která je přidružená k vašemu předplatnému. [](regions.md) `SpeechRecognitionFromMicrophone_ButtonClicked` (Například použijte `westus` pro předplatné bezplatné zkušební verze.)

1. V řádku **nabídek výběrem možnosti** > **Uložit vše** uložte změny.

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

Teď jste připraveni sestavit a otestovat svoji aplikaci.

1. V řádku nabídek vyberte **sestavení** > sestavit**řešení** a sestavte aplikaci. Kód by se teď měl zkompilovat bez chyb.

1. Zvolte **ladění** > **Spustit ladění** (nebo stiskněte klávesu **F5**) a spusťte aplikaci. Zobrazí se okno **HelloWorld** .

   ![Ukázková aplikace pro rozpoznávání řeči C# UWP v rychlém startu](media/sdk/qs-csharp-uwp-helloworld-window.png)

1. Vyberte možnost **Povolit mikrofon**a když se zobrazí žádost o přístupové oprávnění, vyberte **Ano**.

   ![Žádost o oprávnění k přístupu k mikrofonu](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Vyberte **Rozpoznávání řeči se vstupem z mikrofonu** a vyslovte do mikrofonu zařízení anglickou frázi nebo větu. Váš hlas se přenáší do služby Speech Services a přepisu na text, který se zobrazí v okně.

   ![Uživatelské rozhraní rozpoznávání řeči](media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte C# ukázky na GitHubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Viz také:

- [Rychlé zprovoznění: Převod řeči pomocí sady Speech SDK pro C# (UWP)](quickstart-translate-speech-uwp.md)
- [Výuka modelu pro Custom Speech](how-to-custom-speech-train-model.md)

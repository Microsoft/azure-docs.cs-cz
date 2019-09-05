---
title: 'Rychlý start: Syntetizace řeči, C# (UWP) – služba pro rozpoznávání řeči'
titleSuffix: Azure Cognitive Services
description: V tomto článku vytvoříte C# aplikace univerzální platformy Windows (UPW) s využitím Cognitive Services SDK řeči. Rozpoznávání řeči textu v reálném čase pro mluvčí zařízení. Aplikace je sestavená pomocí balíčku NuGet sady Speech SDK a Microsoft Visual Studio 2019.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: yinhew
ms.openlocfilehash: 65b65c9af377b6a9951f9f328e0732850d3b9c1d
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382230"
---
# <a name="quickstart-synthesize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Rychlý start: Rozpoznávání řeči v aplikaci pro UWP pomocí sady Speech SDK

K dispozici jsou také rychlé starty pro [rozpoznávání řeči](quickstart-csharp-uwp.md), [Překlad řeči a funkce](quickstart-translate-speech-uwp.md)pro [virtuální asistenty hlasu First](quickstart-virtual-assistant-csharp-uwp.md).

V tomto článku vyvíjíte aplikaci C# Univerzální platforma Windows (UWP) pomocí sady Cognitive Services [Speech SDK](speech-sdk.md). Program z textu v reálném čase vysyntetizuje řeč od svého mluvčího zařízení. Sestavíte aplikaci pomocí [balíčku NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a Microsoft Visual Studio 2019 (libovolná edice).

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

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. V **Průzkumník řešení**otevřete zdrojový soubor `MainPage.xaml.cs`kódu na pozadí. (Je seskupena pod `MainPage.xaml`.)

1. Nahraďte veškerý kód v něm následujícím fragmentem kódu:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. V `Speak_ButtonClicked` obslužné rutině zdrojového souboru vyhledejte řetězec `YourSubscriptionKey`a nahraďte ho klíčem předplatného.

1. V obslužné rutině Najděte řetězec `YourServiceRegion`a nahraďte ho oblastí, která je přidružená k vašemu předplatnému. [](regions.md) `Speak_ButtonClicked` (Například použijte `westus` pro předplatné bezplatné zkušební verze.)

1. V řádku **nabídek výběrem možnosti** > **Uložit vše** uložte změny.

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

Teď jste připraveni sestavit a otestovat svoji aplikaci.

1. V řádku nabídek vyberte **sestavení** > sestavit**řešení** a sestavte aplikaci. Kód by se teď měl zkompilovat bez chyb.

1. Zvolte **ladění** > **Spustit ladění** (nebo stiskněte klávesu **F5**) a spusťte aplikaci. Zobrazí se okno **HelloWorld** .

   ![Ukázková aplikace pro rozpoznávání řeči C# pro UWP v rychlém startu](media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. Do textového pole zadejte nějaký text a klikněte na **mluvit**. Váš text se přenáší do služeb řeči a syntetizuje se na řeč, který hraje na reproduktoru.

    ![Uživatelské rozhraní syntézy řeči](media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte C# ukázky na GitHubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Viz také:

- [Vytváření a používání vlastních hlasových modelů](how-to-custom-voice-create-voice.md)
- [Záznam ukázek hlasu](record-custom-voice-samples.md)

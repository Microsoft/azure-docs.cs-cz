---
title: 'Rychlý start: Syntetizace řeči, C# (UWP) – služba pro rozpoznávání řeči'
titleSuffix: Azure Cognitive Services
description: V tomto článku vytvoříte C# aplikace univerzální platformy Windows (UPW) s využitím Cognitive Services SDK řeči. Rozpoznávání řeči textu v reálném čase pro mluvčí zařízení. Aplikace je sestavená pomocí balíčku NuGet sady SDK pro řeč a Microsoft Visual Studio 2017.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: 5e1f8aea1b00cbba7fec6c7ca416a965458ab526
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607754"
---
# <a name="quickstart-synthesize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Rychlý start: Rozpoznávání řeči v aplikaci pro UWP pomocí sady Speech SDK

K dispozici jsou také rychlé starty pro [rozpoznávání řeči](quickstart-csharp-uwp.md), [Převod řeči](quickstart-translate-speech-uwp.md) a [hlasově prvního virtuálního pomocníka](quickstart-virtual-assistant-csharp-uwp.md).

V tomto článku vyvíjíte C# Univerzální platforma Windows (UWP; Aplikace Windows verze 1709 novější) pomocí [sady Cognitive Services Speech SDK](speech-sdk.md). Program bude z textu v reálném čase pro mluvčí zařízení syntetizovat řeč. Aplikace je sestavená pomocí [balíčku NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a Microsoft Visual Studio 2017 nebo novější (libovolná edice).

> [!NOTE]
> Univerzální platforma Windows umožňuje vyvíjet aplikace, které běží na všech zařízeních podporujících Windows 10 včetně počítačů, Xboxu, Surface Hubu a dalších.

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) nebo novější
* Klíč předplatného Azure pro službu Speech Service. [Získejte je zdarma](get-started.md).

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Uživatelské rozhraní aplikace je definované pomocí XAML. Otevřete `MainPage.xaml` v Průzkumníku řešení. V zobrazení XAML návrháře vložte do značky Grid (mezi `<Grid>` a `</Grid>`) následující fragment kódu XAML.

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Otevřete zdrojový soubor s kódem `MainPage.xaml.cs` (je seskupený pod `MainPage.xaml`). Veškerý kód nahraďte následujícím kódem.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. V obslužné rutině `Speak_ButtonClicked` v tomto souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.

1. V obslužné rutině `Speak_ButtonClicked` nahraďte řetězec `YourServiceRegion` [oblastí](regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

1. Uložte všechny změny do projektu.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. Sestavte aplikaci. V řádku nabídek vyberte **Sestavení** > **Sestavit řešení**. Kód by se teď měl zkompilovat bez chyb.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Sestavit řešení](media/sdk/qs-csharp-uwp-08-build.png "Úspěšné sestavení")

1. Spusťte aplikaci. V řádku nabídek vyberte **Ladit** > **Spustit ladění** nebo stiskněte klávesu **F5**.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Spustit ladění](media/sdk/qs-csharp-uwp-09-start-debugging.png "Spuštění aplikace s laděním")

1. Do textového pole zadejte nějaký text a klikněte na **mluvit**. Váš text se přenáší do služeb řeči a syntetizuje se na řeč, který hraje na reproduktoru.

    ![Snímek uživatelského rozhraní syntézy řeči](media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte C# ukázky na GitHubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Viz také:

- [Přizpůsobení hlasových písem](how-to-customize-voice-font.md)
- [Záznam ukázek hlasu](record-custom-voice-samples.md)

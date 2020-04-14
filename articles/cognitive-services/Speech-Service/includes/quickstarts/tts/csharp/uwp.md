---
title: 'Úvodní příručka: Syntetizovat řeč, C# (UPW) - služba řeči'
titleSuffix: Azure Cognitive Services
description: V tomto článku vytvoříte aplikaci pro Univerzální platformu Windows (UPW) v jazyce C# pomocí sady Cognitive Services Speech SDK. Syntetizujete řeč z textu v reálném čase do reproduktoru vašeho zařízení. Aplikace je vytvořena pomocí balíčku Speech SDK NuGet package a Microsoft Visual Studio 2019.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 89fc5fddffb291942b8f3a4db3dfdf4ccd6cf46a
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81275113"
---
> [!NOTE]
> Univerzální platforma Windows umožňuje vyvíjet aplikace, které běží na všech zařízeních podporujících Windows 10 včetně počítačů, Xboxu, Surface Hubu a dalších.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

> [!div class="checklist"]
> * [Vytvoření řečového prostředku Azure](../../../../get-started.md)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=uwp&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>Přidání ukázkového kódu

Nyní přidejte kód XAML, který definuje uživatelské rozhraní aplikace, a přidejte implementaci kódu C#.

1. V **Průzkumníku** `MainPage.xaml`řešení otevřete .

1. V zobrazení XAML návrháře vložte do značky **Mřížka** následující úrych XAML (mezi `<Grid>` a `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml#StackPanel)]

1. V **Průzkumníku řešení**otevřete zdrojový `MainPage.xaml.cs`soubor s kódem na pozadí . (Je seskupena pod `MainPage.xaml`.)

1. Nahraďte veškerý kód v něm následujícím fragmentem:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml.cs#code)]

1. Ve obslužné rutině `Speak_ButtonClicked` `YourSubscriptionKey`zdrojového souboru najděte řetězec a nahraďte ho klíčem předplatného.

1. V `Speak_ButtonClicked` obslužné `YourServiceRegion`rutině najděte řetězec a nahraďte ho [oblastí](~/articles/cognitive-services/Speech-Service/regions.md) přidruženou k vašemu předplatnému. (Použijte například `westus` bezplatné zkušební předplatné.)

1. Na řádku nabídek zvolte **Uložit** > **vše,** chcete-li uložit změny.

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

Nyní jste připraveni k sestavení a testování aplikace.

1. Na řádku nabídek zvolte **Sestavení** > **sestavení řešení** k sestavení aplikace. Kód by se teď měl zkompilovat bez chyb.

1. **Chcete-li** > spustit aplikaci, zvolte Ladění**spouštění ladění** (nebo stisknutím **klávesy F5).** Zobrazí se okno **Helloworld.**

   ![Ukázka aplikace pro syntézu řeči UWP v c# - rychlý start](~/articles/cognitive-services/Speech-Service/media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. Zadejte nějaký text do textového pole a klepněte na **tlačítko Přečíst**. Text je přenášen do služby Řeč a syntetizován do řeči, která se přehrává na reproduktoru.

    ![Uživatelské rozhraní pro syntézu řeči](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Viz také

- [Vytvoření vlastního hlasu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Záznam vlastních hlasových ukázek](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)

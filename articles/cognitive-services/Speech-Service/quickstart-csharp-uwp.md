---
title: 'Rychlý start: Rozpoznávání řeči, C# (UWP) – služba Speech'
titleSuffix: Azure Cognitive Services
description: V tomto článku vytvoříte C# aplikace univerzální platformy Windows (UPW) s využitím Cognitive Services SDK řeči. V reálném čase přepíšete řeč z mikrofonu zařízení na text. Aplikace je sestavená pomocí balíčku NuGet sady SDK pro řeč a Microsoft Visual Studio 2017.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/23/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 5ea75f1956b70b6bfebebcf29e27542eba0ca0cf
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839951"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Rychlý start: Rozpoznávání řeči v aplikaci UWP pomocí sady Speech SDK

K dispozici jsou také rychlé starty pro převod [textu na řeč](quickstart-text-to-speech-csharp-uwp.md), [rozpoznávání řeči](quickstart-translate-speech-uwp.md) a [hlasového prvního virtuálního pomocníka](quickstart-virtual-assistant-csharp-uwp.md).

V případě potřeby vyberte jiný programovací jazyk nebo prostředí:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku vyvíjíte C# Univerzální platforma Windows (UWP; Aplikace Windows verze 1709 novější) pomocí [sady Cognitive Services Speech SDK](speech-sdk.md). Z mikrofonu vašeho zařízení bude program přepisovat řeč v reálném čase na text. Aplikace je sestavená pomocí [balíčku NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a Microsoft Visual Studio 2017 nebo novější (libovolná edice).

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

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Otevřete zdrojový soubor s kódem `MainPage.xaml.cs` (je seskupený pod `MainPage.xaml`). Veškerý kód nahraďte následujícím kódem.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. V obslužné rutině `SpeechRecognitionFromMicrophone_ButtonClicked` v tomto souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.

1. V obslužné rutině `SpeechRecognitionFromMicrophone_ButtonClicked` nahraďte řetězec `YourServiceRegion` [oblastí](regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

1. Uložte všechny změny do projektu.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. Sestavte aplikaci. V řádku nabídek vyberte **Sestavení** > **Sestavit řešení**. Kód by se teď měl zkompilovat bez chyb.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Sestavit řešení](media/sdk/qs-csharp-uwp-08-build.png "Úspěšné sestavení")

1. Spusťte aplikaci. V řádku nabídek vyberte **Ladit** > **Spustit ladění** nebo stiskněte klávesu **F5**.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Spustit ladění](media/sdk/qs-csharp-uwp-09-start-debugging.png "Spuštění aplikace s laděním")

1. Otevře se okno. Vyberte **Povolit mikrofon** a potvrďte žádost o oprávnění, která se otevře.

    ![Snímek obrazovky s žádostí o oprávnění](media/sdk/qs-csharp-uwp-10-access-prompt.png "Spuštění aplikace s laděním")

1. Vyberte **Rozpoznávání řeči se vstupem z mikrofonu** a vyslovte do mikrofonu zařízení anglickou frázi nebo větu. Váš hlas se přenáší do služby Speech Services a přepisu na text, který se zobrazí v okně.

    ![Snímek obrazovky uživatelského rozhraní pro rozpoznávání řeči](media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte C# ukázky na GitHubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Viz také:

- [Překlad řeči](how-to-translate-speech-csharp.md)
- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)

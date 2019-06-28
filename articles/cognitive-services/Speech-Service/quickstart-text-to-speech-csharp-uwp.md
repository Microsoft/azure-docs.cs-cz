---
title: 'Rychlý start: Syntetizace řeči, C# (UPW) – hlasové služby'
titleSuffix: Azure Cognitive Services
description: V tomto článku vytvoříte C# aplikace univerzální platformy Windows (UPW) s využitím Cognitive Services SDK řeči. Můžete syntetizace řeč z textu v reálném čase na vaše zařízení mluvčího. Aplikace je sestavená pomocí balíčku NuGet sady SDK pro řeč a Microsoft Visual Studio 2017.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: c11fbff883d2699bdd0a107fc462524d92ee410d
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67467443"
---
# <a name="quickstart-synthesize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Rychlý start: Syntetizace řeči do aplikace pro UPW pomocí sady SDK pro řeč

Rychlí průvodci jsou také k dispozici pro [rozpoznávání řeči](quickstart-csharp-uwp.md), [překlad řeči](quickstart-translate-speech-uwp.md) a [virtuálních asistentů hlasové první](quickstart-virtual-assistant-csharp-uwp.md).

V tomto článku se při vývoji C# univerzální platformu Windows (UPW; Windows verze 1709 později) aplikací pomocí služeb Cognitive Services [sadou SDK pro řeč](speech-sdk.md). Program bude syntetizace řeč z textu v reálném čase na vaše zařízení mluvčího. Tato aplikace je vytvořená pomocí [balíčku NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a sady Microsoft Visual Studio 2017 (libovolné edice).

> [!NOTE]
> Univerzální platforma Windows umožňuje vyvíjet aplikace, které běží na všech zařízeních podporujících Windows 10 včetně počítačů, Xboxu, Surface Hubu a dalších.

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Klíč předplatného pro službu rozpoznávání řeči. [Získat zdarma](get-started.md).

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

1. Zadejte nějaký text do textového pole a klikněte na tlačítko **Speak**. Text se přenášejí do služeb řeči a syntetizovat do mluvené řeči, který přehraje na reproduktoru.

    ![Snímek obrazovky uživatelského rozhraní pro syntézu řeči](media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte C# ukázky na Githubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Další informace najdete v tématech

- [Přizpůsobení hlasová písma](how-to-customize-voice-font.md)
- [Záznam hlasu ukázky](record-custom-voice-samples.md)

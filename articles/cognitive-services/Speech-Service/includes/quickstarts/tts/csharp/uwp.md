---
title: 'Rychlý Start: syntetizace řeči C# , (UWP) – služba pro rozpoznávání řeči'
titleSuffix: Azure Cognitive Services
description: V tomto článku vytvoříte C# aplikace univerzální platformy Windows (UPW) s využitím Cognitive Services SDK řeči. Rozpoznávání řeči textu v reálném čase pro mluvčí zařízení. Aplikace je sestavená pomocí balíčku NuGet sady Speech SDK a Microsoft Visual Studio 2019.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/19/2019
ms.author: yinhew
ms.openlocfilehash: 8ca97be2863bd8e45ac7937c49c464fa2f216b11
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925513"
---
> [!NOTE]
> Univerzální platforma Windows umožňuje vyvíjet aplikace, které běží na všech zařízeních podporujících Windows 10 včetně počítačů, Xboxu, Surface Hubu a dalších.

## <a name="prerequisites"></a>Předpoklady

Než začnete, nezapomeňte:

> [!div class="checklist"]
> * [Vytvoření prostředku Azure Speech](../../../../get-started.md)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=uwp)

## <a name="add-sample-code"></a>Přidání ukázkového kódu

Nyní přidejte kód jazyka XAML, který definuje uživatelské rozhraní aplikace, a přidejte implementaci kódu na C# pozadí.

1. V **Průzkumník řešení**otevřete `MainPage.xaml`.

1. V zobrazení jazyka XAML návrháře vložte následující fragment kódu XAML do značky **Grid** (mezi `<Grid>` a `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml#StackPanel)]

1. V **Průzkumník řešení**otevřete zdrojový soubor kódu na pozadí `MainPage.xaml.cs`. (Je seskupeno pod `MainPage.xaml`.)

1. Nahraďte veškerý kód v něm následujícím fragmentem kódu:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml.cs#code)]

1. V obslužné rutině `Speak_ButtonClicked` zdrojového souboru Najděte `YourSubscriptionKey`řetězce a nahraďte ho klíčem předplatného.

1. V obslužné rutině `Speak_ButtonClicked` Najděte `YourServiceRegion`řetězce a nahraďte ho [oblastí](~/articles/cognitive-services/Speech-Service/regions.md) přidruženou k vašemu předplatnému. (Například pro předplatné bezplatné zkušební verze použijte `westus`.)

1. V řádku nabídek vyberte **soubor** > **Uložit vše** a uložte provedené změny.

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

Teď jste připraveni sestavit a otestovat svoji aplikaci.

1. V řádku nabídek výběrem možnosti **sestavit** > **Sestavit řešení** sestavte aplikaci. Kód by se teď měl zkompilovat bez chyb.

1. Zvolte **ladění** > **Spustit ladění** (nebo stiskněte klávesu **F5**) a spusťte aplikaci. Zobrazí se okno **HelloWorld** .

   ![Ukázková aplikace pro rozpoznávání řeči C# pro UWP v rychlém startu](~/articles/cognitive-services/Speech-Service/media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. Do textového pole zadejte nějaký text a klikněte na **mluvit**. Váš text se přenáší do služby pro rozpoznávání řeči a syntetizuje se na řeč, který hraje na svém mluvčím.

    ![Uživatelské rozhraní syntézy řeči](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Viz také

- [Vytvoření vlastního hlasu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Zaznamenat vlastní ukázky hlasu](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)

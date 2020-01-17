---
title: 'Rychlý Start: Převod řeči na text, C# (UWP) – služba Speech'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 12/09/2019
ms.author: erhopf
ms.topic: include
ms.openlocfilehash: 70db4e23aebe2a986c9aa4c85c28ac6529ac3202
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "76155973"
---
## <a name="prerequisites"></a>Požadavky

Než začnete, nezapomeňte:

> [!div class="checklist"]
> * [Vytvoření prostředku Azure Speech](../../../../get-started.md)
> * [Nastavení vývojového prostředí](../../../../quickstarts/setup-platform.md?tabs=uwp)
> * [Vytvořit prázdný ukázkový projekt](../../../../quickstarts/create-project.md?tabs=uwp)

## <a name="add-sample-code"></a>Přidání ukázkového kódu

Nyní přidejte kód jazyka XAML, který definuje uživatelské rozhraní aplikace, a přidejte implementaci kódu na C# pozadí.

1. V **Průzkumník řešení**otevřete `MainPage.xaml`.

1. V zobrazení jazyka XAML návrháře vložte následující fragment kódu XAML do značky **Grid** (mezi `<Grid>` a `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml#StackPanel)]

1. V **Průzkumník řešení**otevřete zdrojový soubor kódu na pozadí `MainPage.xaml.cs`. (Je seskupeno pod `MainPage.xaml`.)

1. Nahraďte veškerý kód v něm následujícím fragmentem kódu:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml.cs#code)]

1. V obslužné rutině `SpeechTranslationFromMicrophone_ButtonClicked` v tomto souboru vyhledejte řetězec `YourSubscriptionKey`a nahraďte ho klíčovým předplatným.

1. V obslužné rutině `SpeechTranslationFromMicrophone_ButtonClicked` Najděte `YourServiceRegion`řetězce a nahraďte ho [oblastí](~/articles/cognitive-services/Speech-Service/regions.md) přidruženou k vašemu předplatnému. (Například pro předplatné bezplatné zkušební verze použijte `westus`.)

1. V řádku nabídek vyberte **soubor** > **Uložit vše** a uložte provedené změny.

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

Teď jste připraveni sestavit a otestovat svoji aplikaci.

1. V řádku nabídek vyberte **sestavit** > **Sestavit řešení** a sestavte aplikaci. Kód by se teď měl zkompilovat bez chyb.

1. Zvolte **ladění** > **Spustit ladění** (nebo stiskněte klávesu **F5**) a spusťte aplikaci. Zobrazí se okno **HelloWorld** .

   ![Ukázková aplikace pro překlad C# UWP v – rychlý Start](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-speech-uwp-helloworld-window.png)

1. Vyberte možnost **Povolit mikrofon**a když se zobrazí žádost o přístupové oprávnění, vyberte **Ano**.

   ![Žádost o oprávnění k přístupu k mikrofonu](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Vyberte možnost **přeložit řeč ze vstupu mikrofonu**a mluvte do mikrofonu zařízení anglickou frázi nebo větu. Aplikace přenáší váš hlas do služby pro rozpoznávání řeči, která překládá řeč na text v jiném jazyce (v tomto případě je to němčina). Služba Speech odesílá přeložený text zpátky do aplikace, která zobrazuje překlad v okně.

   ![Uživatelské rozhraní překladu řeči](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]

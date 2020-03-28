---
title: 'Úvodní příručka: Překlad řeči na text, C# (UPW) - služba řeči'
titleSuffix: Azure Cognitive Services
description: Bude doplněno
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 12/09/2019
ms.author: erhopf
ms.topic: include
ms.openlocfilehash: e84c7423e0f061004a827ac45c211d7db9c556df
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925292"
---
## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

> [!div class="checklist"]
> * [Vytvoření řečového prostředku Azure](../../../../get-started.md)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=uwp)

## <a name="add-sample-code"></a>Přidání ukázkového kódu

Nyní přidejte kód XAML, který definuje uživatelské rozhraní aplikace, a přidejte implementaci kódu C#.

1. V **Průzkumníku** `MainPage.xaml`řešení otevřete .

1. V zobrazení XAML návrháře vložte do značky **Mřížka** následující úrych XAML (mezi `<Grid>` a `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml#StackPanel)]

1. V **Průzkumníku řešení**otevřete zdrojový `MainPage.xaml.cs`soubor s kódem na pozadí . (Je seskupena pod `MainPage.xaml`.)

1. Nahraďte veškerý kód v něm následujícím fragmentem:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml.cs#code)]

1. V `SpeechTranslationFromMicrophone_ButtonClicked` obslužné rutině `YourSubscriptionKey`v tomto souboru najděte řetězec a nahraďte jej klíčem předplatného.

1. V `SpeechTranslationFromMicrophone_ButtonClicked` obslužné `YourServiceRegion`rutině najděte řetězec a nahraďte ho [oblastí](~/articles/cognitive-services/Speech-Service/regions.md) přidruženou k vašemu předplatnému. (Použijte například `westus` bezplatné zkušební předplatné.)

1. Na řádku nabídek zvolte **Uložit** > **vše,** chcete-li uložit změny.

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

Nyní jste připraveni k sestavení a testování aplikace.

1. Na řádku nabídek vyberte **sestavení** > **sestavení řešení** k sestavení aplikace. Kód by se teď měl zkompilovat bez chyb.

1. **Chcete-li** > spustit aplikaci, zvolte Ladění**spouštění ladění** (nebo stisknutím **klávesy F5).** Zobrazí se okno **Helloworld.**

   ![Ukázka aplikace překladu UPW v C# - rychlý start](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-speech-uwp-helloworld-window.png)

1. Vyberte **Povolit mikrofon**a když se objeví žádost o přístupová oprávnění, vyberte **Ano**.

   ![Žádost o oprávnění k přístupu k mikrofonu](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Vyberte **Přeložit řeč ze vstupu mikrofonu**a do mikrofonu zařízení vyslovte anglickou frázi nebo větu. Aplikace přenáší váš projev do služby Speech, která překládá řeč do textu v jiném jazyce (v tomto případě němčině). Služba Řeč odešle přeložený text zpět do aplikace, která zobrazí překlad v okně.

   ![Uživatelské rozhraní překladu řeči](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]

---
title: 'Rychlý start: Přeložit Speech, C# (UWP) – služba Speech'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu vytvoříte jednoduchou aplikaci Univerzální platforma Windows (UWP) pro zachycení uživatelského řeči, překladu na jiný jazyk a výstup textu do příkazového řádku. Tato příručka je určená pro uživatele Windows.
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 07/23/2019
ms.author: erhopf
ms.openlocfilehash: 71f8ecfaa11538cf43284d2aafff61cbf9655c0e
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607765"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c-uwp"></a>Rychlý start: Převod řeči pomocí sady Speech SDK pro C# (UWP)

K dispozici jsou také rychlé starty pro [Převod řeči na text](quickstart-csharp-uwp.md), pro [Převod textu na řeč](quickstart-text-to-speech-csharp-uwp.md) a pro [hlasový vstup](quickstart-virtual-assistant-csharp-uwp.md).

V tomto rychlém startu vytvoříte jednoduchou aplikaci Univerzální platforma Windows (UWP), která zachycuje řeč uživatele z mikrofonu vašeho počítače, převede řeč a transcribes přeložený text do příkazového řádku v reálném čase. Tato aplikace je určená ke spuštění v 64 Windows a je sestavená pomocí [balíčku NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a Microsoft Visual Studio 2017 nebo novější.

Úplný seznam jazyků dostupných pro překlad řeči najdete v tématu [Podpora jazyků](language-support.md).

> [!NOTE]
> UWP vám umožní vyvíjet aplikace, které běží na jakémkoli zařízení s podporou Windows 10, včetně počítačů, Xbox, Surface Hub a dalších zařízení.

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) nebo novější
* Klíč předplatného Azure pro službu Speech Service. [Získejte je zdarma](get-started.md).

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Uživatelské rozhraní aplikace je definované pomocí XAML. Otevřete `MainPage.xaml` v Průzkumníku řešení. V zobrazení jazyka XAML návrháře vložte následující fragment kódu XAML mezi `<Grid>` a. `</Grid>`

    [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Otevřete zdrojový soubor s kódem `MainPage.xaml.cs` (je seskupený pod `MainPage.xaml`). Veškerý kód nahraďte následujícím kódem.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. V obslužné rutině `SpeechTranslationFromMicrophone_ButtonClicked` v tomto souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.

1. V obslužné rutině `SpeechTranslationFromMicrophone_ButtonClicked` nahraďte řetězec `YourServiceRegion` [oblastí](regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

1. Uložte všechny změny do projektu.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. Sestavte aplikaci. V řádku nabídek vyberte **Sestavení** > **Sestavit řešení**. Kód by se teď měl zkompilovat bez chyb.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Sestavit řešení](media/sdk/qs-csharp-uwp-08-build.png "Úspěšné sestavení")

1. Spusťte aplikaci. V řádku nabídek vyberte **Ladit** > **Spustit ladění** nebo stiskněte klávesu **F5**.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Spustit ladění](media/sdk/qs-csharp-uwp-09-start-debugging.png "Spuštění aplikace s laděním")

1. Otevře se okno. Vyberte **Povolit mikrofon** a potvrďte žádost o oprávnění, která se otevře.

    ![Snímek obrazovky s žádostí o oprávnění](media/sdk/qs-csharp-uwp-10-access-prompt.png "Spuštění aplikace s laděním")

1. Vyberte **Rozpoznávání řeči se vstupem z mikrofonu** a vyslovte do mikrofonu zařízení anglickou frázi nebo větu. Vaše řeč se přenese do služby Speech a přepíše na text, který se zobrazí v okně.

    ![Snímek obrazovky uživatelského rozhraní pro rozpoznávání řeči](media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte C# ukázky na GitHubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Viz také:

- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)

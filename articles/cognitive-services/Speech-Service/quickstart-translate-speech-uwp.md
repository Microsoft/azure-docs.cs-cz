---
title: 'Rychlý start: Překlad řeči, C# (UPW) – hlasové služby'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu vytvoříte jednoduchou aplikaci univerzální platformy Windows (UPW) zaznamenat uživatelské řeči, přeloží ji do jiného jazyka a panelem text do příkazového řádku. Tato příručka je určená pro uživatele Windows.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: bb0296098d0717e95e9aa1d73229d59709d13766
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56106082"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c-uwp"></a>Rychlý start: Překlad řeči se sadou SDK pro řeč pro C# (UPW)

V tomto rychlém startu vytvoříte jednoduchou aplikaci univerzální platformy Windows (UPW), který zachycuje uživatele řeči z vašeho počítače mikrofonu, přeloží řeč a transcribes přeložený text do příkazového řádku v reálném čase. Tato aplikace je navržen pro spouštění na Windows 64-bit a využívá rozhraní [balíček NuGet sady SDK pro řeč](https://aka.ms/csspeech/nuget) a Microsoft Visual Studio 2017.

Úplný seznam jazyků, které jsou k dispozici pro překlad řeči, naleznete v tématu [jazykovou podporu](language-support.md).

> [!NOTE]
> UPW umožňuje vyvíjet aplikace, které běží na všech zařízeních, která podporuje Windows 10, včetně počítačů, Xbox, Surface Hubu a dalších zařízení.

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Klíč předplatného pro službu rozpoznávání řeči. [Získat zdarma](get-started.md).

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Uživatelské rozhraní aplikace je definované pomocí XAML. Otevřete `MainPage.xaml` v Průzkumníku řešení. V zobrazení návrháři XAML, vložte následující fragment kódu XAML mezi `<Grid>` a `</Grid>`.

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
> [Prozkoumejte C# ukázky na Githubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Další informace najdete v tématech

- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)

---
title: 'Rychlý start: Přeložit Speech, C# (UWP) – služba Speech'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu vytvoříte aplikaci Univerzální platforma Windows (UWP) pro zachycení uživatelského rozpoznávání řeči, překladu na jiný jazyk a výstup textu do příkazového řádku. Tato příručka je určená pro uživatele Windows.
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 08/19/2019
ms.author: erhopf
ms.topic: quickstart
ms.openlocfilehash: e513cbbc615965ef196a830351aab8ac241c3f20
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382608"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c-uwp"></a>Rychlý start: Převod řeči pomocí sady Speech SDK pro C# (UWP)

K dispozici jsou také rychlé starty pro [rozpoznávání řeči](quickstart-csharp-uwp.md), [syntézu řeči a funkce](quickstart-text-to-speech-csharp-uwp.md)pro [virtuální asistenty hlasu First](quickstart-virtual-assistant-csharp-uwp.md).

V tomto rychlém startu vytvoříte aplikaci Univerzální platforma Windows (UWP), která zachycuje řeč uživatele z mikrofonu vašeho počítače, převede řeč a transcribes přeložený text do příkazového řádku v reálném čase. Tato aplikace je navržená tak, aby běžela v 64 Windows a byla sestavena pomocí [balíčku NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a Microsoft Visual Studio 2019.

Úplný seznam jazyků dostupných pro překlad řeči najdete v tématu [Podpora jazyků](language-support.md).

> [!NOTE]
> UWP vám umožní vyvíjet aplikace, které běží na jakémkoli zařízení s podporou Windows 10, včetně počítačů, Xbox, Surface Hub a dalších zařízení.

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

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. V **Průzkumník řešení**otevřete zdrojový soubor `MainPage.xaml.cs`kódu na pozadí. (Je seskupena pod `MainPage.xaml`.)

1. Nahraďte veškerý kód v něm následujícím fragmentem kódu:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. V obslužné rutině v tomto souboru vyhledejte řetězec `YourSubscriptionKey`a nahraďte ho klíčem předplatného. `SpeechTranslationFromMicrophone_ButtonClicked`

1. V obslužné rutině Najděte řetězec `YourServiceRegion`a nahraďte ho oblastí, která je přidružená k vašemu předplatnému. [](regions.md) `SpeechTranslationFromMicrophone_ButtonClicked` (Například použijte `westus` pro předplatné bezplatné zkušební verze.)

1. V řádku **nabídek výběrem možnosti** > **Uložit vše** uložte změny.

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

Teď jste připraveni sestavit a otestovat svoji aplikaci.

1. V řádku nabídek vyberte **sestavit** > **sestavení řešení** a sestavte aplikaci. Kód by se teď měl zkompilovat bez chyb.

1. Zvolte **ladění** > **Spustit ladění** (nebo stiskněte klávesu **F5**) a spusťte aplikaci. Zobrazí se okno **HelloWorld** .

   ![Ukázková aplikace pro překlad C# UWP v – rychlý Start](media/sdk/qs-translate-speech-uwp-helloworld-window.png)

1. Vyberte možnost **Povolit mikrofon**a když se zobrazí žádost o přístupové oprávnění, vyberte **Ano**.

   ![Žádost o oprávnění k přístupu k mikrofonu](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Vyberte možnost **přeložit řeč ze vstupu mikrofonu**a mluvte do mikrofonu zařízení anglickou frázi nebo větu. Aplikace přenáší váš hlas do služby pro rozpoznávání řeči, která překládá řeč na text v jiném jazyce (v tomto případě je to němčina). Služba Speech odesílá přeložený text zpátky do aplikace, která zobrazuje překlad v okně.

   ![Uživatelské rozhraní překladu řeči](media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte C# ukázky na GitHubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Viz také:

- [Výuka modelu pro Custom Speech](how-to-custom-speech-train-model.md)

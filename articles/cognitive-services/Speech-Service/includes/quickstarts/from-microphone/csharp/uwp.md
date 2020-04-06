---
title: 'Úvodní příručka: Rozpoznávání řeči z mikrofonu, C# (UPW) – služba řeči'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.openlocfilehash: 9a3f478604547819162ad13d39a9383263321857
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671687"
---
## <a name="prerequisites"></a>Požadavky

Než začnete:

> [!div class="checklist"]
> * [Vytvoření řečového prostředku Azure](../../../../get-started.md)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=uwp&pivots=programming-language-csharp)
> * Ujistěte se, že máte přístup k mikrofonu pro snímání zvuku

Pokud jste to už udělali, skvělé. Pojďme dál.

## <a name="open-your-project-in-visual-studio"></a>Otevření projektu v sadě Visual Studio

Prvním krokem je ujistěte se, že máte projekt otevřený v sadě Visual Studio.

## <a name="start-with-some-boilerplate-code"></a>Začněte s nějakým standardním kódem

Přidáme nějaký kód, který funguje jako kostra pro náš projekt.

1. V **Průzkumníku** `MainPage.xaml`řešení otevřete .

2. V zobrazení XAML návrháře vložte do značky **Mřížka** následující úrych XAML (mezi `<Grid>` a `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml#StackPanel)]

3. V **Průzkumníku řešení**otevřete zdrojový `MainPage.xaml.cs`soubor s kódem na pozadí . (Je seskupena pod `MainPage.xaml`.)

4. Nahraďte kód následujícím základním kódem:

   [!code-csharp[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=6-50,55-56,94-154)]

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Před inicializaci objektu `SpeechRecognizer` je třeba vytvořit konfiguraci, která používá klíč předplatného a oblast předplatného. Vložte tento `RecognizeSpeechAsync()` kód do metody.

> [!NOTE]
> Tato ukázka `FromSubscription()` používá metodu k sestavení `SpeechConfig`. Úplný seznam dostupných metod naleznete v tématu [SpeechConfig Class](https://docs.microsoft.com/dotnet/api/)[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=51-53)]

## <a name="initialize-a-speechrecognizer"></a>Inicializovat rozpoznávání řeči

Nyní vytvoříme . `SpeechRecognizer` Tento objekt je vytvořen uvnitř using prohlášení k zajištění správné uvolnění nespravovaných prostředků. Vložte tento `RecognizeSpeechAsync()` kód do metody přímo pod konfigurací řeči.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=58,59,93)]

## <a name="recognize-a-phrase"></a>Rozpoznání fráze

Z `SpeechRecognizer` objektu zavoláte metodu. `RecognizeOnceAsync()` Tato metoda umožňuje řeči služby vědět, že odesíláte jednu frázi pro rozpoznávání a že jakmile je fráze identifikována k zastavení rozpoznávání řeči.

Uvnitř using prohlášení, přidejte tento kód.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=66)]

## <a name="display-the-recognition-results-or-errors"></a>Zobrazení výsledků rozpoznávání (nebo chyb)

Když je výsledek rozpoznávání vrácen službou Řeč, budete s ním chtít něco udělat. Uděláme to jednoduše a výsledek vytiskneme na stavový panel.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=68-93)]

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

Nyní jste připraveni k sestavení a testování aplikace.

1. Na řádku nabídek zvolte **Sestavení** > **sestavení řešení** k sestavení aplikace. Kód by se teď měl zkompilovat bez chyb.

1. **Chcete-li** > spustit aplikaci, zvolte Ladění**spouštění ladění** (nebo stisknutím **klávesy F5).** Zobrazí se okno **Helloworld.**

   ![Ukázka aplikace rozpoznávání řeči UpW v c# - rychlý start](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-helloworld-window.png)

1. Vyberte **Povolit mikrofon**a když se objeví žádost o přístupová oprávnění, vyberte **Ano**.

   ![Žádost o oprávnění k přístupu k mikrofonu](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Vyberte **Rozpoznávání řeči se vstupem z mikrofonu** a vyslovte do mikrofonu zařízení anglickou frázi nebo větu. Vaše řeč se přenese do služby Speech a přepíše na text, který se zobrazí v okně.

   ![Uživatelské rozhraní pro rozpoznávání řeči](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](../footer.md)]

---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 54b5e3f0ed2fd10fa04305fdefbec725143f62c5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422375"
---
## <a name="prerequisites"></a>Požadavky

Než začnete:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp" target="_blank">Nainstalujte sadu Speech SDK pro vývojové<span class="docon docon-navigate-external x-hidden-focus"></span>prostředí vytvořit a prázdné ukázkový projekt</a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Vytvoření aplikace LUIS pro rozpoznávání záměru

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>Otevření projektu v sadě Visual Studio

Dále otevřete projekt v sadě Visual Studio.

1. Spusťte Visual Studio 2019.
2. Načtěte projekt `Program.cs`a otevřete .

## <a name="start-with-some-boilerplate-code"></a>Začněte s nějakým standardním kódem

Přidáme nějaký kód, který funguje jako kostra pro náš projekt. Všimněte si, že jste vytvořili `RecognizeIntentAsync()`asynchronní metodu s názvem .
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-17,77-86)]

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Před inicializací objektu `IntentRecognizer` je třeba vytvořit konfiguraci, která používá klíč a umístění pro prostředek předpovědi LUIS.

> [!IMPORTANT]
> Startovací klíč a vývojové klávesy nebudou fungovat. Je nutné použít klíč předpověď a umístění, které jste vytvořili dříve. Další informace naleznete [v tématu Vytvoření aplikace LUIS pro rozpoznávání záměru](#create-a-luis-app-for-intent-recognition).

Vložte tento `RecognizeIntentAsync()` kód do metody. Ujistěte se, že aktualizujete tyto hodnoty:

* Nahraďte `"YourLanguageUnderstandingSubscriptionKey"` pomocí predikčního klíče LUIS.
* Nahraďte `"YourLanguageUnderstandingServiceRegion"` umístění luis. Použijte **identifikátor oblasti** z [oblasti](https://aka.ms/speech/sdkregion).

>[!TIP]
> Pokud potřebujete pomoc s hledáním těchto hodnot, přečtěte si informace [o vytvoření aplikace LUIS pro rozpoznávání záměru](#create-a-luis-app-for-intent-recognition).

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=26)]

Tato ukázka `FromSubscription()` používá metodu k sestavení `SpeechConfig`. Úplný seznam dostupných metod naleznete v tématu [SpeechConfig Class](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).

Sada Speech SDK bude ve výchozím nastavení rozpoznána použití jazyka en-us, viz [Určení zdrojového jazyka pro řeč na text,](../../../../how-to-specify-source-language.md) kde naleznete informace o výběru zdrojového jazyka.

## <a name="initialize-an-intentrecognizer"></a>Inicializovat rozpoznač záměrů

Nyní vytvoříme . `IntentRecognizer` Tento objekt je vytvořen uvnitř using prohlášení k zajištění správné uvolnění nespravovaných prostředků. Vložte tento `RecognizeIntentAsync()` kód do metody přímo pod konfigurací řeči.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=29-30,76)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Přidání languageunderstandingmodel a záměry

Je třeba přidružit `LanguageUnderstandingModel` k nástrojpro rozpoznávání záměru a přidat záměry, které chcete rozpoznat. Použijeme záměry z předem vytvořené domény pro domácí automatizaci. Vložte tento kód do příkazu using z předchozí části. Ujistěte se, `"YourLanguageUnderstandingAppId"` že nahradíte ID aplikace LUIS.

>[!TIP]
> Pokud potřebujete pomoc s vyhledáním této hodnoty, přečtěte si téma [Vytvoření aplikace LUIS pro rozpoznávání záměru](#create-a-luis-app-for-intent-recognition).

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=33-35)]

## <a name="recognize-an-intent"></a>Rozpoznání záměru

Z `IntentRecognizer` objektu zavoláte metodu. `RecognizeOnceAsync()` Tato metoda umožňuje řeči služby vědět, že odesíláte jednu frázi pro rozpoznávání a že jakmile je fráze identifikována k zastavení rozpoznávání řeči.

Uvnitř příkazu using přidejte tento kód pod model.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=46)]

## <a name="display-recognition-results-or-errors"></a>Zobrazení výsledků rozpoznávání (nebo chyb)

Když je výsledek rozpoznávání vrácen službou Řeč, budete s ním chtít něco udělat. Budeme to dělat jednoduše a výsledky vytiskneme na konzoli.

Uvnitř příkazu using `RecognizeOnceAsync()`níže přidejte tento kód:

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=49-75)]

## <a name="check-your-code"></a>Kontrola kódu

V tomto okamžiku by měl váš kód vypadat takto:

> [!NOTE]
> K této verzi jsme přidali několik komentářů.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-86)]

## <a name="build-and-run-your-app"></a>Vytvoření a spuštění aplikace

Teď jste připraveni vytvořit aplikaci a otestovat naše rozpoznávání řeči pomocí služby Řeč.

1. **Kompilace kódu** – z panelu nabídek sady Visual Studio zvolte **Build** > **Build Build Solution**.
2. **Spuštění aplikace** – z řádku nabídek zvolte **Ladění** > **ladění startování** nebo stiskněte <kbd>klávesu F5</kbd>.
3. **Začněte rozpoznávat** - Vyzve vás, abyste mluvili frází v angličtině. Vaše řeč je odeslána do služby Řeč, přepsána jako text a vykreslena v konzole.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]

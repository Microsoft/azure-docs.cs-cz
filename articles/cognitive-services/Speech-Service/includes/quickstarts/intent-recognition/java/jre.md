---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 01/27/2020
ms.topic: include
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 3481d751aacd4183bf5a999422b9631ec0e21edb
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925180"
---
## <a name="prerequisites"></a>Požadavky

Než začnete:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=jre" target="_blank">Nainstalujte sadu Speech SDK pro vývojové<span class="docon docon-navigate-external x-hidden-focus"></span>prostředí a vytvořte prázdný ukázkový projekt</a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Vytvoření aplikace LUIS pro rozpoznávání záměru

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>Otevření projektu

1. Otevřete upřednostňované ide.
2. Načtěte projekt `Main.java`a otevřete .

## <a name="start-with-some-boilerplate-code"></a>Začněte s nějakým standardním kódem

Přidáme nějaký kód, který funguje jako kostra pro náš projekt.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-20,68-75)]

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Před inicializací objektu `IntentRecognizer` je třeba vytvořit konfiguraci, která používá klíč a umístění pro prostředek předpovědi LUIS.  

Vložte tento kód do bloku `main()`try /catch v . Ujistěte se, že aktualizujete tyto hodnoty:

* Nahraďte `"YourLanguageUnderstandingSubscriptionKey"` pomocí predikčního klíče LUIS.
* Nahraďte `"YourLanguageUnderstandingServiceRegion"` umístění luis. Použít **identifikátor oblasti** z [oblasti](https://aka.ms/speech/sdkregion)

>[!TIP]
> Pokud potřebujete pomoc s hledáním těchto hodnot, přečtěte si informace [o vytvoření aplikace LUIS pro rozpoznávání záměru](#create-a-luis-app-for-intent-recognition).

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=27)]

Tato ukázka `FromSubscription()` používá metodu k sestavení `SpeechConfig`. Úplný seznam dostupných metod naleznete v tématu [SpeechConfig Class](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).

Sada Speech SDK bude ve výchozím nastavení rozpoznána použití jazyka en-us, viz [Určení zdrojového jazyka pro řeč na text,](../../../../how-to-specify-source-language.md) kde naleznete informace o výběru zdrojového jazyka.

## <a name="initialize-an-intentrecognizer"></a>Inicializovat rozpoznač záměrů

Nyní vytvoříme . `IntentRecognizer` Vložte tento kód přímo pod konfiguraci řeči.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=30)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Přidání languageunderstandingmodel a záměry

Je třeba přidružit `LanguageUnderstandingModel` k nástrojpro rozpoznávání záměru a přidat záměry, které chcete rozpoznat. Použijeme záměry z předem vytvořené domény pro domácí automatizaci.

Vložte tento `IntentRecognizer`kód pod aplikaci . Ujistěte se, `"YourLanguageUnderstandingAppId"` že nahradíte ID aplikace LUIS.

>[!TIP]
> Pokud potřebujete pomoc s vyhledáním této hodnoty, přečtěte si téma [Vytvoření aplikace LUIS pro rozpoznávání záměru](#create-a-luis-app-for-intent-recognition).

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=33-35)]

## <a name="recognize-an-intent"></a>Rozpoznání záměru

Z `IntentRecognizer` objektu zavoláte metodu. `recognizeOnceAsync()` Tato metoda umožňuje řeči služby vědět, že odesíláte jednu frázi pro rozpoznávání a že jakmile je fráze identifikována k zastavení rozpoznávání řeči.

Pod model vložte tento kód:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=40)]

## <a name="display-the-recognition-results-or-errors"></a>Zobrazení výsledků rozpoznávání (nebo chyb)

Když je výsledek rozpoznávání vrácen službou Řeč, budete s ním chtít něco udělat. Budeme to jednoduché a vytisknout výsledek na konzoli.

Vložte tento kód `recognizeOnceAsync()`pod volání aplikace .

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=43-64)]

## <a name="release-resources"></a>Uvolnit zdroje

Je důležité uvolnit prostředky řeči, když je budete používat. Vložte tento kód na konci bloku try / catch:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=66-67)]

## <a name="check-your-code"></a>Kontrola kódu

V tomto okamžiku by měl váš kód vypadat takto:

> [!NOTE]
> K této verzi jsme přidali několik komentářů.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-75)]

## <a name="build-and-run-your-app"></a>Vytvoření a spuštění aplikace

Stiskněte <kbd>klávesu F11</kbd>nebo vyberte **možnost Spustit** > **ladění**.
Následujících 15 sekund hlasového vstupu z mikrofonu se rozpozná a zaprotokoluje v okně konzoly.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]

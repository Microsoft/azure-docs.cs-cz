---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 01/27/2020
ms.topic: include
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 6bd276f07f75ecad21ffb0bf19150f6158eb7b7c
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77156536"
---
## <a name="prerequisites"></a>Předpoklady

Než začnete:

* Pokud se jedná o váš první projekt Java (JRE), použijte tento průvodce k <a href="~/articles/cognitive-services/Speech-Service/quickstarts/create-project.md?tabs=jre" target="_blank">Vytvoření prázdného ukázkového projektu <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=jre" target="_blank">Nainstalujte sadu Speech SDK pro vývojové prostředí <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Vytvoření aplikace v LUIS pro rozpoznávání záměrů

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>Otevřete projekt

1. Otevřete preferované integrované vývojové prostředí (IDE).
2. Načtěte projekt a otevřete `Main.java`.

## <a name="start-with-some-boilerplate-code"></a>Začínáme s některým často používaným kódem

Pojďme přidat kód, který funguje jako kostra pro náš projekt.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-20,68-75)]

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Předtím, než budete moci inicializovat objekt `IntentRecognizer`, je nutné vytvořit konfiguraci, která používá klíč a umístění prostředku předpovědi LUIS.  

Vložte tento kód do bloku try/catch v `main()`. Ujistěte se, že tyto hodnoty aktualizujete:

* Nahraďte `"YourLanguageUnderstandingSubscriptionKey"` klíčem předpovědi LUIS.
* Nahraďte `"YourLanguageUnderstandingServiceRegion"` umístěním LUIS. Použít parametr "Speech SDK" z [oblasti](https://aka.ms/speech/sdkregion)

>[!TIP]
> Pokud potřebujete nápovědu k nalezení těchto hodnot, přečtěte si téma [Vytvoření aplikace v Luis pro rozpoznávání záměrů](#create-a-luis-app-for-intent-recognition).

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=27)]

Tato ukázka používá metodu `FromSubscription()` k sestavení `SpeechConfig`. Úplný seznam dostupných metod naleznete v tématu [Třída SpeechConfig](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).

Sada Speech SDK bude standardně rozpoznána pomocí en-US pro daný jazyk. informace o výběru zdrojového jazyka najdete v tématu [určení zdrojového jazyka pro převod řeči na text](../../../../how-to-specify-source-language.md) .

## <a name="initialize-an-intentrecognizer"></a>Inicializovat IntentRecognizer

Nyní vytvoříme `IntentRecognizer`. Vložte tento kód přímo pod konfiguraci řeči.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=30)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Přidat LanguageUnderstandingModel a záměry

Je potřeba přidružit `LanguageUnderstandingModel` k nástroji pro rozpoznávání záměrů a přidat záměry, které chcete rozpoznat. Budeme používat záměry z předem připravené domény pro automatizaci domů.

Vložte tento kód pod `IntentRecognizer`. Ujistěte se, že `"YourLanguageUnderstandingAppId"` nahradíte ID aplikace LUIS.

>[!TIP]
> Pokud potřebujete nápovědu najít tuto hodnotu, přečtěte si téma [Vytvoření aplikace v Luis pro rozpoznávání záměrů](#create-a-luis-app-for-intent-recognition).

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=33-35)]

## <a name="recognize-an-intent"></a>Rozpoznávání záměru

Z objektu `IntentRecognizer` zavoláte metodu `recognizeOnceAsync()`. Tato metoda umožňuje službě rozpoznávání řeči zjistit, že posíláte jednoduchou frázi pro rozpoznávání, a že po identifikaci fráze zastavit rozpoznávání řeči.

Vložte tento kód pod model:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=40)]

## <a name="display-the-recognition-results-or-errors"></a>Zobrazit výsledky rozpoznávání (nebo chyby)

Když Služba rozpoznávání řeči vrátí výsledek rozpoznávání, budete s ním chtít něco dělat. My to Zjednodušme a vytiskneme výsledek do konzoly.

Vložte tento kód pod volání `recognizeOnceAsync()`: [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=43-64)]

## <a name="release-resources"></a>Uvolnit prostředky

Je důležité, abyste prostředky řeči uvolnili, až je budete používat. Vložte tento kód na konec bloku try/catch:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=66-67)]

## <a name="check-your-code"></a>Kontrolovat kód

V tomto okamžiku váš kód by měl vypadat takto:

> [!NOTE]
> Do této verze jsme přidali nějaké komentáře.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-75)]

## <a name="build-and-run-your-app"></a>Sestavení a spuštění aplikace

Stiskněte klávesu <kbd>F11</kbd>nebo vyberte **Spustit** > **ladění**.
Následujících 15 sekund hlasového vstupu z mikrofonu se rozpozná a zaprotokoluje v okně konzoly.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]

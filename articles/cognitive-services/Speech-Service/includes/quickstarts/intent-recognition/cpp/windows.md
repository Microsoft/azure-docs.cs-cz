---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 01/27/2020
ms.topic: include
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 292eba09b151d8de50fc379051091a4d2990a105
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77156218"
---
## <a name="prerequisites"></a>Předpoklady

Než začnete:

* Pokud se jedná o váš C++ první projekt, pomocí tohoto průvodce <a href="~/articles/cognitive-services/Speech-Service/quickstarts/create-project.md?tabs=windows" target="_blank">vytvořte prázdný ukázkový projekt <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=windows" target="_blank">Nainstalujte sadu Speech SDK pro vývojové prostředí <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Vytvoření aplikace v LUIS pro rozpoznávání záměrů

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>Otevřete projekt v sadě Visual Studio

Pak otevřete projekt v aplikaci Visual Studio.

1. Spusťte Visual Studio 2019.
2. Načtěte projekt a otevřete `helloworld.cpp`.

## <a name="start-with-some-boilerplate-code"></a>Začínáme s některým často používaným kódem

Pojďme přidat kód, který funguje jako kostra pro náš projekt. Nezapomeňte, že jste vytvořili asynchronní metodu nazvanou `recognizeIntent()`.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-16,72-80)]

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Předtím, než budete moci inicializovat objekt `IntentRecognizer`, je nutné vytvořit konfiguraci, která používá klíč a umístění prostředku předpovědi LUIS.

> [!IMPORTANT]
> Spouštěcí klíč a klíč pro vytváření obsahu nebudou fungovat. Je nutné použít klíč předpovědi a umístění, které jste vytvořili dříve. Další informace najdete v tématu [Vytvoření aplikace Luis pro rozpoznávání záměrů](#create-a-luis-app-for-intent-recognition).

Vložte tento kód do metody `recognizeIntent()`. Ujistěte se, že tyto hodnoty aktualizujete:

* Nahraďte `"YourLanguageUnderstandingSubscriptionKey"` klíčem předpovědi LUIS.
* Nahraďte `"YourLanguageUnderstandingServiceRegion"` umístěním LUIS.  Použijte parametr "Speech SDK" z [oblasti](https://aka.ms/speech/sdkregion).

>[!TIP]
> Pokud potřebujete nápovědu k nalezení těchto hodnot, přečtěte si téma [Vytvoření aplikace v Luis pro rozpoznávání záměrů](#create-a-luis-app-for-intent-recognition).

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=25)]

Tato ukázka používá metodu `FromSubscription()` k sestavení `SpeechConfig`. Úplný seznam dostupných metod naleznete v tématu [Třída SpeechConfig](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig).

Sada Speech SDK bude standardně rozpoznána pomocí en-US pro daný jazyk. informace o výběru zdrojového jazyka najdete v tématu [určení zdrojového jazyka pro převod řeči na text](../../../../how-to-specify-source-language.md) .

## <a name="initialize-an-intentrecognizer"></a>Inicializovat IntentRecognizer

Nyní vytvoříme `IntentRecognizer`. Vložte tento kód do metody `recognizeIntent()` hned pod konfigurací řeči.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=28)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Přidat LanguageUnderstandingModel a záměry

Je potřeba přidružit `LanguageUnderstandingModel` k nástroji pro rozpoznávání záměrů a přidat záměry, které chcete rozpoznat. Budeme používat záměry z předem připravené domény pro automatizaci domů.

Vložte tento kód pod `IntentRecognizer`. Ujistěte se, že `"YourLanguageUnderstandingAppId"` nahradíte ID aplikace LUIS.

>[!TIP]
> Pokud potřebujete nápovědu najít tuto hodnotu, přečtěte si téma [Vytvoření aplikace v Luis pro rozpoznávání záměrů](#create-a-luis-app-for-intent-recognition).

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=31-33)]

## <a name="recognize-an-intent"></a>Rozpoznávání záměru

Z objektu `IntentRecognizer` zavoláte metodu `RecognizeOnceAsync()`. Tato metoda umožňuje službě rozpoznávání řeči zjistit, že posíláte jednoduchou frázi pro rozpoznávání, a že po identifikaci fráze zastavit rozpoznávání řeči. Pro zjednodušení budeme čekat na dokončení budoucího navrácení.

Vložte tento kód pod model:

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=43)]

## <a name="display-the-recognition-results-or-errors"></a>Zobrazit výsledky rozpoznávání (nebo chyby)

Když Služba rozpoznávání řeči vrátí výsledek rozpoznávání, budete s ním chtít něco dělat. My to Zjednodušme a vytiskneme výsledek do konzoly.

Následující kód vložte `auto result = recognizer->RecognizeOnceAsync().get();`:

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=46-71)]

## <a name="check-your-code"></a>Kontrolovat kód

V tomto okamžiku váš kód by měl vypadat takto:

> [!NOTE]
> Do této verze jsme přidali nějaké komentáře.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-79)]

## <a name="build-and-run-your-app"></a>Sestavení a spuštění aplikace

Nyní jste připraveni sestavit aplikaci a otestovat rozpoznávání řeči pomocí služby Speech.

1. **Zkompilujte kód** -z panelu nabídek v aplikaci Visual Studio, vyberte **sestavení** **řešení**Build > .
2. **Spusťte aplikaci** – z panelu nabídek zvolte možnost **ladění** > **Spustit ladění** nebo stiskněte klávesu <kbd>F5</kbd>.
3. **Spustit rozpoznávání** – zobrazí výzvu k vymluvenému vynechání fráze v angličtině. Váš hlas se odešle službě Speech, přepisu jako text a vykreslí se v konzole nástroje.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]

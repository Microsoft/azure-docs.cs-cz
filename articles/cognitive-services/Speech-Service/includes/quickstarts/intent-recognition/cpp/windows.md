---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 08679bac77121ee2d276f3d2854e0b119c769582
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98109490"
---
## <a name="prerequisites"></a>Požadavky

Než začnete:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp" target="_blank">Nainstalujte sadu Speech SDK pro vývojové prostředí a vytvořte prázdný ukázkový projekt <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Vytvoření aplikace v LUIS pro rozpoznávání záměrů

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>Otevřete projekt v aplikaci Visual Studio

Pak otevřete projekt v aplikaci Visual Studio.

1. Spusťte Visual Studio 2019.
2. Načtěte projekt a otevřete `helloworld.cpp` .

## <a name="start-with-some-boilerplate-code"></a>Začínáme s některým často používaným kódem

Pojďme přidat kód, který funguje jako kostra pro náš projekt. Nezapomeňte, že jste vytvořili asynchronní metodu s názvem `recognizeIntent()` .

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-16,72-80)]

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Předtím, než budete moci inicializovat `IntentRecognizer` objekt, je nutné vytvořit konfiguraci, která používá klíč a umístění prostředku předpovědi Luis.

> [!IMPORTANT]
> Spouštěcí klíč a klíč pro vytváření obsahu nebudou fungovat. Je nutné použít klíč předpovědi a umístění, které jste vytvořili dříve. Další informace najdete v tématu [Vytvoření aplikace Luis pro rozpoznávání záměrů](#create-a-luis-app-for-intent-recognition).

Vložte tento kód do `recognizeIntent()` metody. Ujistěte se, že tyto hodnoty aktualizujete:

* Nahraďte `"YourLanguageUnderstandingSubscriptionKey"` klíčem předpovědi Luis.
* Nahraďte `"YourLanguageUnderstandingServiceRegion"` umístěním Luis.  Použijte **identifikátor oblasti** z [oblasti](../../../../regions.md).

>[!TIP]
> Pokud potřebujete nápovědu k nalezení těchto hodnot, přečtěte si téma [Vytvoření aplikace v Luis pro rozpoznávání záměrů](#create-a-luis-app-for-intent-recognition).

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=25)]

Tato ukázka používá `FromSubscription()` metodu pro sestavení `SpeechConfig` . Úplný seznam dostupných metod naleznete v tématu [Třída SpeechConfig](/cpp/cognitive-services/speech/speechconfig).

Sada Speech SDK bude standardně rozpoznána pomocí en-US pro daný jazyk. informace o výběru zdrojového jazyka najdete v tématu [určení zdrojového jazyka pro převod řeči na text](../../../../how-to-specify-source-language.md) .

## <a name="initialize-an-intentrecognizer"></a>Inicializovat IntentRecognizer

Teď vytvoříme `IntentRecognizer` . Vložte tento kód do `recognizeIntent()` metody hned pod konfigurací řeči.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=28)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Přidat LanguageUnderstandingModel a záměry

Je potřeba přidružit k `LanguageUnderstandingModel` nástroji pro rozpoznávání záměru a přidat záměry, které chcete rozpoznat. Budeme používat záměry z předem připravené domény pro automatizaci domů.

Vložte tento kód pod `IntentRecognizer` . Ujistěte se, že jste nahradili `"YourLanguageUnderstandingAppId"` ID aplikace Luis.

>[!TIP]
> Pokud potřebujete nápovědu najít tuto hodnotu, přečtěte si téma [Vytvoření aplikace v Luis pro rozpoznávání záměrů](#create-a-luis-app-for-intent-recognition).

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=31-33)]

Tento příklad používá `AddIntent()` funkci k samostatnému přidání záměrů. Pokud chcete přidat všechny záměry z modelu, použijte `AddAllIntents(model)` a předejte model.

> [!NOTE]
> Sada Speech SDK podporuje pouze koncové body LUIS v 2.0.
> V případě, že chcete použít vzor adresy URL v 2.0, je nutné ručně upravit adresu URL koncového bodu v 3.0, kterou najdete v poli příklad dotazu.
> Koncové body LUIS v 2.0 se vždy řídí jedním z těchto dvou vzorů:
> * `https://{AzureResourceName}.cognitiveservices.azure.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
> * `https://{Region}.api.cognitive.microsoft.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`

## <a name="recognize-an-intent"></a>Rozpoznávání záměru

Z `IntentRecognizer` objektu budete volat `RecognizeOnceAsync()` metodu. Tato metoda umožňuje službě rozpoznávání řeči zjistit, že posíláte jednoduchou frázi pro rozpoznávání, a že po identifikaci fráze zastavit rozpoznávání řeči. Pro zjednodušení budeme čekat na dokončení budoucího navrácení.

Vložte tento kód pod model:

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=43)]

## <a name="display-the-recognition-results-or-errors"></a>Zobrazit výsledky rozpoznávání (nebo chyby)

Když Služba rozpoznávání řeči vrátí výsledek rozpoznávání, budete s ním chtít něco dělat. My to Zjednodušme a vytiskneme výsledek do konzoly.

Níže vložte následující kód `auto result = recognizer->RecognizeOnceAsync().get();` :

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=46-71)]

## <a name="check-your-code"></a>Kontrolovat kód

V tomto okamžiku váš kód by měl vypadat takto:

> [!NOTE]
> Do této verze jsme přidali nějaké komentáře.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-79)]

## <a name="build-and-run-your-app"></a>Sestavení a spuštění aplikace

Nyní jste připraveni sestavit aplikaci a otestovat rozpoznávání řeči pomocí služby Speech.

1. **Zkompilujte kód** -z panelu nabídek v aplikaci Visual Studio, vyberte **sestavení**  >  **řešení** sestavení.
2. **Spusťte aplikaci** – z řádku nabídek zvolte **ladění**  >  **Spustit ladění** nebo stiskněte klávesu <kbd>F5</kbd>.
3. **Spustit rozpoznávání** – zobrazí výzvu k vymluvenému vynechání fráze v angličtině. Váš hlas se odešle službě Speech, přepisu jako text a vykreslí se v konzole nástroje.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]
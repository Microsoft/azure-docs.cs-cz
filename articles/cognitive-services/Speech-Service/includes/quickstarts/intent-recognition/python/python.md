---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 0cb27a8dc5685ce295c2ce30820734c4301e9dc6
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98109469"
---
## <a name="prerequisites"></a>Požadavky

Než začnete:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?pivots=programming-language-python" target="_blank">Nainstalujte sadu Speech SDK pro vývojové prostředí a vytvořte prázdný ukázkový projekt <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Vytvoření aplikace v LUIS pro rozpoznávání záměrů

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>Otevřete projekt

1. Otevřete preferované integrované vývojové prostředí (IDE).
2. Vytvořte nový projekt a vytvořte soubor s názvem a `quickstart.py` otevřete ho.

## <a name="start-with-some-boilerplate-code"></a>Začínáme s některým často používaným kódem

Pojďme přidat kód, který funguje jako kostra pro náš projekt.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Předtím, než budete moci inicializovat `IntentRecognizer` objekt, je nutné vytvořit konfiguraci, která používá klíč a umístění prostředku předpovědi Luis.

Vložte tento kód do `quickstart.py` . Ujistěte se, že tyto hodnoty aktualizujete:

* Nahraďte `"YourLanguageUnderstandingSubscriptionKey"` klíčem předpovědi Luis.
* Nahraďte `"YourLanguageUnderstandingServiceRegion"` umístěním Luis. Použít **identifikátor oblasti** z [oblasti](../../../../regions.md)

>[!TIP]
> Pokud potřebujete nápovědu k nalezení těchto hodnot, přečtěte si téma [Vytvoření aplikace v Luis pro rozpoznávání záměrů](#create-a-luis-app-for-intent-recognition).

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

Tato ukázka vytvoří `SpeechConfig` objekt pomocí Luis klíče a oblasti. Úplný seznam dostupných metod naleznete v tématu [Třída SpeechConfig](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig).

Sada Speech SDK bude standardně rozpoznána pomocí en-US pro daný jazyk. informace o výběru zdrojového jazyka najdete v tématu [určení zdrojového jazyka pro převod řeči na text](../../../../how-to-specify-source-language.md) .

## <a name="initialize-an-intentrecognizer"></a>Inicializovat IntentRecognizer

Teď vytvoříme `IntentRecognizer` . Vložte tento kód přímo pod konfiguraci řeči.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Přidat LanguageUnderstandingModel a záměry

Je potřeba přidružit k `LanguageUnderstandingModel` nástroji pro rozpoznávání záměrů a přidat záměry, které chcete rozpoznat. Budeme používat záměry z předem připravené domény pro automatizaci domů.

Vložte tento kód pod `IntentRecognizer` . Ujistěte se, že jste nahradili `"YourLanguageUnderstandingAppId"` ID aplikace Luis. 

>[!TIP]
> Pokud potřebujete nápovědu najít tuto hodnotu, přečtěte si téma [Vytvoření aplikace v Luis pro rozpoznávání záměrů](#create-a-luis-app-for-intent-recognition).

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

Tento příklad používá `add_intents()` funkci k přidání seznamu explicitně definovaných záměrů. Pokud chcete přidat všechny záměry z modelu, použijte `add_all_intents(model)` a předejte model.

> [!NOTE]
> Sada Speech SDK podporuje pouze koncové body LUIS v 2.0.
> V případě, že chcete použít vzor adresy URL v 2.0, je nutné ručně upravit adresu URL koncového bodu v 3.0, kterou najdete v poli příklad dotazu.
> Koncové body LUIS v 2.0 se vždy řídí jedním z těchto dvou vzorů:
> * `https://{AzureResourceName}.cognitiveservices.azure.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
> * `https://{Region}.api.cognitive.microsoft.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`

## <a name="recognize-an-intent"></a>Rozpoznávání záměru

Z `IntentRecognizer` objektu budete volat `recognize_once()` metodu. Tato metoda umožňuje službě rozpoznávání řeči zjistit, že posíláte jednoduchou frázi pro rozpoznávání, a že po identifikaci fráze zastavit rozpoznávání řeči.

Vložte tento kód pod model.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>Zobrazit výsledky rozpoznávání (nebo chyby)

Když Služba rozpoznávání řeči vrátí výsledek rozpoznávání, budete s ním chtít něco dělat. My to Zjednodušme a vytiskneme výsledek do konzoly.

Pod voláním `recognize_once()` přidejte tento kód.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>Kontrolovat kód

V tomto okamžiku by váš kód měl vypadat takto.

> [!NOTE]
> Do této verze jsme přidali nějaké komentáře.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>Sestavení a spuštění aplikace

Spusťte ukázku z konzoly nebo v integrovaném vývojovém prostředí:

```
python quickstart.py
```

Následujících 15 sekund hlasového vstupu z mikrofonu se rozpozná a zaprotokoluje v okně konzoly.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]
---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 45ae268e51aba4ef28bbbb6cd9d055c48b65be5e
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671215"
---
## <a name="prerequisites"></a>Požadavky

Než začnete:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?pivots=programming-language-python" target="_blank">Nainstalujte sadu Speech SDK pro vývojové<span class="docon docon-navigate-external x-hidden-focus"></span>prostředí a vytvořte prázdný ukázkový projekt</a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Vytvoření aplikace LUIS pro rozpoznávání záměru

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>Otevření projektu

1. Otevřete upřednostňované ide.
2. Vytvořte nový projekt a `quickstart.py`vytvořte soubor s názvem a otevřete jej.

## <a name="start-with-some-boilerplate-code"></a>Začněte s nějakým standardním kódem

Přidáme nějaký kód, který funguje jako kostra pro náš projekt.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Před inicializací objektu `IntentRecognizer` je třeba vytvořit konfiguraci, která používá klíč a umístění pro prostředek předpovědi LUIS.

Vložte tento `quickstart.py`kód do . Ujistěte se, že aktualizujete tyto hodnoty:

* Nahraďte `"YourLanguageUnderstandingSubscriptionKey"` pomocí predikčního klíče LUIS.
* Nahraďte `"YourLanguageUnderstandingServiceRegion"` umístění luis. Použít **identifikátor oblasti** z [oblasti](https://aka.ms/speech/sdkregion)

>[!TIP]
> Pokud potřebujete pomoc s hledáním těchto hodnot, přečtěte si informace [o vytvoření aplikace LUIS pro rozpoznávání záměru](#create-a-luis-app-for-intent-recognition).

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

Tato ukázka `SpeechConfig` vytvoří objekt pomocí klíče luis a oblasti. Úplný seznam dostupných metod naleznete v tématu [SpeechConfig Class](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig).

Sada Speech SDK bude ve výchozím nastavení rozpoznána použití jazyka en-us, viz [Určení zdrojového jazyka pro řeč na text,](../../../../how-to-specify-source-language.md) kde naleznete informace o výběru zdrojového jazyka.

## <a name="initialize-an-intentrecognizer"></a>Inicializovat rozpoznač záměrů

Nyní vytvoříme . `IntentRecognizer` Vložte tento kód přímo pod konfiguraci řeči.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Přidání languageunderstandingmodel a záměry

Je třeba přidružit `LanguageUnderstandingModel` k nástrojpro rozpoznávání záměru a přidat záměry, které chcete rozpoznat. Použijeme záměry z předem vytvořené domény pro domácí automatizaci.

Vložte tento `IntentRecognizer`kód pod aplikaci . Ujistěte se, `"YourLanguageUnderstandingAppId"` že nahradíte ID aplikace LUIS. 

>[!TIP]
> Pokud potřebujete pomoc s vyhledáním této hodnoty, přečtěte si téma [Vytvoření aplikace LUIS pro rozpoznávání záměru](#create-a-luis-app-for-intent-recognition).

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

## <a name="recognize-an-intent"></a>Rozpoznání záměru

Z `IntentRecognizer` objektu zavoláte metodu. `recognize_once()` Tato metoda umožňuje řeči služby vědět, že odesíláte jednu frázi pro rozpoznávání a že jakmile je fráze identifikována k zastavení rozpoznávání řeči.

Vložte tento kód pod model.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>Zobrazení výsledků rozpoznávání (nebo chyb)

Když je výsledek rozpoznávání vrácen službou Řeč, budete s ním chtít něco udělat. Budeme to jednoduché a vytisknout výsledek na konzoli.

Pod volání `recognize_once()`mj.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>Kontrola kódu

V tomto okamžiku by měl váš kód vypadat takto.

> [!NOTE]
> K této verzi jsme přidali několik komentářů.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>Vytvoření a spuštění aplikace

Spusťte ukázku z konzoly nebo v ide:

```
python quickstart.py
```

Následujících 15 sekund hlasového vstupu z mikrofonu se rozpozná a zaprotokoluje v okně konzoly.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]

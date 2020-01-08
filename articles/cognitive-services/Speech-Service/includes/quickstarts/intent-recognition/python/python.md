---
title: 'Rychlý Start: rozpoznávání řeči, záměrů a entit, Python-Speech Service'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 01/02/2020
ms.topic: include
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 22a40cac59c36e9e0b6b7ef534639748428ae62e
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660464"
---
## <a name="prerequisites"></a>Požadavky

Než začnete:

* Pokud se jedná o váš první projekt v Pythonu, pomocí tohoto průvodce <a href="../quickstarts/create-project.md" target="_blank">vytvořte prázdný ukázkový projekt</a>.
* <a href="../quickstarts/setup-platform.md" target="_blank">Nainstalujte sadu Speech SDK pro vývojové prostředí</a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Vytvoření aplikace v LUIS pro rozpoznávání záměrů

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>Otevřete projekt

1. Otevřete preferované integrované vývojové prostředí (IDE).
2. Vytvořte nový projekt a vytvořte soubor s názvem `quickstart.py`a otevřete ho.

## <a name="start-with-some-boilerplate-code"></a>Začínáme s některým často používaným kódem

Pojďme přidat kód, který funguje jako kostra pro náš projekt.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Předtím, než budete moci inicializovat objekt `IntentRecognizer`, je nutné vytvořit konfiguraci, která používá klíč a umístění prostředku předpovědi LUIS.

Vložte tento kód do `quickstart.py`. Ujistěte se, že tyto hodnoty aktualizujete:

* Nahraďte `"YourLanguageUnderstandingSubscriptionKey"` klíčem předpovědi LUIS. 
* Nahraďte `"YourLanguageUnderstandingServiceRegion"` umístěním LUIS. 

>[!TIP]
> Pokud potřebujete nápovědu k nalezení těchto hodnot, přečtěte si téma [Vytvoření aplikace v Luis pro rozpoznávání záměrů](#create-a-luis-app-for-intent-recognition).

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

Tato ukázka vytvoří objekt `SpeechConfig` pomocí klíče a oblasti LUIS. Úplný seznam dostupných metod naleznete v tématu [Třída SpeechConfig](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig).

Sada Speech SDK bude standardně rozpoznána pomocí en-US pro daný jazyk. informace o výběru zdrojového jazyka najdete v tématu [určení zdrojového jazyka pro převod řeči na text](../../../../how-to-specify-source-language.md) .

## <a name="initialize-an-intentrecognizer"></a>Inicializovat IntentRecognizer

Nyní vytvoříme `IntentRecognizer`. Vložte tento kód přímo pod konfiguraci řeči.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Přidat LanguageUnderstandingModel a záměry

Je potřeba přidružit `LanguageUnderstandingModel` k nástroji pro rozpoznávání záměrů a přidat záměry, které chcete rozpoznat. Budeme používat záměry z předem připravené domény pro automatizaci domů. 

Vložte tento kód pod `IntentRecognizer`. Ujistěte se, že `"YourLanguageUnderstandingAppId"` nahradíte ID aplikace LUIS. 

>[!TIP]
> Pokud potřebujete nápovědu najít tuto hodnotu, přečtěte si téma [Vytvoření aplikace v Luis pro rozpoznávání záměrů](#create-a-luis-app-for-intent-recognition).

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

## <a name="recognize-an-intent"></a>Rozpoznávání záměru

Z objektu `IntentRecognizer` zavoláte metodu `recognize_once()`. Tato metoda umožňuje službě rozpoznávání řeči zjistit, že posíláte jednoduchou frázi pro rozpoznávání, a že po identifikaci fráze zastavit rozpoznávání řeči.

Vložte tento kód pod model:

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>Zobrazit výsledky rozpoznávání (nebo chyby)

Když Služba rozpoznávání řeči vrátí výsledek rozpoznávání, budete s ním chtít něco dělat. My to Zjednodušme a vytiskneme výsledek do konzoly.

Pod vaším voláním `recognize_once()`přidejte tento kód: [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>Kontrolovat kód

V tomto okamžiku váš kód by měl vypadat takto:  

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

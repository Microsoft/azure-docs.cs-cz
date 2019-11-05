---
title: 'Rychlý Start: rozpoznávání řeči, záměrů a entit, Python-Speech Service'
titleSuffix: Azure Cognitive Services
description: Bude doplněno
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 8916cb0438f0005d3ff9f720cf1b5a5653eeac77
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504029"
---
## <a name="prerequisites"></a>Požadavky

Než začnete, nezapomeňte:

> [!div class="checklist"]
> * [Vytvoření prostředku Azure Speech](../../../../get-started.md)
> * [Vytvoření aplikace LUIS a získání klíče koncového bodu](../../../../quickstarts/create-luis.md)
> * [Nastavení vývojového prostředí](../../../../quickstarts/setup-platform.md)
> * [Vytvořit prázdný ukázkový projekt](../../../../quickstarts/create-project.md)

## <a name="open-your-project"></a>Otevřete projekt

Otevřete Quickstart.py v editoru Pythonu.

## <a name="start-with-some-boilerplate-code"></a>Začínáme s některým často používaným kódem

Pojďme přidat kód, který funguje jako kostra pro náš projekt.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Předtím, než budete moci inicializovat objekt `IntentRecognizer`, je nutné vytvořit konfiguraci, která používá Endpoing klíč a oblast LUIS. Vložte tento kód dál.

Tato ukázka vytvoří objekt `SpeechConfig` pomocí klíče a oblasti LUIS. Úplný seznam dostupných metod naleznete v tématu [Třída SpeechConfig](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig).

> [!NOTE]
> Je důležité použít klíč koncového bodu LUIS a ne počáteční nebo Authroing klíče, protože pro rozpoznávání řeči je platný jenom klíč koncového bodu. Pokyny, jak získat správný klíč, najdete v tématu [Vytvoření aplikace v Luis a získání klíče koncového bodu](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md) .

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

## <a name="initialize-a-intentrecognizer"></a>Inicializovat IntentRecognizer

Nyní vytvoříme `IntentRecognizer`. Vložte tento kód přímo pod konfiguraci řeči.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Přidat LanguageUnderstandingModel a záměry

Teď je potřeba přidružit `LanguageUnderstandingModel` k nástroji pro rozpoznávání záměrů a přidat záměry, které chcete rozpoznat.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

## <a name="recognize-an-intent"></a>Rozpoznávání záměru

Z objektu `IntentRecognizer` zavoláte metodu `recognize_once()`. Tato metoda umožňuje službě rozpoznávání řeči zjistit, že posíláte jednoduchou frázi pro rozpoznávání, a že jakmile je fráze identifikována pro zastavení reconizing řeči.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>Zobrazit výsledky rozpoznávání (nebo chyby)

Když Služba rozpoznávání řeči vrátí výsledek rozpoznávání, budete s ním chtít něco dělat. My to Zjednodušme a vytiskneme výsledek do konzoly.

V příkazu Using pod vaším voláním `recognize_once()`přidejte tento kód: [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>Kontrolovat kód

V tomto okamžiku by váš kód měl vypadat takto: (do této verze jsme přidali nějaké komentáře) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>Sestavení a spuštění aplikace

Spusťte ukázku z konzoly nebo v integrovaném vývojovém prostředí:

    ````
    python quickstart.py
    ````

Následujících 15 sekund hlasového vstupu z mikrofonu se rozpozná a zaprotokoluje v okně konzoly.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]

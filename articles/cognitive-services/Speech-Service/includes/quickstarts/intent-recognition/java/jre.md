---
title: 'Rychlý Start: rozpoznávání řeči, záměrů a entit, Java-Speech Service'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 9d847bbc27710a510564363281b65ac57f57ed7e
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795693"
---
## <a name="prerequisites"></a>Předpoklady

Než začnete, nezapomeňte:

> [!div class="checklist"]
>
> * [Vytvoření prostředku Azure Speech](../../../../get-started.md)
> * [Vytvoření aplikace Language Understanding (LUIS) a získání klíče koncového bodu](../../../../quickstarts/create-luis.md)
> * [Nastavení vývojového prostředí](../../../../quickstarts/setup-platform.md?tabs=jre)
> * [Vytvořit prázdný ukázkový projekt](../../../../quickstarts/create-project.md?tabs=jre)

## <a name="open-your-project"></a>Otevřete projekt

Načtěte projekt a otevřete `Main.java`.

## <a name="start-with-some-boilerplate-code"></a>Začínáme s některým často používaným kódem

Pojďme přidat kód, který funguje jako kostra pro náš projekt.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-20,69-76)]

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Předtím, než budete moci inicializovat objekt `IntentRecognizer`, je nutné vytvořit konfiguraci, která používá klíč a oblast koncového bodu LUIS. Vložit tento kód do bloku try/catch v Main

Tato ukázka používá metodu `FromSubscription()` k sestavení `SpeechConfig`. Úplný seznam dostupných metod naleznete v tématu [Třída SpeechConfig](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).
Sada Speech SDK bude standardně rozpoznána pomocí en-US pro daný jazyk. informace o výběru zdrojového jazyka najdete v tématu [určení zdrojového jazyka pro převod řeči na text](../../../../how-to-specify-source-language.md) .

> [!NOTE]
> Je důležité použít klíč koncového bodu LUIS a ne počáteční nebo zdrojové klíče, protože pro rozpoznávání řeči je platný jenom klíč koncového bodu. Pokyny, jak získat správný klíč, najdete v tématu [Vytvoření aplikace v Luis a získání klíče koncového bodu](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md) .

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=27)]

## <a name="initialize-an-intentrecognizer"></a>Inicializovat IntentRecognizer

Nyní vytvoříme `IntentRecognizer`. Vložte tento kód přímo pod konfiguraci řeči.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=30)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Přidat LanguageUnderstandingModel a záměry

Teď je potřeba přidružit `LanguageUnderstandingModel` k nástroji pro rozpoznávání záměrů a přidat záměry, které chcete rozpoznat.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=33-36)]

## <a name="recognize-an-intent"></a>Rozpoznávání záměru

Z objektu `IntentRecognizer` zavoláte metodu `recognizeOnceAsync()`. Tato metoda umožňuje službě rozpoznávání řeči zjistit, že posíláte jednoduchou frázi pro rozpoznávání, a že po identifikaci fráze zastavit rozpoznávání řeči.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=41)]

## <a name="display-the-recognition-results-or-errors"></a>Zobrazit výsledky rozpoznávání (nebo chyby)

Když Služba rozpoznávání řeči vrátí výsledek rozpoznávání, budete s ním chtít něco dělat. My to Zjednodušme a vytiskneme výsledek do konzoly.

Pod vaším voláním `recognizeOnceAsync()`přidejte tento kód: [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=44-65)]

## <a name="release-resources"></a>Uvolnit prostředky

Je důležité, abyste prostředky řeči uvolnili, až je budete používat. Vložte tento kód na konec bloku try/catch: [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=67-68)]

## <a name="check-your-code"></a>Kontrolovat kód

V tomto okamžiku váš kód by měl vypadat takto:  
(Do této verze jsme přidali nějaké komentáře) [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-76)]

## <a name="build-and-run-your-app"></a>Sestavení a spuštění aplikace

Stiskněte F11 nebo vyberte **Run** (Spustit)  >  **Debug** (Ladit).
Následujících 15 sekund hlasového vstupu z mikrofonu se rozpozná a zaprotokoluje v okně konzoly.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]
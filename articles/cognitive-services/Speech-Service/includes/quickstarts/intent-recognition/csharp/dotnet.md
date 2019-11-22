---
title: 'Rychlý Start: rozpoznávání řeči, záměrů a entit, C# služba Speech'
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
ms.openlocfilehash: 599f59df1a18dfe77d66c497c52a05d7d2e3b6d6
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280430"
---
## <a name="prerequisites"></a>Požadavky

Než začnete, nezapomeňte:

> [!div class="checklist"]
>
> * [Vytvoření prostředku Azure Speech](../../../../get-started.md)
> * [Vytvoření aplikace Language Understanding (LUIS) a získání klíče koncového bodu](../../../../quickstarts/create-luis.md)
> * [Nastavení vývojového prostředí](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Vytvořit prázdný ukázkový projekt](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>Otevřete projekt v sadě Visual Studio

Prvním krokem je ujistit se, že máte projekt otevřený v aplikaci Visual Studio.

1. Spusťte Visual Studio 2019.
2. Načtěte projekt a otevřete `Program.cs`.

## <a name="start-with-some-boilerplate-code"></a>Začínáme s některým často používaným kódem

Pojďme přidat kód, který funguje jako kostra pro náš projekt. Nezapomeňte, že jste vytvořili asynchronní metodu nazvanou `RecognizeIntentAsync()`.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-17,77-86)]

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Předtím, než budete moci inicializovat objekt `IntentRecognizer`, je nutné vytvořit konfiguraci, která používá klíč a oblast koncového bodu LUIS. Vložte tento kód do metody `RecognizeIntentAsync()`.

Tato ukázka používá metodu `FromSubscription()` k sestavení `SpeechConfig`. Úplný seznam dostupných metod naleznete v tématu [Třída SpeechConfig](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).

> [!NOTE]
> Je důležité použít klíč koncového bodu LUIS a ne počáteční nebo zdrojové klíče, protože pro rozpoznávání řeči je platný jenom klíč koncového bodu. Pokyny, jak získat správný klíč, najdete v tématu [Vytvoření aplikace v Luis a získání klíče koncového bodu](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md) .

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=26)]

## <a name="initialize-an-intentrecognizer"></a>Inicializovat IntentRecognizer

Nyní vytvoříme `IntentRecognizer`. Tento objekt je vytvořen v rámci příkazu Using, aby bylo zajištěno správné vydání nespravovaných prostředků. Vložte tento kód do metody `RecognizeIntentAsync()` hned pod konfigurací řeči.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=28-30,76)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Přidat LanguageUnderstandingModel a záměry

Teď je potřeba přidružit `LanguageUnderstandingModel` k nástroji pro rozpoznávání záměrů a přidat záměry, které chcete rozpoznat.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=31-35)]

## <a name="recognize-an-intent"></a>Rozpoznávání záměru

Z objektu `IntentRecognizer` zavoláte metodu `RecognizeOnceAsync()`. Tato metoda umožňuje službě rozpoznávání řeči zjistit, že posíláte jednoduchou frázi pro rozpoznávání, a že po identifikaci fráze zastavit rozpoznávání řeči.

V příkazu Using přidejte tento kód: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=46)]

## <a name="display-the-recognition-results-or-errors"></a>Zobrazit výsledky rozpoznávání (nebo chyby)

Když Služba rozpoznávání řeči vrátí výsledek rozpoznávání, budete s ním chtít něco dělat. My to Zjednodušme a vytiskneme výsledek do konzoly.

V příkazu Using níže `RecognizeOnceAsync()`přidejte tento kód: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=48-75)]

## <a name="check-your-code"></a>Kontrolovat kód

V tomto okamžiku váš kód by měl vypadat takto:  
(Do této verze jsme přidali nějaké komentáře) [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=5-86)]

## <a name="build-and-run-your-app"></a>Sestavení a spuštění aplikace

Nyní jste připraveni sestavit aplikaci a otestovat rozpoznávání řeči pomocí služby Speech.

1. **Zkompilujte kód** -z panelu nabídek v aplikaci Visual Studio, vyberte **sestavení** **řešení**Build > .
2. **Spusťte aplikaci** – z panelu nabídek zvolte možnost **ladění** > **Spustit ladění** nebo stiskněte klávesu **F5**.
3. **Spustit rozpoznávání** – zobrazí výzvu k vymluvenému vynechání fráze v angličtině. Váš hlas se odešle službě Speech, přepisu jako text a vykreslí se v konzole nástroje.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]

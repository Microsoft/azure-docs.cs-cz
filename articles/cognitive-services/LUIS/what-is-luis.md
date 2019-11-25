---
title: Co je Language Understanding (LUIS)?
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) je cloudová služba API, která prostřednictvím vlastních inteligentních funkcí strojového učení, které používá na konverzační text uživatele v přirozeném jazyce, předvídá celkový význam a vytahuje z něj relevantní podrobné informace.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: 99f312521727658788e96a57b619a7c0e3d4751b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456570"
---
# <a name="what-is-language-understanding-luis"></a>Co je Language Understanding (LUIS)?

Language Understanding (LUIS) is a cloud-based API service that applies custom machine-learning intelligence to natural language text to predict overall meaning, and pull out relevant, detailed information. 

For example, when a client application sends the text, `find me a wireless keyboard for $30`, LUIS responds with the following JSON object. 

```JSON
{
    "query": "find me a wireless keyboard for $30",
    "prediction": {
        "topIntent": "Finditem",
        "intents": {
            "Finditem": {
                "score": 0.934672
            }
        },
        "entities": {
            "item": [
                "wireless keyboard"
            ],
            "money": [
        {
            "number": 30,
            "units": "Dollar"
        }
           ]
        }
        
    }
}
```
In the example above, the _**intent**_ , or overall meaning of the phrase is that the user is trying to find an item. The detailed pieces of information that LUIS extracts are called _**entities**_ . In this case, the entities are the name of the item the user is looking for and the amount of money they want to spend.

Client applications use LUIS's returned JSON, the _intent_ (category), and _entities_ (extracted detailed information), to drive actions in the client application. A client application for LUIS is often a conversational application that communicates with a user in natural language to complete a task. Mezi příklady klientských aplikací patří aplikace sociálních médií, chatboti a desktopové aplikace s podporou hlasových služeb. 

![Conceptual image of 3 client applications working with Cognitive Services Language Understanding (LUIS)](./media/luis-overview/luis-entry-point.png "Conceptual image of 3 client applications working with Cognitive Services Language Understanding (LUIS)")

## <a name="example-use-luis-in-a-chat-bot"></a>Example use LUIS in a chat bot

<a name="Accessing-LUIS"></a>

A client application sends utterances (text) to the published LUIS natural language processing endpoint [API][endpoint-apis] and receives the results as JSON responses. Běžnou klientskou aplikace pro službu LUIS je chatbot.


![Conceptual imagery of LUIS working with Chat bot to predict user text with natural language understanding (NLP)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Conceptual imagery of LUIS working with Chat bot to predict user text with natural language understanding (NLP")

|Krok|Akce|
|:--|:--|
|1\. místo|Klientská aplikace odešle _promluvu_ uživatele (text napsaný vlastními slovy): „I want to call my HR rep“ (Chci zavolat zástupci z oddělení lidských zdrojů). do koncového bodu služby LUIS jako požadavek HTTP.|
|2|LUIS applies machine learned language models to the user's unstructured input text and returns a JSON-formatted response, with a top intent, `HRContact`. Odpověď koncového bodu JSON obsahuje minimálně promluvu dotazu a záměr s nejvyšším skóre. It can also extract data such as the _Contact Type_ entity.|
|3|Klientská aplikace se na základě odpovědi ve formátu JSON rozhoduje, jak splnit požadavky uživatele. These decisions can include a decision tree in the bot and calls to other services. |

Aplikace LUIS poskytuje klientské aplikaci informace, které jí umožní chytře rozhodovat. Služba LUIS toto rozhodování nezajišťuje. 

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Zpracování přirozeného jazyka

Your LUIS app contains domain-specific natural language models, which work together. You can start the LUIS app with one or more prebuilt models, build your own model, or blend prebuilt models with your own custom information.

* **Prebuilt model** LUIS has many prebuilt domains that include intent and entity models that work together to complete common usage scenarios. These domains include labeled utterances that can be inspected and edited, allowing you to customize them. [Předem připravené doménové modely](luis-how-to-use-prebuilt-domains.md) zahrnují celý návrh a představují skvělý způsob, jak rychle začít používat službu LUIS. In addition, there are prebuilt entities such as currency and number that you can use independently from the prebuilt domains.

* **Custom model** LUIS gives you several ways to build your own custom models including intents, and entities. Entities include machine-learned entities, pattern matching entities, and a combination of machine-learned and pattern matching.

## <a name="build-the-luis-app"></a>Build the LUIS app
Build the app with the [authoring](https://go.microsoft.com/fwlink/?linkid=2092087) APIs or with the [LUIS portal](https://www.luis.ai).

The LUIS app begins with categories of input text called **[intents](luis-concept-intent.md)** . Každý záměr potřebuje příklady **[promluv](luis-concept-utterance.md)** uživatele. Each utterance can provide data that needs to be extracted. 

|Příklad promluvy uživatele|Záměr|Extracted data|
|-----------|-----------|-----------|
|`Book a flight to __Seattle__?`|BookFlight (Rezervovat let)|Seattle|
|`When does your store __open__?`|StoreHoursAndLocation (Poloha a otevírací doba obchodu)|open (otevírá)|
|`Schedule a meeting at __1pm__ with __Bob__ in Distribution`|ScheduleMeeting (Naplánovat schůzku)|1pm, Bob (13:00, Bob)|

## <a name="query-prediction-endpoint"></a>Koncový bod předpovědi dotazů

After your app is trained and published to the endpoint, the client application sends utterances to the prediction [endpoint](https://go.microsoft.com/fwlink/?linkid=2092356) API. The API applies the app to the utterance for analysis and responds with the prediction results in a JSON format.  

Odpověď koncového bodu JSON obsahuje minimálně promluvu dotazu a záměr s nejvyšším skóre. It can also extract data such as the following **Contact Type** entity and overall sentiment. 

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
        "normalizedQuery": "i want to call my hr rep",
        "topIntent": "HRContact",
        "intents": {
            "HRContact": {
                "score": 0.8582669
            }
        },
        "entities": {
            "Contact Type": [
                "call"
            ]
        },
        "sentiment": {
            "label": "negative",
            "score": 0.103343368
        }
    }
}
```

## <a name="improve-model-prediction"></a>Vylepšení předpovědí modelu

After your LUIS app is published and receives real user utterances, LUIS provides [active learning](luis-concept-review-endpoint-utterances.md) of endpoint utterances to improve prediction accuracy. 

<a name="using-luis"></a>

## <a name="iterative-development-lifecycle"></a>Iterative development lifecycle
LUIS provides tools, versioning, and collaboration with other LUIS authors to integrate into the full iterative [development life cycle](luis-concept-app-iteration.md). 

## <a name="implementing-luis"></a>Implementace služby LUIS
Language Understanding (LUIS), as a REST API, can be used with any product, service, or framework with an HTTP request. Následující seznam obsahuje produkty a služby Microsoftu nejčastěji používané se službou LUIS.

Hlavní klientská aplikace služby LUIS je následující:
* [Robot webové aplikace](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) umožňující rychlé vytvoření chatbota s podporou služby LUIS, který bude s uživatelem komunikovat prostřednictvím textového vstupu. Uses [Bot Framework][bot-framework] version [4.x](https://github.com/Microsoft/botbuilder-dotnet) for a complete bot experience.

Nástroje pro rychlé a snadné používání služby LUIS s využitím robota:
* [LUIS CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) The NPM package provides authoring and prediction with as either a stand-alone command line tool or as import. 
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) – LUISGen je nástroj pro generování zdrojového kódu C# se silnými typy a zdrojového kódu TypeScript z exportovaného modelu LUIS.
* [Dispatch](https://aka.ms/dispatch-tool) umožňuje používat z nadřazené aplikace několik aplikací LUIS a QnA Maker s využitím modelu dispečera.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) – LUDown je nástroj příkazového řádku, který pomáhá spravovat jazykové modely pro vašeho robota.
* [Bot framework - Composer](https://github.com/microsoft/BotFramework-Composer) - an integrated development tool for developers and multi-disciplinary teams to build bots and conversational experiences with the Microsoft Bot Framework

Další služby Cognitive Services používané se službou LUIS:
* [QnA Maker][qnamaker] allows several types of text to combine into a question and answer knowledge base.
* [Služba Speech](../Speech-Service/overview.md) převádí mluvené požadavky na text. 
* [Conversation learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) umožňuje ve službě LUIS rychleji vytvářet konverzace chatbota.

Samples using LUIS:
* [Conversational AI](https://github.com/Microsoft/AI) GitHub repository.
* [Bot framework - Bot samples](https://github.com/microsoft/BotBuilder-Samples)

## <a name="next-steps"></a>Další kroky

* [Co je nového](whats-new.md)
* Author a new LUIS app with a [prebuilt](luis-get-started-create-app.md) or [custom](luis-quickstart-intents-only.md) domain
* [Odešlete dotaz na koncový bod předpovědi](luis-get-started-get-intent-from-browser.md) veřejné aplikace IoT. 
* [Developer resources](developer-reference-resource.md) for LUIS. 

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/

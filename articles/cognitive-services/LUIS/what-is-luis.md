---
title: Co je Language Understanding (LUIS) – Azure Cognitive Services | Microsoft Docs
description: Language Understanding (LUIS) je cloudová služba API, která prostřednictvím vlastních inteligentních funkcí strojového učení, které používá na konverzační text uživatele v přirozeném jazyce, předvídá celkový význam a vytahuje z něj relevantní podrobné informace. Klientskou aplikací je pro LUIS jakákoli konverzační aplikace, která s uživatelem při dokončování úloh komunikuje v přirozeném jazyce. Mezi příklady klientských aplikací patří aplikace sociálních médií, chatboti a desktopové aplikace s podporou hlasových služeb.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: overview
ms.date: 08/15/2018
ms.author: diberry
ms.openlocfilehash: a8e9deb7c677d04634b223045adc2d31fa74ba6e
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033035"
---
# <a name="what-is-language-understanding-luis"></a>Co je Language Understanding (LUIS)?

Language Understanding (LUIS) je cloudová služba API, která prostřednictvím vlastních inteligentních funkcí strojového učení, které používá na konverzační text uživatele v přirozeném jazyce, předvídá celkový význam a vytahuje z něj relevantní podrobné informace. 

Klientskou aplikací je pro LUIS jakákoli konverzační aplikace, která s uživatelem při dokončování úloh komunikuje v přirozeném jazyce. Mezi příklady klientských aplikací patří aplikace sociálních médií, chatboti a desktopové aplikace s podporou hlasových služeb.  

![Koncepční obrázek 3 klientských aplikací pracujících se službou Cognitive Services Language Understanding (LUIS)](./media/luis-overview/luis-entry-point.png "Koncepční obrázek 3 klientských aplikací pracujících se službou Cognitive Services Language Understanding (LUIS)")

## <a name="use-luis-in-a-chat-bot"></a>Použití služby LUIS v chatbotu

<a name="Accessing-LUIS"></a>

Po publikování aplikace LUIS může klientská aplikace odesílat promluvy (text) do rozhraní [API][endpoint-apis] koncového bodu služby LUIS pro zpracování přirozeného jazyka a přijímat z něj výsledky v podobě odpovědí ve formátu JSON. Běžnou klientskou aplikace pro službu LUIS je chatbot.


![Koncepční obrázek spolupráce služby LUIS s chatbotem na předvídání významu textu uživatele s využitím rozpoznávání přirozeného jazyka](./media/luis-overview/luis-overview-process-2.png "Koncepční obrázek spolupráce služby LUIS s chatbotem na předvídání významu textu uživatele s využitím rozpoznávání přirozeného jazyka")

|Krok|Akce|
|:--|:--|
|1|Klientská aplikace odešle _promluvu_ uživatele (text napsaný vlastními slovy): „I want to call my HR rep“ (Chci zavolat zástupci z oddělení lidských zdrojů). do koncového bodu služby LUIS jako požadavek HTTP.|
|2|Služba LUIS na text v přirozeném jazyce použije naučený model, který jí poskytne inteligentní porozumění vstupu uživatele. Služba LUIS vrátí odpověď ve formátu JSON s hlavním záměrem HRContact (Kontaktovat oddělení lidských zdrojů). Odpověď koncového bodu JSON obsahuje minimálně promluvu dotazu a záměr s nejvyšším skóre. Také může extrahovat data, jako je entita Contact Type (Typ kontaktu).|
|3|Klientská aplikace se na základě odpovědi ve formátu JSON rozhoduje, jak splnit požadavky uživatele. Toto rozhodování může zahrnovat určitý rozhodovací strom v kódu architektury chatbota nebo volání dalších služeb. |

Aplikace LUIS poskytuje klientské aplikaci informace, které jí umožní chytře rozhodovat. Služba LUIS toto rozhodování nezajišťuje. 

<!--

### Example of JSON endpoint response

The minimum JSON endpoint response contains the query utterance, and the top scoring intent. It can also extract data such as the following **Contact Type** entity. 

```JSON
{
  "query": "I want to call my HR rep.",
  "topScoringIntent": {
    "intent": "HRContact",
    "score": 0.921233
  },
  "entities": [
    {
      "entity": "call",
      "type": "Contact Type",
      "startIndex": 10,
      "endIndex": 13,
      "score": 0.7615982
    }
  ]
}
```
-->

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Zpracování přirozeného jazyka

Aplikace LUIS obsahuje model zpracování přirozeného jazyka specifický pro doménu. Aplikaci LUIS můžete spustit s předem připraveným doménovým modelem, sestavit pro ni vlastní model nebo zkombinovat části předem připravené domény s vlastními informacemi.

* **Předem připravený model:** Služba LUIS obsahuje řadu předem připravených doménových modelů, včetně záměrů, promluv a předem připravených entit. Předem připravené entity můžete použít, aniž byste museli použít záměry a promluvy předem připraveného modelu. [Předem připravené doménové modely](luis-how-to-use-prebuilt-domains.md) zahrnují celý návrh a představují skvělý způsob, jak rychle začít používat službu LUIS.

* **Vlastní entity:** Služba LUIS nabízí několik způsobů, jak identifikovat vlastní záměry a entity, včetně strojově naučených entit, konkrétních entit nebo entit literálů a kombinace strojově naučených entit a entit literálů.

## <a name="build-the-luis-model"></a>Sestavení modelu LUIS
Model můžete sestavit pomocí rozhraní API pro [vytváření](https://aka.ms/luis-authoring-apis) nebo portálu služby LUIS.

Model LUIS začíná kategoriemi záměrů uživatele, které se označují jako **[záměry](luis-concept-intent.md)**. Každý záměr potřebuje příklady **[promluv](luis-concept-utterance.md)** uživatele. Každá promluva může poskytovat různá data, která je potřeba extrahovat pomocí **[entit](luis-concept-entity-types.md)**. 

|Příklad promluvy uživatele|Záměr|Entity|
|-----------|-----------|-----------|
|„Book a flight to __Seattle__?“ (Rezervace letu do Seattlu?)|BookFlight (Rezervovat let)|Seattle|
|„When does your store __open__?“ (Kdy otevírá váš obchod?)|StoreHoursAndLocation (Poloha a otevírací doba obchodu)|open (otevírá)|
|„Schedule a meeting at __1pm__ with __Bob__ in Distribution“ (Naplánovat na 13:00 schůzku s Bobem v oddělení distribuce)|ScheduleMeeting (Naplánovat schůzku)|1pm, Bob (13:00, Bob)|

<!--
## What is a natural language model?

A model begins with a list of general user intentions, called _intents_, such as "Book Flight" or "Contact Help Desk." You provide user's example text, called _example utterances_ for the intents. Then mark significant words or phrases in the utterance, called _entities_.


A model includes:

* **[intents](#intents)**: categories of user intentions (overall intended action or result)
* **[entities](#entities)**: specific types of data in utterances such as number, email, or name contained in text
* **[example utterances](#example-utterances)**: example text a user might enter in the client application

### Intents 

An [intent](luis-how-to-add-intents.md), short for _intention_, is a purpose or goal expressed in a user's utterance, such as booking a flight, paying a bill, or finding a news article. You create an intent for each action. A LUIS travel app may define an intent named "BookFlight." Each prediction query includes the top scored intent. 

The client application can use the top scoring intent to trigger an action. For example, when "BookFlight" intent is returned from LUIS, a client application could trigger an API call to an external service for booking a plane ticket.

### Entities

An [entity](luis-how-to-add-entities.md) represents detailed information found within the utterance that is relevant to the user's request. For example, in the utterance "Book a ticket to Paris",  a single ticket is requested, and "Paris" is a location. Two entities are found "a ticket" indicating a single ticket and "Paris" indicating the destination. 

After LUIS returns the entities found in the user’s utterance, the client application can use the list of entities as parameters to trigger an action. For example, booking a flight requires entities like the travel destination, date, and airline.
-->
<!--
### Example utterances

An example [utterance](luis-how-to-add-example-utterances.md) is text input from the user that the client application needs to understand. It may be a sentence, like "Book a ticket to Paris", or a fragment of a sentence, like "Booking" or "Paris flight." Utterances aren't always well-formed, and there can be many utterance variations for a particular intent. Add 10 to 20 example utterances to each intent and mark entities in every utterance.

|Example user utterance|Intent|Entities|
|-----------|-----------|-----------|
|"Book a flight to __Seattle__?"|BookFlight|Seattle|
|"When does your store __open__?"|StoreHoursAndLocation|open|
|"Schedule a meeting at __1pm__ with __Bob__ in Distribution"|ScheduleMeeting|1pm, Bob|
-->
## <a name="query-prediction-endpoint"></a>Koncový bod předpovědi dotazů

Po sestavení a publikování modelu v koncovém bodu může klientská aplikace odesílat promluvy do publikovaného rozhraní API [koncového bodu](https://aka.ms/luis-endpoint-apis) předpovědi. Rozhraní API pomocí modelu analyzuje text. Rozhraní API odešle odpověď s výsledky předpovědi ve formátu JSON.  

Odpověď koncového bodu JSON obsahuje minimálně promluvu dotazu a záměr s nejvyšším skóre. Může extrahovat také data, jako je následující entita **Contact Type** (Typ kontaktu). 

```JSON
{
  "query": "I want to call my HR rep.",
  "topScoringIntent": {
    "intent": "HRContact",
    "score": 0.921233
  },
  "entities": [
    {
      "entity": "call",
      "type": "Contact Type",
      "startIndex": 10,
      "endIndex": 13,
      "score": 0.7615982
    }
  ]
}
```

## <a name="improve-model-prediction"></a>Vylepšení předpovědí modelu

Jakmile je model LUIS publikovaný a přijímá skutečné promluvy uživatelů, poskytuje služba LUIS několik metod, jak zlepšit přesnost předpovědí: [aktivní učení](#active-learning) promluv koncového bodu, [seznamy frází](#phrase-lists) pro zahrnutí doménových slov a [vzory](#patterns) pro snížení počtu potřebných promluv.
<!--
### Active learning

In the [active learning](luis-how-to-review-endoint-utt.md) process, LUIS allows you to adapt the LUIS app to real-world utterances by selecting utterances it received at the endpoint for your review. You can accept or correct the endpoint prediction, retrain, and republish. LUIS learns quickly with this iterative process, taking the minimum amount of your time and effort. 

### Phrase lists 

LUIS provides [phrases lists](luis-concept-feature.md) so you can indicate important words or phrases of the model. LUIS uses these lists to add additional significance to those words and phrases that would otherwise not be found in the model.

### Patterns 

Patterns allow you to simplify an intent's utterance collection into common [templates](luis-concept-patterns.md) of word choice and word order. This allows LUIS to learn quicker by needing fewer example utterances for the intents. Patterns are a hybrid system of regular expressions and machine-learned expressions. 
-->
<a name="using-luis"></a>
<!--
## Authoring and accessing models
Author LUIS from the [authoring](https://aka.ms/luis-authoring-apis) APIs or from the LUIS portal. Query the published prediction endpoint of the model from the [endpoint](https://aka.ms/luis-endpoint-apis) APIs.
-->

## <a name="integrating-with-luis"></a>Integrace se službou LUIS
Služba LUIS jako rozhraní REST API umožňuje použití s jakýmkoli produktem, službou nebo architekturou odesílající požadavky HTTP. Následující seznam obsahuje produkty a služby Microsoftu nejčastěji používané se službou LUIS.

Mezi klientské aplikace Microsoftu pro službu LUIS patří:
* [Robot webové aplikace](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-3.0) umožňující rychlé vytvoření chatbota s podporou služby LUIS, který bude s uživatelem komunikovat prostřednictvím textového vstupu. Úplné funkce chatbota zajišťuje rozhraní [Bot Framework][bot-framework] verze [3.x](https://github.com/Microsoft/BotBuilder) nebo [4.x](https://github.com/Microsoft/botbuilder-dotnet).
* [Windows Mixed Reality](https://docs.microsoft.com/windows/mixed-reality/) – Další informace najdete v tomto [kurzu smíšené reality](https://docs.microsoft.com/windows/mixed-reality/mr-azure-303) s využitím služby LUIS. 

Nástroje Microsoftu pro použití služby LUIS v chatbotu:
* [Dispatch](https://aka.ms/dispatch-tool) umožňuje používat z nadřazené aplikace několik aplikací LUIS a QnA Maker s využitím modelu dispečera.
* [Conversation learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) umožňuje ve službě LUIS rychleji vytvářet konverzace chatbota.
* [Project personality chat](https://docs.microsoft.com/azure/cognitive-services/project-personality-chat/overview) zpracovává nezávaznou konverzaci chatbota.

Další služby Cognitive Services používané se službou LUIS:
* [QnA Maker][qnamaker] umožňuje kombinovat několik typů textu do znalostní báze otázek a odpovědí.
* [Rozhraní API Bingu pro kontrolu pravopisu](../bing-spell-check/proof-text.md) zajišťuje opravu textu před vytvořením předpovědi. 
* [Služba Speech](../Speech-Service/overview.md) převádí mluvené požadavky na text. 



## <a name="next-steps"></a>Další kroky

Vytvořte novou aplikaci LUIS s využitím [předem připravené](luis-get-started-create-app.md) nebo [vlastní](luis-quickstart-intents-only.md) domény. [Odešlete dotaz na koncový bod předpovědi](luis-get-started-cs-get-intent.md) veřejné aplikace IoT.

<!-- Reference-style links -->
[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://aka.ms/luis-authoring-api
[endpoint-apis]: https://aka.ms/luis-endpoint-apis
[qnamaker]: https://qnamaker.ai/
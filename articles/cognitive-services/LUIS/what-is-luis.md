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
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 3d4251613ac2a00ddc56d5e573b49ced01adf61c
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703101"
---
# <a name="what-is-language-understanding-luis"></a>Co je Language Understanding (LUIS)?

Language Understanding (LUIS) je cloudová služba API, která prostřednictvím vlastních inteligentních funkcí strojového učení, které používá na konverzační text uživatele v přirozeném jazyce, předvídá celkový význam a vytahuje z něj relevantní podrobné informace. 

Klientskou aplikací je pro LUIS jakákoli konverzační aplikace, která s uživatelem při dokončování úloh komunikuje v přirozeném jazyce. Mezi příklady klientských aplikací patří aplikace sociálních médií, chatboti a desktopové aplikace s podporou hlasových služeb.  

![Koncepční obrázek 3 klientských aplikací pracujících se službou Cognitive Services Language Understanding (LUIS)](./media/luis-overview/luis-entry-point.png "Koncepční obrázek 3 klientských aplikací pracujících se službou Cognitive Services Language Understanding (LUIS)")

## <a name="use-luis-in-a-chat-bot"></a>Použití služby LUIS v chatbotu

<a name="Accessing-LUIS"></a>

Po publikování aplikace LUIS pošle klientská aplikace projevy (text) do [rozhraní API][endpoint-apis] koncového bodu zpracování Luis přirozeného jazyka a výsledky obdrží jako odpověď JSON. Běžnou klientskou aplikace pro službu LUIS je chatbot.


![Koncepční obrázek spolupráce služby LUIS s chatbotem na předvídání významu textu uživatele s využitím rozpoznávání přirozeného jazyka](./media/luis-overview/luis-overview-process-2.png "Koncepční obrázek spolupráce služby LUIS s chatbotem na předvídání významu textu uživatele s využitím rozpoznávání přirozeného jazyka")

|Krok|Akce|
|:--|:--|
|1\. místo|Klientská aplikace odešle _promluvu_ uživatele (text napsaný vlastními slovy): „I want to call my HR rep“ (Chci zavolat zástupci z oddělení lidských zdrojů). do koncového bodu služby LUIS jako požadavek HTTP.|
|2|Služba LUIS na text v přirozeném jazyce použije naučený model, který jí poskytne inteligentní porozumění vstupu uživatele. Služba LUIS vrátí odpověď ve formátu JSON s hlavním záměrem HRContact (Kontaktovat oddělení lidských zdrojů). Odpověď koncového bodu JSON obsahuje minimálně promluvu dotazu a záměr s nejvyšším skóre. Také může extrahovat data, jako je entita Contact Type (Typ kontaktu).|
|3|Klientská aplikace se na základě odpovědi ve formátu JSON rozhoduje, jak splnit požadavky uživatele. Toto rozhodování může zahrnovat určitý rozhodovací strom v kódu architektury chatbota nebo volání dalších služeb. |

Aplikace LUIS poskytuje klientské aplikaci informace, které jí umožní chytře rozhodovat. Služba LUIS toto rozhodování nezajišťuje. 

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Zpracování přirozeného jazyka

Aplikace LUIS obsahuje model zpracování přirozeného jazyka specifický pro doménu. Aplikaci LUIS můžete spustit s předem připraveným doménovým modelem, sestavit pro ni vlastní model nebo zkombinovat části předem připravené domény s vlastními informacemi.

* **Předem připravený model:** Služba LUIS obsahuje řadu předem připravených doménových modelů, včetně záměrů, promluv a předem připravených entit. Předem připravené entity můžete použít, aniž byste museli použít záměry a promluvy předem připraveného modelu. [Předem připravené doménové modely](luis-how-to-use-prebuilt-domains.md) zahrnují celý návrh a představují skvělý způsob, jak rychle začít používat službu LUIS.

* **Vlastní entity:** Služba LUIS nabízí několik způsobů, jak identifikovat vlastní záměry a entity, včetně strojově naučených entit, konkrétních entit nebo entit literálů a kombinace strojově naučených entit a entit literálů.

## <a name="build-the-luis-model"></a>Sestavení modelu LUIS
Model můžete sestavit pomocí rozhraní API pro [vytváření](https://go.microsoft.com/fwlink/?linkid=2092087) nebo portálu služby LUIS.

Model LUIS začíná kategoriemi záměrů uživatele, které se označují jako **[záměry](luis-concept-intent.md)** . Každý záměr potřebuje příklady **[promluv](luis-concept-utterance.md)** uživatele. Každá promluva může poskytovat různá data, která je potřeba extrahovat pomocí **[entit](luis-concept-entity-types.md)** . 

|Příklad promluvy uživatele|Záměr|Entity|
|-----------|-----------|-----------|
|„Book a flight to __Seattle__?“ (Rezervace letu do Seattlu?)|BookFlight (Rezervovat let)|Seattle|
|„When does your store __open__?“ (Kdy otevírá váš obchod?)|StoreHoursAndLocation (Poloha a otevírací doba obchodu)|open (otevírá)|
|„Schedule a meeting at __1pm__ with __Bob__ in Distribution“ (Naplánovat na 13:00 schůzku s Bobem v oddělení distribuce)|ScheduleMeeting (Naplánovat schůzku)|1pm, Bob (13:00, Bob)|

## <a name="query-prediction-endpoint"></a>Koncový bod předpovědi dotazů

Po sestavení a publikování modelu v koncovém bodu může klientská aplikace odesílat promluvy do publikovaného rozhraní API [koncového bodu](https://go.microsoft.com/fwlink/?linkid=2092356) předpovědi. Rozhraní API pomocí modelu analyzuje text. Rozhraní API odešle odpověď s výsledky předpovědi ve formátu JSON.  

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

Jakmile je model LUIS publikovaný a přijímá skutečné promluvy uživatelů, poskytuje služba LUIS několik metod, jak zlepšit přesnost předpovědí: [aktivní učení](luis-concept-review-endpoint-utterances.md) promluv koncového bodu, [seznamy frází](luis-concept-feature.md) pro zahrnutí doménových slov a [vzory](luis-concept-patterns.md) pro snížení počtu potřebných promluv.

<a name="using-luis"></a>

## <a name="development-lifecycle"></a>Životní cyklus vývoje
Služba LUIS poskytuje nástroje, správu verzí a spolupráci s jinými autory služby LUIS, čímž umožňuje začlenění do plného životního cyklu vývoje na úrovni klientské aplikace a jazykového modelu. 

## <a name="implementing-luis"></a>Implementace služby LUIS
Služba LUIS jako rozhraní REST API umožňuje použití s jakýmkoli produktem, službou nebo architekturou odesílající požadavky HTTP. Následující seznam obsahuje produkty a služby Microsoftu nejčastěji používané se službou LUIS.

Hlavní klientská aplikace služby LUIS je následující:
* [Robot webové aplikace](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) umožňující rychlé vytvoření chatbota s podporou služby LUIS, který bude s uživatelem komunikovat prostřednictvím textového vstupu. Pro kompletní prostředí robota používá [rozhraní bot Framework][bot-framework] verze [4. x](https://github.com/Microsoft/botbuilder-dotnet) .

Nástroje pro rychlé a snadné používání služby LUIS s využitím robota:
* [Luis CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) Balíček NPM poskytuje vytváření a předpovědi pomocí samostatného nástroje příkazového řádku nebo jako importu. 
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) – LUISGen je nástroj pro generování zdrojového kódu C# se silnými typy a zdrojového kódu TypeScript z exportovaného modelu LUIS.
* [Dispatch](https://aka.ms/dispatch-tool) umožňuje používat z nadřazené aplikace několik aplikací LUIS a QnA Maker s využitím modelu dispečera.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) – LUDown je nástroj příkazového řádku, který pomáhá spravovat jazykové modely pro vašeho robota.

Další služby Cognitive Services používané se službou LUIS:
* [QnA maker][qnamaker] umožňuje zkombinovat několik typů textu do otázek a odpovědí znalostní báze.
* [Rozhraní API Bingu pro kontrolu pravopisu](../bing-spell-check/proof-text.md) zajišťuje opravu textu před vytvořením předpovědi. 
* [Služba Speech](../Speech-Service/overview.md) převádí mluvené požadavky na text. 
* [Conversation learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) umožňuje ve službě LUIS rychleji vytvářet konverzace chatbota.
* [Project personality chat](https://docs.microsoft.com/azure/cognitive-services/project-personality-chat/overview) zpracovává nezávaznou konverzaci chatbota.

Ukázky pomocí LUIS:
* [Konverzační AI](https://github.com/Microsoft/AI) Úložiště GitHub.
* [Language Understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding) Ukázky Azure

## <a name="next-steps"></a>Další kroky

Vytvořte novou aplikaci LUIS s využitím [předem připravené](luis-get-started-create-app.md) nebo [vlastní](luis-quickstart-intents-only.md) domény. [Odešlete dotaz na koncový bod předpovědi](luis-get-started-get-intent-from-browser.md) veřejné aplikace IoT.

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/

---
title: Co je Language Understanding (LUIS) – Azure Cognitive Services | Microsoft Docs
description: Language Understanding (LUIS) je cloudová služba API, která prostřednictvím vlastních inteligentních funkcí strojového učení, které používá na konverzační text uživatele v přirozeném jazyce, předvídá celkový význam a vytahuje z něj relevantní podrobné informace. Klientskou aplikací je pro LUIS jakákoli konverzační aplikace, která s uživatelem při dokončování úloh komunikuje v přirozeném jazyce. Mezi příklady klientských aplikací patří aplikace sociálních médií, chatboti a desktopové aplikace s podporou hlasových služeb.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: overview
ms.date: 12/06/2018
ms.author: diberry
ms.openlocfilehash: ce0dd89a56e94c8ec509a814409ebca8c2853f21
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "53001058"
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

Jakmile je model LUIS publikovaný a přijímá skutečné promluvy uživatelů, poskytuje služba LUIS několik metod, jak zlepšit přesnost předpovědí: [aktivní učení](luis-concept-review-endpoint-utterances.md) promluv koncového bodu, [seznamy frází](luis-concept-feature.md) pro zahrnutí doménových slov a [vzory](luis-concept-patterns.md) pro snížení počtu potřebných promluv.

<a name="using-luis"></a>

## <a name="development-lifecycle"></a>Životní cyklus vývoje
Služba LUIS poskytuje nástroje, správu verzí a spolupráci s jinými autory služby LUIS, čímž umožňuje začlenění do plného životního cyklu vývoje na úrovni klientské aplikace a jazykového modelu. 

## <a name="implementing-luis"></a>Implementace služby LUIS
Služba LUIS jako rozhraní REST API umožňuje použití s jakýmkoli produktem, službou nebo architekturou odesílající požadavky HTTP. Následující seznam obsahuje produkty a služby Microsoftu nejčastěji používané se službou LUIS.

Hlavní klientská aplikace služby LUIS je následující:
* [Robot webové aplikace](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-3.0) umožňující rychlé vytvoření chatbota s podporou služby LUIS, který bude s uživatelem komunikovat prostřednictvím textového vstupu. Úplné funkce chatbota zajišťuje rozhraní [Bot Framework][bot-framework] verze [3.x](https://github.com/Microsoft/BotBuilder) nebo [4.x](https://github.com/Microsoft/botbuilder-dotnet).

Nástroje pro rychlé a snadné používání služby LUIS s využitím robota:
* [Rozhraní příkazového řádku služby LUIS](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) – balíček NPM poskytuje vytváření a předpovědi jak s použitím samostatného nástroje příkazového řádku, tak v rámci importu. 
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) – LUISGen je nástroj pro generování zdrojového kódu C# se silnými typy a zdrojového kódu TypeScript z exportovaného modelu LUIS.
* [Dispatch](https://aka.ms/dispatch-tool) umožňuje používat z nadřazené aplikace několik aplikací LUIS a QnA Maker s využitím modelu dispečera.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) – LUDown je nástroj příkazového řádku, který pomáhá spravovat jazykové modely pro vašeho robota.

Další služby Cognitive Services používané se službou LUIS:
* [QnA Maker][qnamaker] umožňuje kombinovat několik typů textu do znalostní báze otázek a odpovědí.
* [Rozhraní API Bingu pro kontrolu pravopisu](../bing-spell-check/proof-text.md) zajišťuje opravu textu před vytvořením předpovědi. 
* [Služba Speech](../Speech-Service/overview.md) převádí mluvené požadavky na text. 
* [Conversation learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) umožňuje ve službě LUIS rychleji vytvářet konverzace chatbota.
* [Project personality chat](https://docs.microsoft.com/azure/cognitive-services/project-personality-chat/overview) zpracovává nezávaznou konverzaci chatbota.

## <a name="next-steps"></a>Další postup

Vytvořte novou aplikaci LUIS s využitím [předem připravené](luis-get-started-create-app.md) nebo [vlastní](luis-quickstart-intents-only.md) domény. [Odešlete dotaz na koncový bod předpovědi](luis-get-started-cs-get-intent.md) veřejné aplikace IoT.

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://aka.ms/luis-authoring-api
[endpoint-apis]: https://aka.ms/luis-endpoint-apis
[qnamaker]: https://qnamaker.ai/

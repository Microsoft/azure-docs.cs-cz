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
ms.date: 11/04/2019
ms.author: diberry
ms.openlocfilehash: 8ee22a2a8a12eb85439e191bc21e6cf391bea3f8
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73612853"
---
# <a name="what-is-language-understanding-luis"></a>Co je Language Understanding (LUIS)?

Language Understanding (LUIS) je cloudová služba API, která prostřednictvím vlastních inteligentních funkcí strojového učení, které používá na konverzační text uživatele v přirozeném jazyce, předvídá celkový význam a vytahuje z něj relevantní podrobné informace. 

Klientskou aplikací je pro LUIS jakákoli konverzační aplikace, která s uživatelem při dokončování úloh komunikuje v přirozeném jazyce. Mezi příklady klientských aplikací patří aplikace sociálních médií, chatboti a desktopové aplikace s podporou hlasových služeb.  

![Koncepční obrázek 3 klientských aplikací pracujících s Cognitive Services Language Understanding (LUIS)](./media/luis-overview/luis-entry-point.png "Koncepční obrázek 3 klientských aplikací pracujících s Cognitive Services Language Understanding (LUIS)")

## <a name="use-luis-in-a-chat-bot"></a>Použití služby LUIS v chatbotu

<a name="Accessing-LUIS"></a>

Po publikování aplikace LUIS pošle klientská aplikace projevy (text) do [rozhraní API][endpoint-apis] koncového bodu zpracování Luis přirozeného jazyka a výsledky obdrží jako odpověď JSON. Běžnou klientskou aplikace pro službu LUIS je chatbot.


![Koncepční obrázky LUIS pracující s robotem chatu pro předpověď textu uživatele s porozuměním v přirozeném jazyku (NLP)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Koncepční obrázky LUIS pracující s robotem chatu pro předpověď textu uživatele s porozuměním v přirozeném jazyku (NLP")

|Krok|Akce|
|:--|:--|
|1|Klientská aplikace odešle _promluvu_ uživatele (text napsaný vlastními slovy): „I want to call my HR rep“ (Chci zavolat zástupci z oddělení lidských zdrojů). do koncového bodu služby LUIS jako požadavek HTTP.|
|2|LUIS vám umožňuje vytvořit vlastní jazykové modely a přidat do své aplikace informace. Modely jazyků počítačů, které přebírají nestrukturovaný vstupní text uživatele, vrátí odpověď ve formátu JSON s nejvyšším záměrem `HRContact`. Odpověď koncového bodu JSON obsahuje minimálně promluvu dotazu a záměr s nejvyšším skóre. Může také extrahovat data, jako je například entita _typu kontakt_ .|
|3|Klientská aplikace se na základě odpovědi ve formátu JSON rozhoduje, jak splnit požadavky uživatele. Tato rozhodnutí mohou zahrnovat rozhodovací strom v kódu rozhraní bot Framework a volání dalších služeb. |

Aplikace LUIS poskytuje klientské aplikaci informace, které jí umožní chytře rozhodovat. Služba LUIS toto rozhodování nezajišťuje. 

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Zpracování přirozeného jazyka

Vaše aplikace LUIS obsahuje model přirozeného jazyka specifického pro doménu. Aplikaci LUIS můžete spustit s předem připraveným doménovým modelem, sestavit pro ni vlastní model nebo zkombinovat části předem připravené domény s vlastními informacemi.

* **Předem připravený model:** Služba LUIS obsahuje řadu předem připravených doménových modelů, včetně záměrů, promluv a předem připravených entit. Předem připravené entity můžete použít, aniž byste museli použít záměry a promluvy předem připraveného modelu. [Předem připravené doménové modely](luis-how-to-use-prebuilt-domains.md) zahrnují celý návrh a představují skvělý způsob, jak rychle začít používat službu LUIS.

* **Vlastní model** LUIS nabízí několik způsobů, jak identifikovat vlastní modely, včetně záměrů a entit. Mezi entity patří uživatelsky zjištěné entity, konkrétní nebo literální entity a kombinace počítačového a literálu.

## <a name="build-the-luis-model"></a>Sestavení modelu LUIS
Sestavte model pomocí rozhraní API pro [vytváření obsahu](https://go.microsoft.com/fwlink/?linkid=2092087) nebo pomocí [portálu Luis](https://www.luis.ai).

Model LUIS začíná kategoriemi záměrů uživatele, které se označují jako **[záměry](luis-concept-intent.md)** . Každý záměr potřebuje příklady **[promluv](luis-concept-utterance.md)** uživatele. Každý utterance může poskytnout data, která je třeba extrahovat. 

|Příklad promluvy uživatele|Záměr|Extrahovaná data|
|-----------|-----------|-----------|
|`Book a flight to __Seattle__?`|BookFlight (Rezervovat let)|Seattle|
|`When does your store __open__?`|StoreHoursAndLocation (Poloha a otevírací doba obchodu)|open (otevírá)|
|`Schedule a meeting at __1pm__ with __Bob__ in Distribution`|ScheduleMeeting (Naplánovat schůzku)|1pm, Bob (13:00, Bob)|

## <a name="query-prediction-endpoint"></a>Koncový bod předpovědi dotazů

Po vyškolení a publikování vaší aplikace do koncového bodu pošle klientská aplikace projevy rozhraní API [koncového bodu](https://go.microsoft.com/fwlink/?linkid=2092356) předpovědi. Rozhraní API aplikuje aplikaci na utterance pro analýzu a odpoví s výsledkem předpovědi ve formátu JSON.  

Odpověď koncového bodu JSON obsahuje minimálně promluvu dotazu a záměr s nejvyšším skóre. Může také extrahovat data, jako je například následující entita **typu kontakt** a celková mínění. 

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

Po publikování aplikace LUIS a přijetí reálných uživatelských projevy LUIS poskytuje [aktivní učení](luis-concept-review-endpoint-utterances.md) koncového bodu projevy, aby se zlepšila přesnost předpovědi. 

<a name="using-luis"></a>

## <a name="development-lifecycle"></a>Životní cyklus vývoje
LUIS poskytuje nástroje, správu verzí a spolupráci s ostatními autory LUIS pro integraci do celého [životního cyklu vývoje](luis-concept-app-iteration.md). 

## <a name="implementing-luis"></a>Implementace služby LUIS
Language Understanding (LUIS) jako REST API lze použít s libovolným produktem, službou nebo architekturou s požadavkem HTTP. Následující seznam obsahuje produkty a služby Microsoftu nejčastěji používané se službou LUIS.

Hlavní klientská aplikace služby LUIS je následující:
* [Robot webové aplikace](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) umožňující rychlé vytvoření chatbota s podporou služby LUIS, který bude s uživatelem komunikovat prostřednictvím textového vstupu. Pro kompletní prostředí robota používá [rozhraní bot Framework][bot-framework] verze [4. x](https://github.com/Microsoft/botbuilder-dotnet) .

Nástroje pro rychlé a snadné používání služby LUIS s využitím robota:
* [Luis CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) Balíček NPM poskytuje vytváření a předpovědi pomocí samostatného nástroje příkazového řádku nebo jako importu. 
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) – LUISGen je nástroj pro generování zdrojového kódu C# se silnými typy a zdrojového kódu TypeScript z exportovaného modelu LUIS.
* [Dispatch](https://aka.ms/dispatch-tool) umožňuje používat z nadřazené aplikace několik aplikací LUIS a QnA Maker s využitím modelu dispečera.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) – LUDown je nástroj příkazového řádku, který pomáhá spravovat jazykové modely pro vašeho robota.
* [Robot Framework – skladatel](https://github.com/microsoft/BotFramework-Composer) – integrovaný vývojový nástroj pro vývojáře a víceklientské týmy pro sestavování roboty a konverzací pomocí Microsoft bot Frameworku

Další služby Cognitive Services používané se službou LUIS:
* [QnA maker][qnamaker] umožňuje zkombinovat několik typů textu do otázek a odpovědí znalostní báze.
* [Služba Speech](../Speech-Service/overview.md) převádí mluvené požadavky na text. 
* [Conversation learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) umožňuje ve službě LUIS rychleji vytvářet konverzace chatbota.

Ukázky pomocí LUIS:
* [Konverzační AI](https://github.com/Microsoft/AI) Úložiště GitHub.
* [Robot Framework – ukázky bot](https://github.com/microsoft/BotBuilder-Samples)

## <a name="next-steps"></a>Další kroky

* [Co je nového](whats-new.md)
* Vytvořte novou aplikaci LUIS s využitím [předem připravené](luis-get-started-create-app.md) nebo [vlastní](luis-quickstart-intents-only.md) domény.
* [Odešlete dotaz na koncový bod předpovědi](luis-get-started-get-intent-from-browser.md) veřejné aplikace IoT. 
* [Materiály pro vývojáře](developer-reference-resource.md) pro Luis. 

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/

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

Language Understanding (LUIS) je cloudová služba API, která na text v přirozeném jazyce používá vlastní informace o tom, jak předpovědět celkový význam a vyžádat si relevantní a podrobné informace. 

Například když klientská aplikace pošle text, `find me a wireless keyboard for $30`LUIS odpoví následujícím objektem JSON. 

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
V předchozím příkladu _**záměr**_ nebo celkový význam fráze je, že se uživatel pokouší najít položku. Podrobné informace, které LUIS extrahují, se nazývají _**entity**_ . V tomto případě jsou entitami název položky, kterou uživatel hledá, a množství peněz, které chtějí strávit.

Klientské aplikace používají LUIS JSON, _záměr_ (kategorie) a _entity_ (extrahování podrobných informací) k řízení akcí v klientské aplikaci. Klientská aplikace pro LUIS je často konverzační aplikace, která komunikuje s uživatelem v přirozeném jazyce k dokončení úkolu. Mezi příklady klientských aplikací patří aplikace sociálních médií, chatboti a desktopové aplikace s podporou hlasových služeb. 

![Koncepční obrázek 3 klientských aplikací pracujících s Cognitive Services Language Understanding (LUIS)](./media/luis-overview/luis-entry-point.png "Koncepční obrázek 3 klientských aplikací pracujících s Cognitive Services Language Understanding (LUIS)")

## <a name="example-use-luis-in-a-chat-bot"></a>Příklad použití LUIS v robotovi chatu

<a name="Accessing-LUIS"></a>

Klientská aplikace pošle projevy (text) do publikovaného [rozhraní API][endpoint-apis] koncového bodu Luis přirozeného jazyka pro zpracování a výsledky obdrží jako odpověď JSON. Běžnou klientskou aplikace pro službu LUIS je chatbot.


![Koncepční obrázky LUIS pracující s robotem chatu pro předpověď textu uživatele s porozuměním v přirozeném jazyku (NLP)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Koncepční obrázky LUIS pracující s robotem chatu pro předpověď textu uživatele s porozuměním v přirozeném jazyku (NLP")

|Krok|Akce|
|:--|:--|
|1|Klientská aplikace odešle _promluvu_ uživatele (text napsaný vlastními slovy): „I want to call my HR rep“ (Chci zavolat zástupci z oddělení lidských zdrojů). do koncového bodu služby LUIS jako požadavek HTTP.|
|2|LUIS aplikuje na nestrukturovaný vstupní text uživatele zjištěné jazykové modely a vrátí odpověď ve formátu JSON s nejvyšším záměrem `HRContact`. Odpověď koncového bodu JSON obsahuje minimálně promluvu dotazu a záměr s nejvyšším skóre. Může také extrahovat data, jako je například entita _typu kontakt_ .|
|3|Klientská aplikace se na základě odpovědi ve formátu JSON rozhoduje, jak splnit požadavky uživatele. Tato rozhodnutí mohou zahrnovat rozhodovací strom na robotovi a volání dalších služeb. |

Aplikace LUIS poskytuje klientské aplikaci informace, které jí umožní chytře rozhodovat. Služba LUIS toto rozhodování nezajišťuje. 

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Zpracování přirozeného jazyka

Vaše aplikace LUIS obsahuje modely přirozeného jazyka specifické pro doménu, které vzájemně spolupracují. Aplikaci LUIS můžete spustit s jedním nebo více předem sestavenými modely, Sestavte si vlastní model nebo blende předem připravené modely s vlastními informacemi.

* **Předem sestavený model** LUIS má mnoho předem sestavených domén, které zahrnují modely záměrů a entit, které spolupracují za účelem dokončení běžných scénářů použití. Tyto domény zahrnují označené projevy, které je možné kontrolovat a upravovat, a umožňují jejich přizpůsobení. [Předem připravené doménové modely](luis-how-to-use-prebuilt-domains.md) zahrnují celý návrh a představují skvělý způsob, jak rychle začít používat službu LUIS. Kromě toho existují předem připravené entity, jako je měna a číslo, které můžete použít nezávisle na předem sestavených doménách.

* **Vlastní model** LUIS nabízí několik způsobů, jak vytvářet vlastní modely, včetně záměrů a entit. Mezi entity patří entity náročné na počítač, entity pro porovnávání vzorů a kombinace porovnávacího strojového a vzorového porovnávání.

## <a name="build-the-luis-app"></a>Sestavení aplikace LUIS
Sestavte aplikaci s využitím rozhraní API pro [vytváření obsahu](https://go.microsoft.com/fwlink/?linkid=2092087) nebo pomocí [portálu Luis](https://www.luis.ai).

Aplikace LUIS začíná kategoriemi vstupního textu s názvem **[záměry](luis-concept-intent.md)** . Každý záměr potřebuje příklady **[promluv](luis-concept-utterance.md)** uživatele. Každý utterance může poskytnout data, která je třeba extrahovat. 

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

## <a name="iterative-development-lifecycle"></a>Cyklus iterativního vývoje
LUIS poskytuje nástroje, správu verzí a spolupráci s ostatními autory LUIS pro integraci do plného iterativního [životního cyklu vývoje](luis-concept-app-iteration.md). 

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
* Vytvoření nové aplikace LUIS s [předem vytvořenou](luis-get-started-create-app.md) nebo [vlastní](luis-quickstart-intents-only.md) doménou
* [Odešlete dotaz na koncový bod předpovědi](luis-get-started-get-intent-from-browser.md) veřejné aplikace IoT. 
* [Materiály pro vývojáře](developer-reference-resource.md) pro Luis. 

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/

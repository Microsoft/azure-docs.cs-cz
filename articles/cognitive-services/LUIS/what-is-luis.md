---
title: Co je Language Understanding (LUIS)?
description: Language Understanding (LUIS) je cloudová služba API, která prostřednictvím vlastních inteligentních funkcí strojového učení, které používá na konverzační text uživatele v přirozeném jazyce, předvídá celkový význam a vytahuje z něj relevantní podrobné informace.
ms.topic: overview
ms.date: 02/23/2020
ms.openlocfilehash: 98fb936422f8e23e728efea19fa2cd75d90fac57
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80053349"
---
# <a name="what-is-language-understanding-luis"></a>Co je Language Understanding (LUIS)?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding (LUIS) je cloudová služba API, která prostřednictvím vlastních inteligentních funkcí strojového učení, které používá na konverzační text uživatele v přirozeném jazyce, předvídá celkový význam a vytahuje z něj relevantní podrobné informace.

Klientskou aplikací je pro LUIS jakákoli konverzační aplikace, která s uživatelem při dokončování úloh komunikuje v přirozeném jazyce. Mezi příklady klientských aplikací patří aplikace sociálních médií, chatboti a desktopové aplikace s podporou hlasových služeb.

![Konceptuální obrázek 3 klientských aplikací pracujících s jazykem cognitive services (LUIS)](./media/luis-overview/luis-entry-point.png "Konceptuální obrázek 3 klientských aplikací pracujících s jazykem cognitive services (LUIS)")

## <a name="use-luis-in-a-chat-bot"></a>Použití služby LUIS v chatbotu

<a name="Accessing-LUIS"></a>

Po publikování aplikace LUIS může klientská aplikace odesílat promluvy (text) do rozhraní [API][endpoint-apis] koncového bodu služby LUIS pro zpracování přirozeného jazyka a přijímat z něj výsledky v podobě odpovědí ve formátu JSON. Běžnou klientskou aplikace pro službu LUIS je chatbot.


![Konceptuální snímky služby LUIS, která pracuje s chatovacím robotem při předvídání textu uživatele s porozuměním přirozenému jazyku (NLP)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Konceptuální snímky luis práce s Chat bot předpovědět uživatelský text s porozuměním přirozenému jazyku (NLP")

|Krok|Akce|
|:--|:--|
|1|Klientská aplikace odešle _promluvu_ uživatele (text napsaný vlastními slovy): „I want to call my HR rep“ (Chci zavolat zástupci z oddělení lidských zdrojů). do koncového bodu služby LUIS jako požadavek HTTP.|
|2|Služba LUIS umožňuje vytvářet vlastní jazykové modely a přidávat do aplikace inteligenci. Strojově naučené jazykové modely přebírají nestrukturovaný vstupní text uživatele a vrátí odpověď `HRContact`ve formátu JSON s nejvyšším záměrem . Odpověď koncového bodu JSON obsahuje minimálně promluvu dotazu a záměr s nejvyšším skóre. Může také extrahovat data, jako je například entita _Typ kontaktu._|
|3|Klientská aplikace se na základě odpovědi ve formátu JSON rozhoduje, jak splnit požadavky uživatele. Tato rozhodnutí mohou zahrnovat rozhodovací strom v kódu rozhraní bot a volání jiných služeb. |

Aplikace LUIS poskytuje klientské aplikaci informace, které jí umožní chytře rozhodovat. Služba LUIS toto rozhodování nezajišťuje.

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Zpracování přirozeného jazyka

Vaše aplikace LUIS obsahuje model přirozeného jazyka specifické pro doménu. Aplikaci LUIS můžete spustit s předem připraveným doménovým modelem, sestavit pro ni vlastní model nebo zkombinovat části předem připravené domény s vlastními informacemi.

* **Předem připravený model:** Služba LUIS obsahuje řadu předem připravených doménových modelů, včetně záměrů, promluv a předem připravených entit. Předem připravené entity můžete použít, aniž byste museli použít záměry a promluvy předem připraveného modelu. [Předem připravené doménové modely](luis-how-to-use-prebuilt-domains.md) zahrnují celý návrh a představují skvělý způsob, jak rychle začít používat službu LUIS.

* **Vlastní model** Služba LUIS nabízí několik způsobů, jak identifikovat vlastní modely včetně záměrů a entit. Entity zahrnují entity získané počítačem, specifické nebo literálové entity a kombinaci počítače množin a literálu.

## <a name="build-the-luis-model"></a>Sestavení modelu LUIS
Vytvořte model pomocí [vytváření](https://go.microsoft.com/fwlink/?linkid=2092087) api nebo s [portálem LUIS](https://www.luis.ai).

Model LUIS začíná kategoriemi záměrů uživatele, které se označují jako **[záměry](luis-concept-intent.md)**. Každý záměr potřebuje příklady **[promluv](luis-concept-utterance.md)** uživatele. Každý utterance může poskytnout data, která je třeba extrahovat.

|Příklad promluvy uživatele|Záměr|Extrahovaná data|
|-----------|-----------|-----------|
|`Book a flight to __Seattle__?`|BookFlight (Rezervovat let)|Seattle|
|`When does your store __open__?`|StoreHoursAndLocation (Poloha a otevírací doba obchodu)|open (otevírá)|
|`Schedule a meeting at __1pm__ with __Bob__ in Distribution`|ScheduleMeeting (Naplánovat schůzku)|1pm, Bob (13:00, Bob)|

## <a name="query-prediction-endpoint"></a>Koncový bod předpovědi dotazů

Po vaše aplikace je trénovaný a publikovaný do koncového bodu, klientská aplikace odešle projevy do rozhraní API [koncového bodu](https://go.microsoft.com/fwlink/?linkid=2092356) předpověď. Rozhraní API použije aplikaci utterance pro analýzu a reaguje s výsledky předpověď ve formátu JSON.

Odpověď koncového bodu JSON obsahuje minimálně promluvu dotazu a záměr s nejvyšším skóre. Může také extrahovat data, jako je například následující **entita Typu kontaktu** a celkový mínění.

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

Po publikování aplikace LUIS a obdrží skutečné uživatelské projevy, LUIS poskytuje [aktivní učení](luis-concept-review-endpoint-utterances.md) projevy koncového bodu ke zlepšení přesnosti předpovědi.

<a name="using-luis"></a>

## <a name="development-lifecycle"></a>Životní cyklus vývoje
Služba LUIS poskytuje nástroje, správu verzí a spolupráci s ostatními autory služby LUIS, které se integrují do celého [životního cyklu vývoje](luis-concept-app-iteration.md).

## <a name="implementing-luis"></a>Implementace služby LUIS
Porozumění jazykům (LUIS) jako rozhraní REST API lze použít s libovolným produktem, službou nebo architekturou s požadavkem HTTP. Následující seznam obsahuje produkty a služby Microsoftu nejčastěji používané se službou LUIS.

Hlavní klientská aplikace služby LUIS je následující:
* [Robot webové aplikace](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) umožňující rychlé vytvoření chatbota s podporou služby LUIS, který bude s uživatelem komunikovat prostřednictvím textového vstupu. Používá [Bot Framework][bot-framework] verze [4.x](https://github.com/Microsoft/botbuilder-dotnet) pro kompletní zkušenosti bot.

Nástroje pro rychlé a snadné používání služby LUIS s využitím robota:
* [Cli služby LUIS](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) Balíček NPM poskytuje vytváření a předpověď jako samostatný nástroj příkazového řádku nebo jako import.
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) – LUISGen je nástroj pro generování zdrojového kódu C# se silnými typy a zdrojového kódu TypeScript z exportovaného modelu LUIS.
* [Dispatch](https://aka.ms/dispatch-tool) umožňuje používat z nadřazené aplikace několik aplikací LUIS a QnA Maker s využitím modelu dispečera.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) LUDown je nástroj příkazového řádku, který pomáhá spravovat jazykové modely pro vašeho robota.
* [Bot framework - Composer](https://github.com/microsoft/BotFramework-Composer) - integrovaný vývojový nástroj pro vývojáře a multidisciplinární týmy pro vytváření robotů a konverzačních zkušeností s Microsoft Bot Framework

Další služby Cognitive Services používané se službou LUIS:
* [QnA Maker][qnamaker] umožňuje kombinovat několik typů textu do znalostní báze otázek a odpovědí.
* [Služba Speech](../Speech-Service/overview.md) převádí mluvené požadavky na text.
* [Conversation learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) umožňuje ve službě LUIS rychleji vytvářet konverzace chatbota.

Ukázky pomocí služby LUIS:
* [Konverzační umělá ai](https://github.com/Microsoft/AI) Úložiště GitHub.
* [Bot framework - Ukázky botů](https://github.com/microsoft/BotBuilder-Samples)

## <a name="next-steps"></a>Další kroky

* [Co je nového](whats-new.md)
* Vytvořte novou aplikaci LUIS s využitím [předem připravené](luis-get-started-create-app.md) nebo [vlastní](luis-quickstart-intents-only.md) domény.
* [Odešlete dotaz na koncový bod předpovědi](luis-get-started-get-intent-from-browser.md) veřejné aplikace IoT.
* [Prostředky pro vývojáře](developer-reference-resource.md) pro službu LUIS.

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/

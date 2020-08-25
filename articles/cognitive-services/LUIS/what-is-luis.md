---
title: Co je Language Understanding (LUIS)?
description: Language Understanding (LUIS) – cloudová služba API, která využívá strojové učení ke konverzaci, přirozenému jazyku pro předpověď významu a extrakci informací.
keywords: Azure, umělal Intelligence, AI, zpracování přirozeného jazyka, NLP, porozumění přirozenému jazyku, NLU, konverzace AI, konverzace AI, AI chatovací robot, chatovací robot maker, LUIS, NLP AI, Luis AI, Azure Luis, principy přirozeného jazyka
ms.topic: overview
ms.date: 08/07/2020
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 22fe99e1552a9612adfbc455d60852f1591a1a54
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752147"
---
# <a name="what-is-language-understanding-luis"></a>Co je Language Understanding (LUIS)?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding (LUIS) je cloudová služba API, která prostřednictvím vlastních inteligentních funkcí strojového učení, které používá na konverzační text uživatele v přirozeném jazyce, předvídá celkový význam a vytahuje z něj relevantní podrobné informace.

Klientskou aplikací je pro LUIS jakákoli konverzační aplikace, která s uživatelem při dokončování úloh komunikuje v přirozeném jazyce. Mezi příklady klientských aplikací patří aplikace sociálních médií, chatboti a desktopové aplikace s podporou hlasových služeb.

![Koncepční obrázek 3 klientských aplikací pracujících s Cognitive Services Language Understanding (LUIS)](./media/luis-overview/luis-entry-point.png "Koncepční obrázek 3 klientských aplikací pracujících s Cognitive Services Language Understanding (LUIS)")

## <a name="use-luis-in-a-chat-bot"></a>Použití služby LUIS v chatbotu

<a name="Accessing-LUIS"></a>

Po publikování aplikace LUIS může klientská aplikace odesílat promluvy (text) do rozhraní [API][endpoint-apis] koncového bodu služby LUIS pro zpracování přirozeného jazyka a přijímat z něj výsledky v podobě odpovědí ve formátu JSON. Běžnou klientskou aplikace pro službu LUIS je chatbot.


![Koncepční obrázky LUIS pracující s robotem chatu pro předpověď textu uživatele s porozuměním v přirozeném jazyku (NLP)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Koncepční obrázky LUIS pracující s robotem chatu pro předpověď textu uživatele s porozuměním v přirozeném jazyku (NLP")

|Krok|Akce|
|:--|:--|
|1|Klientská aplikace odešle _promluvu_ uživatele (text napsaný vlastními slovy): „I want to call my HR rep“ (Chci zavolat zástupci z oddělení lidských zdrojů). do koncového bodu služby LUIS jako požadavek HTTP.|
|2|LUIS vám umožňuje vytvořit vlastní jazykové modely a přidat do své aplikace informace. Jazykové modely, které se naučily pro počítače, přijímají nestrukturovaný vstupní text uživatele a vrátí odpověď ve formátu JSON s nejvyšším záměrem `HRContact` . Odpověď koncového bodu JSON obsahuje minimálně promluvu dotazu a záměr s nejvyšším skóre. Může také extrahovat data, jako je například entita _typu kontakt_ .|
|3|Klientská aplikace se na základě odpovědi ve formátu JSON rozhoduje, jak splnit požadavky uživatele. Tato rozhodnutí mohou zahrnovat rozhodovací strom v kódu rozhraní bot Framework a volání dalších služeb. |

Aplikace LUIS poskytuje klientské aplikaci informace, které jí umožní chytře rozhodovat. Služba LUIS toto rozhodování nezajišťuje.

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-understanding-nlu"></a>Principy přirozeného jazyka (NLU)

[Luis poskytuje umělou Intelligence (AI)](artificial-intelligence.md) ve formě NLU, což je podmnožina NLP (přirozený jazyk Processing).

Vaše aplikace LUIS obsahuje model přirozeného jazyka specifického pro doménu. Aplikaci LUIS můžete spustit s předem připraveným doménovým modelem, sestavit pro ni vlastní model nebo zkombinovat části předem připravené domény s vlastními informacemi.

* **Předem připravený model:** Služba LUIS obsahuje řadu předem připravených doménových modelů, včetně záměrů, promluv a předem připravených entit. Předem připravené entity můžete použít, aniž byste museli použít záměry a promluvy předem připraveného modelu. [Předem připravené doménové modely](luis-how-to-use-prebuilt-domains.md) zahrnují celý návrh a představují skvělý způsob, jak rychle začít používat službu LUIS.

* **Vlastní model** LUIS nabízí několik způsobů, jak identifikovat vlastní modely, včetně záměrů a entit. Mezi entity patří entity strojového učení, konkrétní nebo literální entity a kombinace strojového učení a literálu.

Přečtěte si další informace o [NLP](artificial-intelligence.md)a oblasti NLU pro konkrétní Luis.

## <a name="step-1-design-and-build-your-model"></a>Krok 1: návrh a sestavení modelu

Navrhněte svůj model pomocí kategorií záměrů uživatelů s názvem **[záměry](luis-concept-intent.md)**. Každý záměr potřebuje příklady **[promluv](luis-concept-utterance.md)** uživatele. Každý utterance může poskytovat data, která je třeba extrahovat pomocí [entit strojového učení](luis-concept-entity-types.md#effective-machine-learned-entities).

|Příklad promluvy uživatele|Záměr|Extrahovaná data|
|-----------|-----------|-----------|
|`Book a flight to Seattle?`|BookFlight (Rezervovat let)|Seattle|
|`When does your store open?`|StoreHoursAndLocation (Poloha a otevírací doba obchodu)|open|
|`Schedule a meeting at 1pm with Bob in Distribution`|ScheduleMeeting (Naplánovat schůzku)|1pm, Bob (13:00, Bob)|

Sestavte model pomocí rozhraní API pro [vytváření obsahu](https://go.microsoft.com/fwlink/?linkid=2092087) nebo pomocí [**portálu Luis**](https://www.luis.ai)nebo obou. Přečtěte si další informace o tom, jak sestavovat pomocí [portálu](get-started-portal-build-app.md) a [klientských knihoven SDK](quickstart-sdk.md).

## <a name="step-2-get-the-query-prediction"></a>Krok 2: získání předpovědi dotazu

Jakmile je model vaší aplikace vyškolený a publikovaný do koncového bodu, klientská aplikace (například robot robot) odešle projevy do rozhraní API [koncového bodu](https://go.microsoft.com/fwlink/?linkid=2092356) předpovědi. Rozhraní API aplikuje model na utterance pro analýzu a odpoví s výsledkem předpovědi ve formátu JSON.

Odpověď koncového bodu JSON obsahuje minimálně promluvu dotazu a záměr s nejvyšším skóre. Může také extrahovat data, jako je například následující entita **typu kontakt** a celková mínění.

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
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
            "label": "neutral",
            "score": 0.5
        }
    }
}
```

## <a name="step-3-improve-model-prediction"></a>Krok 3: zlepšení předpovědi modelu

Po publikování aplikace LUIS a přijetí reálných uživatelských projevy LUIS poskytuje [aktivní učení](luis-concept-review-endpoint-utterances.md) koncového bodu projevy, aby se zlepšila přesnost předpovědi. Tyto návrhy si projděte v rámci pravidelné údržby v životním cyklu vývoje.

<a name="using-luis"></a>

## <a name="development-lifecycle-and-tools"></a>Životní cyklus a nástroje pro vývoj
LUIS poskytuje nástroje, správu verzí a spolupráci s ostatními autory LUIS pro integraci do celého [životního cyklu vývoje](luis-concept-app-iteration.md).

Language Understanding (LUIS) jako REST API lze použít s libovolným produktem, službou nebo architekturou s požadavkem HTTP. LUIS také poskytuje klientské knihovny (SDK) pro několik hlavních programovacích jazyků. Přečtěte si další informace o poskytnutých [zdrojích pro vývojáře](developer-reference-resource.md) .

Nástroje pro rychlé a snadné používání služby LUIS s využitím robota:
* [Luis CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) Balíček NPM poskytuje vytváření a předpovědi pomocí samostatného nástroje příkazového řádku nebo importu.
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) – LUISGen je nástroj pro generování zdrojového kódu C# se silnými typy a zdrojového kódu TypeScript z exportovaného modelu LUIS.
* [Dispatch](https://aka.ms/dispatch-tool) umožňuje používat z nadřazené aplikace několik aplikací LUIS a QnA Maker s využitím modelu dispečera.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) LUDown je nástroj příkazového řádku, který pomáhá spravovat jazykové modely pro robota.

## <a name="integrate-with-a-bot"></a>Integrace s robotem

Pomocí [služby Azure bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) s [Microsoft bot Framework](https://dev.botframework.com/) sestavíte a nasadíte robota chatu. Navrhněte a vývoj pomocí nástrojů grafického rozhraní, [skladatele](https://docs.microsoft.com/composer/)nebo [pracovních robotů](https://github.com/microsoft/BotBuilder-Samples) , které jsou navržené pro scénáře s největším robotem.

## <a name="integrate-with-other-cognitive-services"></a>Integrace s jinými Cognitive Services

Další služby Cognitive Services používané se službou LUIS:
* [QnA Maker][qnamaker] umožňuje kombinovat několik typů textu do znalostní báze otázek a odpovědí.
* [Služba Speech](../Speech-Service/overview.md) převádí mluvené požadavky na text.

LUIS poskytuje funkce z Analýza textu jako součást vašich stávajících prostředků LUIS. Tato funkce zahrnuje [analýzu mínění](luis-how-to-publish-app.md#configuring-publish-settings) a [extrakci klíčových frází](luis-reference-prebuilt-keyphrase.md) s předem vytvořenou entitou keyPhrase.

## <a name="learn-with-the-quickstarts"></a>Seznamte se s rychlými starty

Seznamte se s LUIS s praktickými rychlými starty pomocí [portálu](get-started-portal-build-app.md) a [klientských knihoven SDK](quickstart-sdk.md).


## <a name="next-steps"></a>Další kroky

* [Co je nového](whats-new.md) u služby a dokumentace
* [Plánování aplikace](luis-how-plan-your-app.md) s využitím [záměrů](luis-concept-intent.md) a [entit](luis-concept-entity-types.md)
* [Dotaz na koncový bod předpovědi](luis-get-started-get-intent-from-browser.md).
* [Materiály pro vývojáře](developer-reference-resource.md) pro Luis.

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/

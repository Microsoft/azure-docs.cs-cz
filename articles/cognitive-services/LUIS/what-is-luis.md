---
title: Co je Language Understanding (LUIS)?
description: Language Understanding (LUIS) – cloudová služba API, která využívá strojové učení ke konverzaci, přirozenému jazyku pro předpověď významu a extrakci informací.
keywords: Azure, umělal Intelligence, AI, zpracování přirozeného jazyka, NLP, přirozené jazykové porozumění, NLU, LUIS, konverzace AI, AI chatovací robot, NLP AI, Azure Luis
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 03/22/2021
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: f32f58bebc0a7d64443259981590e368b109b19b
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278945"
---
# <a name="what-is-language-understanding-luis"></a>Co je Language Understanding (LUIS)?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding (LUIS) je cloudová konverzační služba pro konverzaci, která aplikuje vlastní informace o strojovém učení na konverzaci, text přirozeného jazyka a předpovídá celkový význam a vyžádá si relevantní a podrobné informace.

Klientskou aplikací je pro LUIS jakákoli konverzační aplikace, která s uživatelem při dokončování úloh komunikuje v přirozeném jazyce. Mezi příklady klientských aplikací patří aplikace pro sociální média, chatovacích robotů o AI a desktopové aplikace s podporou řeči.

![Koncepční obrázek 3 klientských aplikací pracujících s Cognitive Services Language Understanding (LUIS)](./media/luis-overview/luis-entry-point.png "Koncepční obrázek 3 klientských aplikací pracujících s Cognitive Services Language Understanding (LUIS)")

Tato dokumentace obsahuje následující typy článků:  

* [**Rychlé starty**](luis-get-started-create-app.md) jsou pokyny Začínáme, které vám pomohou při provádění požadavků na službu.  
* Návody obsahují pokyny k používání služby v konkrétnějším nebo přizpůsobeném [**způsobu.**](luis-how-to-start-new-app.md)  
* [**Koncepty**](artificial-intelligence.md) poskytují podrobné vysvětlení funkcí a funkcí služby.  
* [**Kurzy**](tutorial-intents-only.md) jsou delší než příručky, které ukazují, jak používat službu jako součást v širších obchodních řešeních.  

## <a name="use-luis-in-a-chat-bot"></a>Použití služby LUIS v chatbotu

<a name="Accessing-LUIS"></a>

Po publikování aplikace Azure LUIS pošle klientská aplikace projevy (text) do [rozhraní API][endpoint-apis] koncového bodu zpracování Luis přirozeného jazyka a výsledky obdrží jako odpověď JSON. Běžnou klientskou aplikace pro službu LUIS je chatbot.


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

[Luis poskytuje umělá inteligentní funkce (AI)](artificial-intelligence.md "LUIS poskytuje umělá inteligentní funkce (AI).") ve formě NLU, což je podmnožina systému souborů AI pro zpracování přirozeného jazyka.

Vaše aplikace LUIS obsahuje model přirozeného jazyka specifického pro doménu. Aplikaci LUIS můžete spustit s předem připraveným doménovým modelem, sestavit pro ni vlastní model nebo zkombinovat části předem připravené domény s vlastními informacemi.

* **Předem připravený model:** Služba LUIS obsahuje řadu předem připravených doménových modelů, včetně záměrů, promluv a předem připravených entit. Předem připravené entity můžete použít, aniž byste museli použít záměry a promluvy předem připraveného modelu. [Předem připravené doménové modely](./howto-add-prebuilt-models.md "Předem připravené doménové modely") zahrnují celý návrh a představují skvělý způsob, jak rychle začít používat službu LUIS.

* **Vlastní model** LUIS nabízí několik způsobů, jak identifikovat vlastní modely, včetně záměrů a entit. Mezi entity patří entity strojového učení, konkrétní nebo literální entity a kombinace strojového učení a literálu.

Přečtěte si další informace o [NLP AI](artificial-intelligence.md "NLP")a oblasti NLU pro konkrétní Luis.

## <a name="step-1-design-and-build-your-model"></a>Krok 1: návrh a sestavení modelu

Navrhněte svůj model pomocí kategorií záměrů uživatelů s názvem **[záměry](luis-concept-intent.md "záměry")**. Každý záměr potřebuje příklady **[promluv](luis-concept-utterance.md "projevy")** uživatele. Každý utterance může poskytovat data, která je třeba extrahovat pomocí [entit strojového učení](luis-concept-entity-types.md#effective-machine-learned-entities "entity strojového učení").

|Příklad promluvy uživatele|Záměr|Extrahovaná data|
|-----------|-----------|-----------|
|`Book a flight to Seattle?`|BookFlight (Rezervovat let)|Seattle|
|`When does your store open?`|StoreHoursAndLocation (Poloha a otevírací doba obchodu)|open|
|`Schedule a meeting at 1pm with Bob in Distribution`|ScheduleMeeting (Naplánovat schůzku)|1pm, Bob (13:00, Bob)|

Sestavte model pomocí rozhraní API pro [vytváření obsahu](https://go.microsoft.com/fwlink/?linkid=2092087 "vytváření obsahu") nebo pomocí **[portálu Luis](https://www.luis.ai "Portál LUIS")** nebo obou. Přečtěte si další informace o tom, jak sestavovat pomocí [portálu](get-started-portal-build-app.md "portál") a [klientských knihoven SDK](./client-libraries-rest-api.md?pivots=rest-api "Klientské knihovny SDK").

## <a name="step-2-get-the-query-prediction"></a>Krok 2: získání předpovědi dotazu

Jakmile je model vaší aplikace vyškolený a publikovaný do koncového bodu, klientská aplikace (například robot robot) odešle projevy do rozhraní API [koncového bodu](https://go.microsoft.com/fwlink/?linkid=2092356 "endpoint") předpovědi. Rozhraní API aplikuje model na utterance pro analýzu a odpoví s výsledkem předpovědi ve formátu JSON.

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

Po publikování aplikace LUIS a přijetí reálných uživatelských projevy LUIS poskytuje [aktivní učení](luis-concept-review-endpoint-utterances.md "aktivní učení") koncového bodu projevy, aby se zlepšila přesnost předpovědi. Tyto návrhy si projděte v rámci pravidelné údržby v životním cyklu vývoje.

<a name="using-luis"></a>

## <a name="development-lifecycle-and-tools"></a>Životní cyklus a nástroje pro vývoj
LUIS poskytuje nástroje, správu verzí a spolupráci s ostatními autory LUIS pro integraci do celého [životního cyklu vývoje](luis-concept-app-iteration.md "životní cyklus vývoje").

Language Understanding (LUIS) jako REST API lze použít s libovolným produktem, službou nebo architekturou s požadavkem HTTP. LUIS také poskytuje klientské knihovny (SDK) pro několik hlavních programovacích jazyků. Přečtěte si další informace o poskytnutých [zdrojích pro vývojáře](developer-reference-resource.md "materiály pro vývojáře") .

Nástroje pro rychlé a snadné používání služby LUIS s využitím robota:
* [Luis CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS "LUIS CLI") Balíček NPM poskytuje vytváření a předpovědi pomocí samostatného nástroje příkazového řádku nebo importu.
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen "LUISGen") – LUISGen je nástroj pro generování zdrojového kódu C# se silnými typy a zdrojového kódu TypeScript z exportovaného modelu LUIS.
* [Dispatch](https://aka.ms/dispatch-tool "Účelem") umožňuje používat z nadřazené aplikace několik aplikací LUIS a QnA Maker s využitím modelu dispečera.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown "LUDown") LUDown je nástroj příkazového řádku, který pomáhá spravovat jazykové modely pro robota.

## <a name="integrate-with-a-bot"></a>Integrace s robotem

Pomocí [služby Azure bot Service](/azure/bot-service/ "Azure bot Service") s [Microsoft bot Framework](https://dev.botframework.com/ "Microsoft Bot Framework") sestavíte a nasadíte robota chatu. Navrhněte a vývoj pomocí nástrojů grafického rozhraní, [skladatele](/composer/ "Composer")nebo [pracovních robotů](https://github.com/microsoft/BotBuilder-Samples "Ukázky pracovních robotů") , které jsou navržené pro scénáře s největším robotem.

## <a name="integrate-with-other-cognitive-services"></a>Integrace s jinými Cognitive Services

Další služby Cognitive Services používané se službou LUIS:
* [QnA Maker](../QnAMaker/overview/overview.md "QnA Maker") umožňuje kombinovat několik typů textu do znalostní báze otázek a odpovědí.
* [Služba Speech](../Speech-Service/overview.md "Služba Speech") převádí mluvené požadavky na text.

LUIS poskytuje funkce z Analýza textu jako součást vašich stávajících prostředků LUIS. Tato funkce zahrnuje [analýzu mínění](luis-how-to-publish-app.md#configuring-publish-settings "Analýza mínění") a [extrakci klíčových frází](luis-reference-prebuilt-keyphrase.md "extrakce klíčových frází") s předem vytvořenou entitou keyPhrase.

## <a name="learn-with-the-quickstarts"></a>Seznamte se s rychlými starty

Seznamte se s LUIS s praktickými rychlými starty pomocí [portálu](get-started-portal-build-app.md "portál") a [klientských knihoven SDK](./client-libraries-rest-api.md?pivots=rest-api "Klientské knihovny SDK").


## <a name="deploy-on-premises-using-docker-containers"></a>Nasazení místně pomocí kontejnerů Docker

[Pomocí kontejnerů Luis](luis-container-howto.md) můžete nasadit funkce rozhraní API místně. Tyto kontejnery Docker umožňují přiblížit službu k vašim datům pro účely dodržování předpisů, zabezpečení nebo jiných provozních důvodů.

## <a name="next-steps"></a>Další kroky

* [Co je nového](whats-new.md "Novinky") u služby a dokumentace
* [Plánování aplikace](luis-how-plan-your-app.md "Plánování aplikace") s využitím [záměrů](luis-concept-intent.md "záměry") a [entit](luis-concept-entity-types.md "podnikům")

[bot-framework]: /bot-framework/
[flow]: /connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
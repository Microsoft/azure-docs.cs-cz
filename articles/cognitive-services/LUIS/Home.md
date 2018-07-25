---
title: O Language Understanding (LUIS) v Azure | Dokumentace Microsoftu
description: Další informace o použití Language Understanding (LUIS) vám efektivních možností strojového učení pro vaše aplikace.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/22/2017
ms.author: diberry
ms.openlocfilehash: 072176347adacbabc0a92f1c7e437f8233531003
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225454"
---
# <a name="what-is-language-understanding-luis"></a>Co je Language Understanding (LUIS)?
Language Understanding (LUIS) je Cloudová služba, která se použije vlastní strojového učení na uživatele konverzační, text přirozeného jazyka k předvídání celkové význam a vyžádá si relevantní, podrobné informace. 

Do klientské aplikace LUIS může být libovolná konverzační aplikace, která komunikuje s uživatelem v přirozeném jazyce k dokončení úkolu. Příklady klientských aplikací: aplikace sociálních médií, chatovacích a podporou hlasových desktopových aplikací.  

![Koncepčního obrázku 3 aplikace tak informace o informace LUIS](./media/luis-overview/luis-entry-point.png)

## <a name="what-is-a-luis-app"></a>Co je aplikace LUIS?
Služba LUIS aplikaci, která obsahuje model přirozeného jazyka specifického pro doménu, navrhnout. Můžete spustit aplikace LUIS s využitím předem připravených doménový model, vytvořte vlastní nebo blend řadu předem připravených domény nahraďte svými vlastními informacemi vlastní.

[Předem připravené doménových modelů](luis-how-to-use-prebuilt-domains.md) zahrnout všechny tyto části pro vás a jsou skvělý způsob, jak začít rychle používat LUIS.

Aplikace LUIS také obsahuje nastavení integrace [spolupracovníci](luis-concept-collaborator.md), a [verze](luis-concept-version.md).

## <a name="using-a-luis-app"></a>Pomocí aplikace LUIS
<a name="Accessing-LUIS"></a> Po publikování aplikace LUIS klientská aplikace odešle projevy LUIS [koncový bod rozhraní API] [ endpoint-apis] a přijímá výsledky předpovědí jako odpověďmi ve formátu JSON.

V následujícím diagramu nejprve chatovací robot váš klient odešle text uživatele požadavkům člověka ve své vlastní slova, která chcete LUIS v jednom požadavku HTTP. Za druhé LUIS zjištěná modelu se vztahuje na přirozeného jazyka, který má smysl vstupu uživatele a vrátí odpověď formát JavaScript Object Notation (JSON). Třetí chatovací robot vašeho klienta používá odpověď JSON pro splnění požadavků uživatele. 

![Koncepční trénováním LUIS práce s chatovací robot](./media/luis-overview/luis-overview-process-2.png)

### <a name="example-of-json-endpoint-response"></a>Příklad odpovědi JSON koncového bodu

Odpovědi JSON koncového bodu, minimálně obsahuje utterance dotazu a začátek hodnocení záměr. 

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

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>
## <a name="what-is-a-natural-language-model"></a>Jaký je model přirozeného jazyka?
Model začíná seznam obecných uživatelských záměry, volá _záměry_, jako je například "Let Book" nebo "Kontaktujte oddělení technické podpory." Zadejte uživatele ukázkovému textu, volá _příklad projevy_ pro příkazů. Označte důležité slova nebo fráze v utterance, volá _entity_.


Model zahrnuje:

* **[záměry](#intents)**: kategorie uživatele záměry (zamýšlené akce nebo výsledek)
* **[entity](#entities)**: specifických typů dat v projevy, jako je číslo, e-mailu nebo název
* **[Příklad projevy](#example-utterances)**: příklad, uživatel zadá v klientské aplikaci

### <a name="intents"></a>Záměry 
[Záměr](luis-how-to-add-intents.md), krátká pro _záměr_, je účelem nebo cílem vyjádřeny utterance uživatele, jako je například rezervace letenky, placení faktury nebo hledání zpravodajskému článku. Můžete vytvořit záměru pro každou akci. Služba LUIS cestovní aplikaci může definovat záměru s názvem "BookFlight." Klientské aplikace můžete použít nejvyšší hodnocení záměr které aktivuje nějakou akci. Například pokud "BookFlight" záměr je vrácen služby luis, klientské aplikace by mohly aktivovat volání rozhraní API pro externí služby pro rezervace roviny lístku.

### <a name="entities"></a>Entity
[Entity](luis-how-to-add-entities.md) představuje podrobné informace najdete v utterance, které se týkají žádost uživatele. Například v utterance "Book lístek do Paříže", je požadován jeden společný lístek a "Paříž" je na místě. Dvě entity, které nebyly nalezeny "lístek" označující jeden společný lístek a "Paříž" označující cíl. 

Po LUIS vrací entity v utterance uživatele, klientská aplikace můžete seznam entit jako parametry vyvolání akce. Rezervace letenky například vyžaduje entitami, jako jsou cílové cesty, datum a letecká společnost.

Služba LUIS poskytuje několik způsobů, jak identifikovat a kategorizaci entity.

* **Předem připravených entit** LUIS má řadu předem připravených doménových modelů, včetně záměrů, projevy, a [předem připravených entit](luis-prebuilt-entities.md). Bez nutnosti použít záměry a projevy předem sestavených modelů můžete použít předem připravených entit. Předem připravených entit vám šetří čas.

* **Vlastní entity** LUIS poskytuje několik způsobů, jak identifikovat vlastní [entity](luis-concept-entity-types.md) včetně zjištěné počítače entity, zvláštní nebo literál entit a kombinaci zjištěné počítače a literálu.

### <a name="example-utterances"></a>Ukázkové promluvy
Příklad [utterance](luis-how-to-add-example-utterances.md) je textové zadání od uživatele, který klientská aplikace potřebuje pochopit. To může být věty, jako je "Rezervovat lístek do Paříže" nebo fragment věty, stejně jako "Rezervace" nebo "Paříž let." Projevy nejsou vždy ve správném formátu a může být mnoho variant utterance pro konkrétní záměr. Přidání projevů příklad 10 až 20 na každé záměr a označování entit v každé utterance.

|Příklad utterance uživatele|Záměr|Entity|
|-----------|-----------|-----------|
|"Rezervuje cestě a __Seattle__?"|BookFlight|Seattle|
|"Když se vaše úložiště __otevřete__?"|StoreHoursAndLocation|otevřít|
|"Naplánovat schůzku v __13: 00__ s __Bob__ v distribuci"|ScheduleMeeting|1 odp., Bob|

## <a name="improve-prediction-accuracy"></a>Zlepšení přesnosti předpovědi
Poté, co aplikace LUIS je publikována a přijímá projevy uživatelů, LUIS poskytuje několik metod, zvyšte přesnost předpovědi: [aktivně učit](#active-learning) z koncového bodu projevy [frázi seznamy](#phrase-lists) pro doménu zahrnutí, Word a [vzory](#patterns) a snížit počet projevy, které jsou potřeba.

### <a name="active-learning"></a>Aktivní vzdělávání
V [aktivně učit](luis-how-to-review-endoint-utt.md) proces, služba LUIS umožňuje přizpůsobit aplikaci LUIS skutečných projevy tak, že vyberete projevy, jakou přijala v koncovém bodě pro kontrolu. Můžete přijmout nebo opravte předpovědi koncový bod, obsloužených a znovu. LUIS naučí rychle s Tento iterativní proces, přičemž minimální množství času a úsilí. 

### <a name="phrase-lists"></a>Seznamy frází 
LUIS poskytuje [slovní spojení seznamy](luis-concept-feature.md) tak můžete určit důležité slova nebo fráze do modelu domény. Služba LUIS používá tyto seznamy přidáte další význam těchto slov a vět, které by jinak byly nalezeny v modelu.

### <a name="patterns"></a>Vzory 
Vzorky umožňují zjednodušit kolekce utterance záměr na běžné [šablony](luis-concept-patterns.md) výběru aplikace word a pořadí slov. To umožňuje LUIS další rychleji podle potřeby méně projevy příklad pro příkazů. Vzorky jsou hybridního systému regulární výrazy a výrazy zjištěné počítače. 

<a name="using-luis"></a>

## <a name="authoring-and-accessing-luis"></a>Vytváření a přístup k LUIS
Sestavení aplikace LUIS z webu služby LUIS nebo programově pomocí [vytváření](https://aka.ms/luis-authoring-apis) rozhraní API, nebo použijte v závislosti na potřebné pro vytváření obsahu. Přístup k publikované aplikaci LUIS dotazem [koncový bod](https://aka.ms/luis-endpoint-apis). 

Služba LUIS poskytuje tři websites po celém světě, v závislosti na vaší zdrojové oblasti. Vytváření oblast určuje oblast Azure, kde můžete publikovat aplikace LUIS.
<!--
|Authoring region|Publishing region(s)|
|--|--|
|[www.luis.ai](https://www.luis.ai)|**U.S.**<br>West US<br>West US 2<br>East US<br>East US 2<br>South Central US<br>West Central US<br><br>**Asia**<br>Southeast Asia<br>East Asia<br><br>**South America**<br>Brazil South |
|[au.luis.ai](https://au.luis.ai)|Australia East|
|[eu.luis.ai](https://eu.luis.ai)|West Europe<br>North Europe|
-->

Přečtěte si [Další](luis-reference-regions.md) o vytváření a publikování oblastech.

## <a name="what-technologies-work-with-luis"></a>Které technologie pracovat LUIS?
Několik technologie Microsoftu spolupracují s LUIS:

* [API kontrola pravopisu Bingu](../bing-spell-check/proof-text.md) poskytuje oprava textu před předpovědi. 
* [Bot Framework] [ bot-framework] umožňuje chatovací robot komunikovat s uživatelem prostřednictvím textové zadání. Vyberte [3.x](https://github.com/Microsoft/BotBuilder) nebo [4.x](https://github.com/Microsoft/botbuilder-dotnet) SDK zajišťuje kompletní robota.
* [Nástroj QnA Maker] [ qnamaker] umožňuje několik typů text, který má zkombinovat do znalostní bázi otázek a odpovědí.
* [Rozpoznávání řeči](../Speech/home.md) převede požadavky mluvený jazyk na text. Po převodu na text, LUIS zpracovává požadavky. Zobrazit [sadou SDK pro řeč](https://aka.ms/csspeech) Další informace.
* [Rozhraní text Analytics](../text-analytics/overview.md) poskytuje mínění analýzy a extrakci klíčových frází data.

## <a name="next-steps"></a>Další postup
Vytvoření nové aplikace LUIS s [předem připravených](luis-get-started-create-app.md) nebo [vlastní](luis-quickstart-intents-only.md) domény.

<!-- Reference-style links -->
[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://aka.ms/luis-authoring-api
[endpoint-apis]: https://aka.ms/luis-endpoint-apis
[qnamaker]: https://qnamaker.ai/
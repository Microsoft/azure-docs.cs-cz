---
title: Jazyk znalosti o (LEOŠ) v Azure | Microsoft Docs
description: Další informace o použití jazyka Principy (LEOŠ) a převeďte sílu strojového učení pro vaše aplikace.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/22/2017
ms.author: v-geberr
ms.openlocfilehash: bbd0a532e54f9b221739c8ae9ff097fe44fdc4df
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751591"
---
# <a name="what-is-language-understanding-luis"></a>Co je jazyk Principy (LEOŠ)?
Principy jazyka (LEOŠ) je Cloudová služba, která se vztahuje na uživatele konverzačního, přirozeného jazyka text k předvídání celkový význam nebo vysunout relevantní, podrobné informace vlastní strojové učení. 

Klientská aplikace pro LEOŠ může být jakékoli konverzačního aplikace, která komunikuje s uživatelem v přirozeném jazyce k dokončení úlohy. Příklady klientských aplikací: aplikace sociálních médií, chatbots a podporou rozpoznávání řeči aplikací klasické pracovní plochy.  

![Koncepčního obrázku 3 aplikací napájení informace info LEOŠ](./media/luis-overview/luis-entry-point.png)

## <a name="what-is-a-luis-app"></a>Co je aplikace LEOŠ?
LEOŠ aplikaci, která obsahuje model specifické pro doménu přirozeného jazyka, který návrhu. Spusťte aplikaci LEOŠ s modelem předem domény, sestavení vlastní nebo přizpůsobte kusy předem domény s vlastními vlastní informace.

[Předkompilované domény modely](luis-how-to-use-prebuilt-domains.md) zahrnout tyto údaje a jsou skvělý způsob, jak začít používat LEOŠ rychle.

Aplikace LEOŠ také obsahuje nastavení integrace [spolupracovníci](luis-concept-collaborator.md), a [verze](luis-concept-version.md).

## <a name="using-a-luis-app"></a>Pomocí aplikace LEOŠ
<a name="Accessing-LUIS"></a> Po publikování aplikace LEOŠ klientské aplikace odesílá utterances LEOŠ [koncový bod rozhraní API] [ endpoint-apis] a přijímá výsledky předpovědi jako JSON odpovědi.

V následujícím diagramu nejprve chatbot váš klient odešle text uživatele co uživatel chce v vlastní slova LEOŠ v požadavku HTTP. Druhý LEOŠ zjištěné modelu se vztahuje na přirozeného jazyka, který má smysl vstupu uživatele a vrátí odpověď formátu JavaScript Object Notation (JSON). Váš klient chatbot třetí, používá JSON odpovědi ke splnění požadavků na uživatele. 

![Koncepční obrazů LEOŠ práce s Chatbot](./media/luis-overview/luis-overview-process-2.png)

### <a name="example-of-json-endpoint-response"></a>Příklad odpovědi JSON koncového bodu

Odpovědi JSON koncového bodu, minimálně obsahuje utterance dotazu a horní vyhodnocování záměr. 

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
## <a name="what-is-a-natural-language-model"></a>Co je model přirozeného jazyka?
Model začíná seznam obecných uživatelských záměry, nazývá _záměry_, jako je například "Cestě adresáře" nebo "Obraťte se na technickou podporu." Zadejte text příklad uživatele, nazývá _příklad utterances_ pro záměry. Potom označit významné slova nebo fráze v utterance, nazývá _entity_.


Model zahrnuje:

* **[záměry](#intents)**: kategorie uživatele záměry (zamýšlenou akci nebo výsledek)
* **[entity](#entities)**: konkrétní typy dat v utterances jako číslo, e-mailu nebo název
* **[Příklad utterances](#example-utterances)**: Příklad text uživatel zadá klientské aplikace

### <a name="intents"></a>Záměry 
[Záměr](luis-how-to-add-intents.md), zkratka pro _záměr_, je účelem nebo cílem vyjádřené v utterance uživatele, například rezervace letu, platícího faktury nebo hledání článku zprávy. Můžete vytvořit záměrem pro každou akci. Cesta aplikace na LEOŠ může definovat záměrem s názvem "BookFlight." Klientské aplikace můžete použít horní vyhodnocování záměr k aktivaci akce. Například pokud je záměr "BookFlight" vrácené z LEOŠ, klientskou aplikaci může spustit volání rozhraní API na externí službu pro rezervace rovinu lístku.

### <a name="entities"></a>Entity
[Entity](luis-how-to-add-entities.md) představuje podrobné informace o se nacházejí v rámci utterance, které se týkají žádost uživatele. Například v utterance "Kniha lístek do Paříž", je požadován jeden lístek a "Paříž" je umístění. Dvě entity nebyly nalezeny "lístek" znamenající jednoho lístku a "Paříž" označující cíl. 

Po LEOŠ vrací entity, najít v utterance uživatele, klientská aplikace můžete použít seznam entity jako parametry pro spuštění akce. Například rezervace letu vyžaduje entity jako cíl cesty, datum a letecká společnost.

LEOŠ poskytuje několik způsobů, jak identifikovat a kategorizace entity.

* **Předkompilované entity** LEOŠ má mnoho modely předem domény včetně záměry, utterances, a [předem entity](pre-builtentities.md). Můžete vytvořit předem entity bez nutnosti použití tříd Intent a utterances předem modelu. Předkompilované entity ušetřit čas.

* **Vlastní entity** LEOŠ poskytuje několik způsobů, jak identifikovat vlastní vlastní [entity](luis-concept-entity-types.md) včetně naučili počítač entity, konkrétní nebo literál entity a kombinace naučili počítače a literálu.

### <a name="example-utterances"></a>Příklad utterances
Příklad [utterance](luis-how-to-add-example-utterances.md) text vstup od uživatele, který klientská aplikace je potřeba pochopit. To může být věty, jako je "Lístek se sešit do Paříž" nebo fragment věty, jako je "Rezervace" nebo "Paříž let." Utterances nejsou vždy ve správném formátu a může být mnoho variant utterance pro zvláštní zájem. Přidejte do každého záměr utterances příklad 10 až 20 a označte entity v každé utterance.

|Příklad utterance uživatele|Záměr|Entity|
|-----------|-----------|-----------|
|"Sešit cestě a __Praha__?"|BookFlight|Seattle|
|"Pokud se vaše úložiště __otevřete__?"|StoreHoursAndLocation|otevřít|
|"Naplánování schůzky na __13: 00__ s __Bob__ rozdělení"|ScheduleMeeting|: 00, Roberta|

## <a name="improve-prediction-accuracy"></a>Zvyšte přesnost předpovědi
Po aplikaci LEOŠ je publikována a přijímá utterances reálný uživatel, LEOŠ poskytuje několik metod a zvyšte tak přesnost předpovědi: [active learning](#active-learning) z koncového bodu utterances [fráze seznamy](#phrase-lists) pro doménu aplikace Word zahrnutí, a [vzory](#patterns) a snížit počet utterances potřeby.

### <a name="active-learning"></a>Aktivní vzdělávání
V [active learning](label-suggested-utterances.md) procesu LEOŠ vám umožní přizpůsobit aplikaci LEOŠ reálného utterances výběrem utterances obdržel na koncový bod pro zkontrolovali. Můžete přijmout nebo opravte předpovědi koncový bod, obsloužených a opakované publikování. LEOŠ zjišťuje rychle se v tomto procesu iterativní trvá minimální množství čas a úsilí. 

### <a name="phrase-lists"></a>Seznamy fráze 
Poskytuje LEOŠ [slovní spojení seznamy](luis-concept-feature.md) , můžete určit důležité slova nebo fráze k vaší doméně modelu. LEOŠ používá tyto seznamy k přidání dalších násobek do těchto slova a slovní spojení, které by jinak nacházejí v modelu.

### <a name="patterns"></a>Vzory 
Vzory vám umožňují zjednodušit záměr utterance kolekce do běžné [šablony](luis-concept-patterns.md) word výběr a pořadí slov. To umožňuje LEOŠ další rychlejší podle nutnosti méně utterances příklad pro záměry. Vzorky se systém hybridní regulární výrazy a výrazy naučili počítače. 

<a name="using-luis"></a>

## <a name="authoring-and-accessing-luis"></a>Vytváření obsahu a přístup k LEOŠ
Sestavení LEOŠ aplikace z webu LEOŠ nebo prostřednictvím kódu programu se [vytváření](https://aka.ms/luis-authoring-apis) rozhraní API, nebo použijte podle potřeb pro vytváření obsahu. Přístup k publikované aplikaci LEOŠ dotazem [koncový bod](https://aka.ms/luis-endpoint-apis). 

LEOŠ poskytuje tři weby po celém světě, v závislosti na vaší oblasti pro vytváření obsahu. Vytváření oblast určuje oblast Azure, kde můžete publikovat aplikaci LEOŠ.
<!--
|Authoring region|Publishing region(s)|
|--|--|
|[www.luis.ai](https://www.luis.ai)|**U.S.**<br>West US<br>West US 2<br>East US<br>East US 2<br>South Central US<br>West Central US<br><br>**Asia**<br>Southeast Asia<br>East Asia<br><br>**South America**<br>Brazil South |
|[au.luis.ai](https://au.luis.ai)|Australia East|
|[eu.luis.ai](https://eu.luis.ai)|West Europe<br>North Europe|
-->

Další informace [Další](luis-reference-regions.md) o vytváření a publikování oblasti.

## <a name="what-technologies-work-with-luis"></a>Jaké technologie pracovat LEOŠ?
Několik technologiích společnosti Microsoft ve spolupráci s LEOŠ:

* [Kontrola pravopisu Bing-API](../bing-spell-check/proof-text.md) poskytuje oprava textu před předpovědi. 
* [Framework robota] [ bot-framework] umožňuje chatbot komunikovat s uživatelem prostřednictvím zadávání textu. Vyberte [3.x](https://github.com/Microsoft/BotBuilder) nebo [4.x](https://github.com/Microsoft/botbuilder-dotnet) SDK pro dokončení robota prostředí.
* [QnA Maker] [ qnamaker] umožňuje několik typů text, který má sloučit ve znalostní bázi otázku a odpověď.
* [Rozpoznávání řeči](../Speech/home.md) převede mluvené jazykové požadavky na text. Jakmile převést na text, LEOŠ zpracovává požadavky. V tématu [řeči SDK](https://aka.ms/csspeech) Další informace.
* [Analýza textu](../text-analytics/overview.md) poskytuje extrakce dat frázi postojích pro analýzu a klíč.

## <a name="next-steps"></a>Další postup
Vytvořit novou aplikaci LEOŠ s [předem](luis-get-started-create-app.md) nebo [vlastní](luis-quickstart-intents-only.md) domény.

<!-- Reference-style links -->
[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://aka.ms/luis-authoring-api
[endpoint-apis]: https://aka.ms/luis-endpoint-apis
[qnamaker]: https://qnamaker.ai/
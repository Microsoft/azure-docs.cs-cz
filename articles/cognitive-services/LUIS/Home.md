---
title: Jazyk znalosti o (LEOŠ) v Azure | Microsoft Docs
description: Další informace o použití jazyka Principy (LEOŠ) a převeďte sílu strojového učení pro vaše aplikace.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/22/2017
ms.author: v-geberr
ms.openlocfilehash: 5656ce051a171812e6d008d56b42bf16c0c2a2ca
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "35343930"
---
# <a name="what-is-language-understanding-luis"></a>Co je jazyk Principy (LEOŠ)?
Principy jazyka (LEOŠ) je Cloudová služba, která se použije vlastní strojové učení na uživatele konverzačního, přirozeného jazyka text k předvídání celkový význam a relevantní, podrobné informace pro vyžádání obsahu. 

Klientská aplikace pro LEOŠ může být jakékoli konverzačního aplikace, která komunikuje s uživatelem v přirozeném jazyce k dokončení úlohy. Příklady klientských aplikací: aplikace sociálních médií, chatbots a podporou rozpoznávání řeči aplikací klasické pracovní plochy.  

![Koncepčního obrázku 3 aplikací napájení informace info LEOŠ](./media/luis-overview/luis-entry-point.png)

Klientské aplikace (například chatbot) odešle text uživatele co uživatel chce v vlastní slova LEOŠ v požadavku HTTP. LEOŠ zjištěné modelu se vztahuje na přirozeného jazyka, který má smysl vstupu uživatele a vrátí odpověď formátu JSON. Klientské aplikace používá JSON odpovědi ke splnění požadavků na uživatele. 

![Koncepční obrazů LEOŠ práce s Chatbot](./media/luis-overview/luis-overview-process-2.png)

## <a name="what-is-a-luis-app"></a>Co je aplikace LEOŠ?
LEOŠ aplikace je model jazyka domény, které návrhu. Spusťte aplikaci s modelem předem domény, sestavení vlastní nebo přizpůsobte kusy předem domény s vlastními vlastní informace.

Model začíná seznam obecných uživatelských záměry, nazývá _záměry_, jako je například "Cestě adresáře" nebo "Obraťte se na technickou podporu." Zadejte uživatele příklad frází, názvem _utterances_ pro záměry. Potom označit významné slova nebo fráze v utterance, nazývá _entity_.

[Předkompilované domény modely] [ prebuilt-domains] zahrnují tyto údaje pro vás a jsou skvělý způsob, jak začít používat LEOŠ rychle.

<a name="Accessing-LUIS"></a>

Jakmile modelu je vytvořená a publikovaná, klientské aplikace odesílá utterances LEOŠ [koncový bod rozhraní API] [ endpoint-apis] a přijímá výsledky předpovědi jako JSON odpovědi.

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

## <a name="what-is-a-luis-model"></a>Co je model LEOŠ?
LEOŠ model zahrnuje:

* **[záměry](#intents)**: kategorie uživatele záměry (zamýšlenou akci nebo výsledek)
* **[entity](#entities)**: konkrétní typy dat v utterances jako číslo, e-mailu nebo název
* **[Příklad utterances](#example-utterances)**: Příklad text uživatel zadá do klientské aplikace

### <a name="intents"></a>Záměry 
[Záměr][add-intents], zkratka pro _záměr_, je účelem nebo cílem vyjádřené v utterance uživatele, například rezervace letu, platícího faktury nebo hledání článku zprávy. Můžete vytvořit záměrem pro každou akci. Cesta aplikace může definovat záměrem s názvem "BookFlight." Klientské aplikace můžete použít horní vyhodnocování záměr k aktivaci akce. Například pokud je záměr "BookFlight" vrácené z LEOŠ, klientskou aplikaci může spustit volání rozhraní API na externí službu pro rezervace rovinu lístku.

### <a name="entities"></a>Entity
[Entity] [ add-entities] představuje podrobné informace o se nacházejí v rámci utterance, které se týkají žádost uživatele. Například v utterance "Kniha lístek do Paříž", je požadován jeden lístek a "Paříž" je umístění. Dvě entity nebyly nalezeny "lístek" znamenající jednoho lístku a "Paříž" označující cíl. 

Po LEOŠ vrací entity v utterance uživatele nalezen, klientské aplikace můžete použít seznam entity jako parametry pro spuštěná akce. Například rezervace letu vyžaduje entity jako cíl cesty, datum a letecká společnost.

LEOŠ poskytuje několik způsobů, jak identifikovat a kategorizace entity.

* **Předkompilované entity** LEOŠ má mnoho modely předem domény včetně záměry, utterances, a [předem entity][prebuilt-entities]. Můžete vytvořit předem entity bez nutnosti použití tříd Intent a utterances předem modelu. Předkompilované entity ušetřit čas.

* **Vlastní entity** LEOŠ poskytuje několik způsobů, jak identifikovat vlastní vlastní [entity] [ entity-concept] včetně naučili počítač entity, konkrétní nebo literál entity a kombinaci naučili počítače a literálu.

### <a name="example-utterances"></a>Příklad utterances
Příklad [utterance] [ add-example-utterances] text vstup od uživatele, který vaše aplikace, musí porozumět. To může být věty, jako je "Lístek se sešit do Paříž" nebo fragment věty, jako je "Rezervace" nebo "Paříž let." Utterances nejsou vždy ve správném formátu a může být mnoho variant utterance pro zvláštní zájem. Přidejte do každého záměr utterances příklad 10 až 20 a označte entity v každé utterance.

|Příklad utterance uživatele|Záměr|Entity|
|-----------|-----------|-----------|
|"Sešit cestě a __Praha__?"|BookFlight|Seattle|
|"Pokud se vaše úložiště __otevřete__?"|StoreHoursAndLocation|otevřít|
|"Naplánování schůzky na __13: 00__ s __Bob__ rozdělení"|ScheduleMeeting|: 00, Roberta|

## <a name="improve-prediction-accuracy"></a>Zvyšte přesnost předpovědi
Jakmile aplikace je publikována a obdrží utterances reálný uživatel, LEOŠ poskytuje několik metod a zvyšte tak přesnost předpovědi: [active learning](#active-learning) z koncového bodu utterances [fráze seznamy](#phrase-lists) pro zahrnutí word domény, a [vzory](#patterns) a snížit počet utterances potřeby.

### <a name="active-learning"></a>Aktivní vzdělávání
V [active learning](label-suggested-utterances.md) procesu LEOŠ vám umožní přizpůsobit aplikaci reálného utterances výběrem utterances obdržel na koncový bod pro zkontrolovali. Můžete přijmout nebo opravte předpovědi koncový bod, obsloužených a opakované publikování. LEOŠ zjišťuje rychle se v tomto procesu iterativní trvá minimální množství čas a úsilí. 

### <a name="phrase-lists"></a>Seznamy fráze 
Poskytuje LEOŠ [slovní spojení seznamy](luis-concept-feature.md) , můžete určit důležité slova nebo fráze k vaší doméně modelu. LEOŠ používá tyto seznamy k přidání dalších násobek do těchto slova a slovní spojení, které by jinak nacházejí v modelu.

### <a name="patterns"></a>Vzory 
Vzory vám umožňují zjednodušit záměr utterance kolekce do běžné [šablony] [ patterns] word výběr a pořadí slov. To umožňuje LEOŠ další rychlejší podle nutnosti méně utterances příklad pro záměry. Vzorky se systém hybridní regulární výrazy a výrazy naučili počítače. 

## <a name="using-luis"></a>Pomocí LEOŠ
Můžete vytvořit aplikaci LEOŠ z [www.luis.ai](http://www.luis.ai) web nebo můžete sestavit aplikace programově pomocí [vytváření](https://aka.ms/luis-authoring-apis) rozhraní API. Přístup k publikované aplikaci LEOŠ dotazem [koncový bod](https://aka.ms/luis-endpoint-apis). 

## <a name="what-technologies-work-with-luis"></a>Jaké technologie pracovat LEOŠ?
Několik technologiích společnosti Microsoft ve spolupráci s LEOŠ:

* [Kontrola pravopisu Bing-API] [ bing-spell-check-api] poskytuje oprava textu před předpovědi. 
* [Framework robota] [ bot-framework] umožňuje chatbot komunikovat s uživatelem prostřednictvím zadávání textu. Vyberte [3.x](https://github.com/Microsoft/BotBuilder) nebo [4.x](https://github.com/Microsoft/botbuilder-dotnet) SDK pro dokončení robota prostředí.
* [QnA Maker] [ qnamaker] umožňuje několik typů text, který má sloučit ve znalostní bázi otázku a odpověď.
* [Rozpoznávání řeči] [ speech] převede mluvené jazykové požadavky na text. Jakmile převést na text, LEOŠ zpracovává požadavky. V tématu [řeči SDK](https://aka.ms/csspeech) Další informace.
* [Analýza textu] [ text-analytics] poskytuje extrakce dat frázi postojích pro analýzu a klíč.

## <a name="next-steps"></a>Další postup
Vytvoření [novou aplikaci LEOŠ](LUIS-get-started-create-app.md).

<!-- Reference-style links -->
[create-app]:luis-get-started-create-app.md
[azure-portal]:https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account
[publish-app]:PublishApp.md#test-your-published-endpoint-in-a-browser
[luis-concept-entity-types]:luis-concept-entity-types.md
[add-example-utterances]: luis-how-to-add-example-utterances.md
[prebuilt-entities]: pre-builtentities.md
[prebuilt-domains]: luis-how-to-use-prebuilt-domains.md
[label-suggested-utterances]: label-suggested-utterances.md
[intro-video]:https://aka.ms/LUIS-Intro-Video
[bot-framework]:https://docs.microsoft.com/bot-framework/
[speech]:../Speech/index.md
[flow]:https://docs.microsoft.com/connectors/luis/
[entity-concept]:luis-concept-entity-types.md
[add-intents]:luis-how-to-add-intents.md
[add-entities]:luis-how-to-add-entities.md
[authoring-apis]:https://aka.ms/luis-authoring-api
[endpoint-apis]:https://aka.ms/luis-endpoint-apis
[LUIS]:luis-reference-regions.md
[text-analytics]:https://azure.microsoft.com/services/cognitive-services/text-analytics/
[patterns]:luis-concept-patterns.md
[bing-spell-check-api]:https://azure.microsoft.com/services/cognitive-services/spell-check/
[qnamaker]:https://qnamaker.ai/
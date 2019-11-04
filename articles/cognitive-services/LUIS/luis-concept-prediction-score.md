---
title: Skóre předpovědi – LUIS
titleSuffix: Azure Cognitive Services
description: Skóre předpovědi indikuje stupeň spolehlivosti, který Služba LUIS API má pro výsledky předpovědi na základě utterance uživatele.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: 5b8d97005d8f404a296ddb45e92b65e4aa811aa3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73486771"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Skóre předpovědi označují přesnost předpovědi pro záměr a entity.

Skóre předpovědi indikuje, že stupeň spolehlivosti LUIS má za následek předpověď výsledků utterance uživatele.

Skóre předpovědi je mezi nulou (0) a jednou (1). Příkladem vysoce jistoty je LUIS skóre je 0,99. Příklad skóre s nízkou jistotou je 0,01. 

|Hodnota skóre|jistotou|
|--|--|
|1\. místo|přesně shoda|
|0,99|vysoká důvěra|
|0,01|nízká důvěra|
|0|přesné selhání při porovnávání|

## <a name="top-scoring-intent"></a>Záměr na nejvyšší hodnocení

Každá předpověď utterance vrací záměr na nejvyšší hodnocení. Tato předpověď je numerické porovnání skóre předpovědi. 

## <a name="proximity-of-scores-to-each-other"></a>Blízkost výsledků od sebe navzájem

V horních dvou hodnoceních může být mezi nimi velmi malý rozdíl. LUIS neindikuje tuto blízkost jinou, než vrací nejvyšší skóre.  

## <a name="return-prediction-score-for-all-intents"></a>Vrátit skóre předpovědi pro všechny záměry

Výsledek testu nebo koncového bodu může obsahovat všechny záměry. Tato konfigurace je nastavená na koncovém bodu pomocí správné dvojice název/hodnota řetězce dotazu.

|Rozhraní API pro předpověď|Název řetězce dotazu|
|--|--|
|Technologie|`show-all-intents=true`|
|V2|`verbose=true`|

## <a name="review-intents-with-similar-scores"></a>Recenze s podobnými skóre

Kontrola skóre pro všechny záměry je dobrým způsobem, jak ověřit, že není pouze správný záměr, ale zda je skóre dalšího identifikovaného záměru významně a konzistentně nižší pro projevy.

Pokud má více záměrů uzavřít skóre na základě kontextu utterance, LUIS může přepínat mezi záměry. Chcete-li tuto situaci opravit, pokračujte v přidávání projevy k jednotlivým záměrům s širší škálou kontextových rozdílů, nebo můžete mít klientskou aplikaci, jako je například robotka chatu, nastavit programovou volbu pro zpracování 2 nejdůležitějších záměrů.

2 záměry, které jsou příliš pečlivě skóre, mohou Invertovat kvůli **nedeterministickému školení**. Nejvyšší skóre by se mohlo stát druhým nahoru a druhé horní skóre by mohlo být první horní skóre. Aby nedocházelo k této situaci, přidejte příklad projevy ke každému hornímu dvou záměrům pro daný utterance s volbou a kontextem pro Word, který rozlišuje 2 záměry. Oba záměry by měly mít stejný počet příkladů projevy. Pravidlo pro oddělení, které zabrání inverzi z důvodu školení, je o 15% rozdílu ve skórech.

**Nedeterministické školení** můžete vypnout [školením se všemi daty](luis-how-to-train.md#train-with-all-data).

## <a name="differences-with-predictions-between-different-training-sessions"></a>Rozdíly mezi různými školicími cvičeními v předpovědi

Když provedete stejný model v jiné aplikaci a skóre nejsou stejné, je rozdíl z důvodu **nedeterministického školení** (prvek náhodnost). Za druhé, jakékoli překrytí utterance na více než jeden záměr znamená, že se nejvyšší záměr pro stejný utterance může změnit na základě školení.

Pokud váš chat robot vyžaduje konkrétní LUIS skóre k označení důvěry v úmyslu, měli byste použít rozdíl skóre mezi prvními dvěma záměry. Tato situace nabízí flexibilitu při výuce.

**Nedeterministické školení** můžete vypnout [školením se všemi daty](luis-how-to-train.md#train-with-all-data).

## <a name="e-exponent-notation"></a>Notaci E (exponent)

Skóre předpovědi můžou použít notaci exponent, který se *zobrazuje* nad rozsahem 0-1, například `9.910309E-07`. Toto skóre je označením velmi **malého** čísla.

|Skóre zápisu E |Skutečné skóre|
|--|--|
|9.910309 e-07|.0000009910309|

## <a name="punctuation"></a>Oddělovač

[Přečtěte si další informace](luis-concept-utterance.md#punctuation-marks) o použití nebo ignorování interpunkce. 

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak přidat entity do aplikace LUIS, najdete v tématu věnovaném [Přidání entit](luis-how-to-add-entities.md) .

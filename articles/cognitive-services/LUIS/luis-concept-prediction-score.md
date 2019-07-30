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
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 34ec5588a510574f4ea9f01bd23c6f6487e288da
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638361"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Skóre předpovědi označují přesnost předpovědi pro záměr a entity.

Skóre předpovědi indikuje, jakou míru spolehlivosti LUIS má výsledek předpovědi na základě utterance uživatele.

Predikce skóre je nula (0) a jedna (1). Příklad vysoce jistotu skóre LUIS je 0,99. Příklad s nízkou spolehlivostí skóre je 0,01. 

|Hodnota skóre|Spolehlivost|
|--|--|
|1|jednoznačného shody|
|0,99.|jistotou|
|0.01|s nízkou spolehlivostí|
|0|jednoznačného selhání tak, aby odpovídaly|

Když utterance výsledkem nízká pravděpodobnost, LUIS upozorňuje, že v [LUIS](luis-reference-regions.md) webu **záměr** stránka s identifikovanou **označené záměr** uvedených s red.

![Nesrovnalosti skóre](./media/luis-concept-score/score-discrepancy.png)

## <a name="top-scoring-intent"></a>Nejvyšší hodnocení záměr

Každý předpovědi utterance vrátí záměr se nejvyšší hodnocení. Tato předpověď je numerické porovnání skóre předpovědi. V horních dvou hodnoceních může být mezi nimi velmi malý rozdíl. LUIS neindikuje tuto blízkost jinou, než vrací nejvyšší skóre.  

## <a name="return-prediction-score-for-all-intents"></a>Vrátí skóre předpovědi pro všechny příkazy

Výsledek testu nebo koncový bod může zahrnovat všechny záměry. Tato konfigurace je nastavena na [koncový bod](https://aka.ms/v1-endpoint-api-docs) s `verbose=true` dvojice název/hodnota v řetězci dotazu.

## <a name="review-intents-with-similar-scores"></a>Zkontrolujte záměry s podobné skóre

Kontrola skóre pro veškeré záměry je dobrým způsobem, jak ověřit, že se identifikuje správná záměr nejen, ale, že další identifikovat záměr na skóre je výrazně nižší konzistentně projevy.

Pokud máte více záměry zavřít předpovědi skóre, na základě kontextu utterance, LUIS může přepínat mezi příkazů. Chcete-li tuto situaci opravit, pokračujte v přidávání projevy k jednotlivým záměrům s širší škálou kontextových rozdílů, nebo můžete mít klientskou aplikaci, jako je například robotka chatu, nastavit programovou volbu pro zpracování 2 nejdůležitějších záměrů.

2 záměry, které jsou příliš pečlivě skóre, mohou Invertovat kvůli nedeterministickému školení. Začátek skóre může být ten druhý a druhou nejvyšší skóre se první hlavní skóre stát. Aby nedocházelo k této situaci, přidejte příklad projevy ke každému hornímu dvou záměrům pro daný utterance s volbou a kontextem pro Word, který rozlišuje 2 záměry. O stejném počtu projevy příklad by měl mít dva záměry. Existuje pravidlo pro oddělení, které brání inverzi kvůli školení, je 15 % rozdíl v skóre.

Nedeterministické školení můžete vypnout [školením se všemi daty](luis-how-to-train.md#train-with-all-data).

## <a name="differences-with-predictions-between-different-training-sessions"></a>Rozdíly mezi různými školicími cvičeními v předpovědi

Když provedete stejný model v jiné aplikaci a skóre nejsou stejné, je rozdíl z důvodu nedeterministického školení (prvek náhodnost). Za druhé žádné překrytí utterance na více než jeden záměr znamená, že hlavní záměr pro stejný utterance lze změnit v závislosti na školení.

Pokud váš chat robot vyžaduje konkrétní LUIS skóre k označení důvěry v úmyslu, měli byste použít rozdíl skóre mezi prvními dvěma záměry. Tato situace nabízí flexibilitu při výuce.

## <a name="e-exponent-notation"></a>Zápis E (exponent)

Predikce skóre, které můžete použít exponentu zápis *povolí, nebude* vyšší než 0-1 v rozsahu, jako například `9.910309E-07`. Toto skóre je údaj o velmi **malé** číslo.

|Skóre zápis E |Skutečný výsledek|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="punctuation"></a>Interpunkce

[Přečtěte si další informace](luis-concept-utterance.md#punctuation-marks) o použití nebo ignorování interpunkce. 

## <a name="next-steps"></a>Další postup

Zobrazit [přidat entity](luis-how-to-add-entities.md) získat další informace o přidání entity do aplikace LUIS.

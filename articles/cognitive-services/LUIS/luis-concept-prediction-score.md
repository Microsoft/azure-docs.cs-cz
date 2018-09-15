---
title: Vysvětlení skóre předpovědi vrácený LUIS
titleSuffix: Azure Cognitive Services
description: Zjistěte, co předpovědi skóre znamená, že v LUIS
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: a7ac747f52ab355c4d611ec3634b19e98f349476
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2018
ms.locfileid: "45632582"
---
# <a name="prediction-score"></a>Skóre předpovědi
Predikce skóre označuje do jaké míry jistoty, LUIS má pro výsledky předpovědí. 

Predikce skóre je obvykle nula (0) a jedna (1). Příklad vysoce jistotu skóre LUIS je 0,99. Příklad s nízkou spolehlivostí skóre je 0,01. 

|Hodnota skóre|Spolehlivost|
|--|--|
|1|jednoznačného shody|
|0,99.|jistotou|
|0.01|s nízkou spolehlivostí|
|0|jednoznačného selhání tak, aby odpovídaly|

Když utterance výsledkem nízká pravděpodobnost, LUIS upozorňuje, že v [LUIS](luis-reference-regions.md) webu **záměr** stránka s identifikovanou **označené záměr** uvedených s red. 

![Nesrovnalosti skóre](./media/luis-concept-score/score-discrepancy.png)

## <a name="top-scoring-intent"></a>Nejvyšší hodnocení záměr
Každý předpovědi utterance vrátí záměr se nejvyšší hodnocení. Toto je číselné porovnání skóre předpovědi. Horní dva skóre může mít velmi malý rozdíl mezi nimi. Služba LUIS neukazuje, tento blízkosti než vrácení skóre.  

Pokud máte obavy o blízkosti horní skóre, měli byste vrátit skóre pro veškeré záměry. Projevy můžete přidat buď do dvou záměrů, které označují rozdíly mezi nimi se word a uspořádání, nebo se může mít LUIS volající aplikace, jako je například chatovacího robota, programové rozhodovat o tom, jak zpracovat dva hlavní záměry. 

Dva záměrů, které jsou příliš úzce skóre, může Invertovat kvůli nedeterministická školení. Začátek skóre může být ten druhý a druhou nejvyšší skóre se první hlavní skóre stát. Pokud chcete předejít, přidání projevů příklad ke každému horní dva záměrů pro tento utterance možnosti aplikace word a kontext, který rozlišuje dva záměry. O stejném počtu projevy příklad by měl mít dva záměry. Existuje pravidlo pro oddělení, které brání inverzi kvůli školení, je 15 % rozdíl v skóre.

## <a name="return-prediction-score-for-all-intents"></a>Vrátí skóre předpovědi pro všechny příkazy
Výsledek testu nebo koncový bod může zahrnovat všechny záměry. Tato konfigurace je nastavena na [koncový bod](https://aka.ms/v1-endpoint-api-docs) s `verbose=true` dvojice název/hodnota v řetězci dotazu. 

## <a name="review-intents-with-similar-scores"></a>Zkontrolujte záměry s podobné skóre
Kontrola skóre pro veškeré záměry je dobrým způsobem, jak ověřit, že se identifikuje správná záměr nejen, ale, že další identifikovat záměr na skóre je výrazně nižší konzistentně projevy. 

Pokud máte více záměry zavřít předpovědi skóre, na základě kontextu utterance, LUIS může přepínat mezi příkazů. Tento problém můžete pokračujte k přidání projevů na každý záměr s širší škálou kontextové rozdíly.   

## <a name="e-exponent-notation"></a>Zápis E (exponent)

Predikce skóre, které můžete použít exponentu zápis *povolí, nebude* vyšší než 0-1 v rozsahu, jako například `9.910309E-07`. Toto skóre je údaj o velmi **malé** číslo.

|Skóre zápis E |Skutečný výsledek|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="differences-with-predictions"></a>Rozdíly mezi predikcí
Při tréninku stejného modelu v jiné aplikaci a skóre se tomto neshodují, je to proto, že je element náhodnost při vzdělávání. Za druhé žádné překrytí utterance na více než jeden záměr znamená, že hlavní záměr pro stejný utterance lze změnit v závislosti na školení.

Pokud váš robot vyžaduje konkrétní skóre LUIS k označení důvěru v záměru, je vhodné použít skóre rozdíl mezi horní dva záměry. Získáte tak flexibilitu pro různé variace školení. 

## <a name="punctuation"></a>Interpunkce
Rozdělení je samostatný token v LUIS. Utterance, který obsahuje tečku na konci oproti utterance, který nemá jsou dvě samostatné projevy a může se zobrazit dvě různé předpovědi. Ujistěte se, že model buď zpracovává interpunkce v [příklad projevy](luis-concept-utterance.md) (mají a nemají interpunkční znaménka) nebo [patterns}(luis-concept-patterns.md), kde se snadněji ignorovat interpunkční znaménka zvláštní syntaxí: `I am applying for the {Job} position[.]`

## <a name="next-steps"></a>Další postup

Zobrazit [přidat entity](luis-how-to-add-entities.md) získat další informace o přidání entity do aplikace LUIS.
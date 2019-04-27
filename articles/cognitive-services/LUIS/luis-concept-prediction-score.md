---
title: Predikce skóre
titleSuffix: Language Understanding - Azure Cognitive Services
description: Predikce skóre označuje takovou úroveň důvěryhodnosti, rozhraní LUIS API služba má pro výsledky předpovědí, podle utterance uživatele.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 383ce4c4248f7e21f745f503c74a29cb613983e2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813985"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Predikce skóre znamená přesnost předpovědi pro záměr a entit

Predikce skóre označuje takovou úroveň důvěryhodnosti, LUIS má pro výsledky předpovědí, podle utterance uživatele.

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

Každý předpovědi utterance vrátí záměr se nejvyšší hodnocení. Tato predikce je číselné porovnání skóre předpovědi. Nejvyšší hodnocení 2 může mít velmi malý rozdíl mezi nimi. Služba LUIS neukazuje, tento blízkosti než návratu na začátek skóre.  

## <a name="return-prediction-score-for-all-intents"></a>Vrátí skóre předpovědi pro všechny příkazy

Výsledek testu nebo koncový bod může zahrnovat všechny záměry. Tato konfigurace je nastavena na [koncový bod](https://aka.ms/v1-endpoint-api-docs) s `verbose=true` dvojice název/hodnota v řetězci dotazu.

## <a name="review-intents-with-similar-scores"></a>Zkontrolujte záměry s podobné skóre

Kontrola skóre pro veškeré záměry je dobrým způsobem, jak ověřit, že se identifikuje správná záměr nejen, ale, že další identifikovat záměr na skóre je výrazně nižší konzistentně projevy.

Pokud máte více záměry zavřít předpovědi skóre, na základě kontextu utterance, LUIS může přepínat mezi příkazů. K vyřešení této situace, pokračujte v přidávání projevy na každý záměr s širší škálou kontextové rozdíly nebo můžete mít klientská aplikace, jako je chatovací robot, programové rozhodovat o tom, jak zpracovat 2 hlavní záměry.

2 záměrů, které jsou příliš úzce skóre, může Invertovat kvůli Nedeterministický školení. Začátek skóre může být ten druhý a druhou nejvyšší skóre se první hlavní skóre stát. Aby nedošlo k této situaci, přidání projevů příklad ke každému nejvyšší dvou příkazů pro tento utterance možnosti aplikace word a kontext, který odlišuje 2 záměry. O stejném počtu projevy příklad by měl mít dva záměry. Existuje pravidlo pro oddělení, které brání inverzi kvůli školení, je 15 % rozdíl v skóre.

Můžete ji vypnout Nedeterministický školení podle [školení se všemi daty](luis-how-to-train.md#train-with-all-data).

## <a name="differences-with-predictions-between-different-training-sessions"></a>Rozdíly mezi predikcí mezi různé školicích seminářů

Při tréninku stejného modelu v jiné aplikaci a skóre nejsou stejné, tento rozdíl je, protože je Nedeterministický školení (element náhodnost). Za druhé žádné překrytí utterance na více než jeden záměr znamená, že hlavní záměr pro stejný utterance lze změnit v závislosti na školení.

Pokud chatovací robot vyžaduje konkrétní skóre LUIS k označení důvěru v záměru, měli byste použít skóre rozdíl mezi horní dva záměry. Tato situace poskytuje flexibilitu pro různé variace školení.

## <a name="e-exponent-notation"></a>Zápis E (exponent)

Predikce skóre, které můžete použít exponentu zápis *povolí, nebude* vyšší než 0-1 v rozsahu, jako například `9.910309E-07`. Toto skóre je údaj o velmi **malé** číslo.

|Skóre zápis E |Skutečný výsledek|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="punctuation"></a>Interpunkce

Rozdělení je samostatný token v LUIS. Utterance, který obsahuje tečku na konci oproti utterance, aby neobsahoval tečku na konci jsou dvě samostatné projevy a může se zobrazit dvě různé předpovědi. Ujistěte se, že model buď zpracovává interpunkce v [příklad projevy](luis-concept-utterance.md) (mají a nemají interpunkční znaménka) nebo [vzory](luis-concept-patterns.md) kde se snadněji ignorovat interpunkční znaménka zvláštní syntaxí: `I am applying for the {Job} position[.]`

## <a name="next-steps"></a>Další postup

Zobrazit [přidat entity](luis-how-to-add-entities.md) získat další informace o přidání entity do aplikace LUIS.

---
title: Predikční skóre - LUIS
titleSuffix: Azure Cognitive Services
description: Skóre předpověď označuje stupeň spolehlivosti služby LUIS API má pro výsledky předpověď, na základě projevuživatele.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: b360bc82b80e834492b524acc5c4535b0409eda1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74280828"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Skóre předpovědi označují přesnost předpovědi pro záměr a entity

Skóre předpověď označuje stupeň spolehlivosti LUIS má pro výsledky předpovědi promluvy uživatele.

Predikce skóre je mezi nulou (0) a jeden (1). Příklad vysoce sebevědomé skóre LUIS je 0,99. Příkladem skóre nízké důvěry je 0,01. 

|Hodnota skóre|Spolehlivost|
|--|--|
|1|definitivní shoda|
|0.99|vysoká důvěra|
|0.01|nízká důvěra|
|0|definitivní selhání shody|

## <a name="top-scoring-intent"></a>Záměr s nejvyšším skóre

Každý utterance předpověď vrátí záměr nejvyšší bodování. Tato předpověď je číselné porovnání skóre předpovědi. 

## <a name="proximity-of-scores-to-each-other"></a>Blízkost skóre k sobě navzájem

Top 2 skóre může mít velmi malý rozdíl mezi nimi. Služba LUIS neznamená tuto blízkost jiné než vrácení nejvyšší skóre.  

## <a name="return-prediction-score-for-all-intents"></a>Vrátit skóre předpovědi pro všechny záměry

Výsledek testu nebo koncového bodu může zahrnovat všechny záměry. Tato konfigurace je nastavena na koncovém bodu pomocí správného páru názvu a hodnoty řetězce dotazu.

|Rozhraní API pro předpověď|Název řetězce dotazu|
|--|--|
|V3|`show-all-intents=true`|
|V2|`verbose=true`|

## <a name="review-intents-with-similar-scores"></a>Kontrola záměrů s podobným skóre

Kontrola skóre pro všechny záměry je dobrý způsob, jak ověřit, že je nejen správný záměr identifikován, ale že další identifikované záměrskóre je výrazně a konzistentně nižší pro projevy.

Pokud více záměry mají skóre close předpověď, na základě kontextu utterance, LUIS může přepínat mezi záměry. Chcete-li tuto situaci opravit, pokračujte v přidávání promluv y pro každý záměr s širší škálou kontextových rozdílů nebo můžete mít klientskou aplikaci, například chatovacího robota, provádět programová rozhodnutí o tom, jak zpracovat 2 hlavní záměry.

2 záměry, které jsou příliš-úzce skóroval, může invertovat v důsledku **non-deterministické školení**. Nejlepší skóre by se mohlo stát druhým nejlepším a druhým nejvyšším skóre by se mohlo stát prvním nejvyšším skóre. Chcete-li zabránit této situaci, přidejte příklad projevy pro každý z prvních dvou záměrů pro tento utterance s volbou slova a kontext, který odlišuje 2 záměry. Dva záměry by měl mít přibližně stejný počet příklad projevy. Pravidlem pro oddělení, aby se zabránilo inverzi v důsledku tréninku, je 15% rozdíl ve skóre.

**Nedeterministické trénování** můžete vypnout [trénováníse se všemi daty](luis-how-to-train.md#train-with-all-data).

## <a name="differences-with-predictions-between-different-training-sessions"></a>Rozdíly s předpovědí mezi různými školeními

Při trénování stejný model v jiné aplikaci a skóre nejsou stejné, tento rozdíl je, protože je **nedeterministické školení** (prvek náhodnosti). Za druhé jakékoli překrytí utterance na více než jeden záměr znamená, že hlavní záměr pro stejné utterance můžete změnit na základě školení.

Pokud váš chatbot vyžaduje konkrétní skóre LUIS k označení důvěry v záměr, měli byste použít rozdíl skóre mezi horní dva záměry. Tato situace poskytuje flexibilitu pro změny v tréninku.

**Nedeterministické trénování** můžete vypnout [trénováníse se všemi daty](luis-how-to-train.md#train-with-all-data).

## <a name="e-exponent-notation"></a>E (exponent) zápis

Predikční skóre můžete použít exponent zápisu, které se `9.910309E-07` _zobrazí_ nad 0-1 rozsah, například . Toto skóre je známkou velmi **malého** počtu.

|E notace skóre |Skutečné skóre|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="punctuation"></a>Interpunkční znaménka

[Přečtěte si další informace](luis-concept-utterance.md#punctuation-marks) o tom, jak používat nebo ignorovat interpunkci. 

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak přidat entity do aplikace LUIS, najdete v tématu [Přidání entit.](luis-how-to-add-entities.md)

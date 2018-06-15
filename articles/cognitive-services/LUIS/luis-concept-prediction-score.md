---
title: Pochopení předpovědi skóre vrácený LEOŠ - Azure | Microsoft Docs
description: Zjistěte, co znamená skóre předpovědi v LEOŠ
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 1620fe80b032245c6ca19279c3981dcff4b9820f
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "35343925"
---
# <a name="prediction-score"></a>Předpověď skóre
Skóre předpovědi označuje stupeň přitom jistotu, že má LEOŠ předpovědi výsledků. 

Skóre předpovědi je obvykle nula (0) a jedna (1). Příkladem vysoce jisti skóre LEOŠ je 0,99. Příkladem skóre nízkou spolehlivosti je 0,01. 

|Hodnota skóre|Spolehlivost|
|--|--|
|1|určit přesně shody|
|0,99|vysoce důvěryhodných|
|0.01|nízkou spolehlivosti|
|0|selhání určit přesně tak, aby odpovídaly|

Při utterance výsledkem spolehlivosti nízké skóre, LEOŠ označuje, že v [LEOŠ] [ LUIS] webu **záměr** stránka, se identifikovanou **s názvem bez přípony záměru**  uvedených s červenou. 

![Nesoulad mezi databází skóre](./media/luis-concept-score/score-discrepancy.png)

## <a name="top-scoring-intent"></a>Horní vyhodnocování záměru
Každý utterance předpovědi vrátí záměr se horní vyhodnocování. Toto je číselné porovnání skóre předpovědi. Horní dva skóre může mít velmi malý rozdíl mezi nimi. LEOŠ nebude znamenat tento blízkosti než vrácení skóre.  

Pokud máte obavy o blízko nejvyšší skóre, by měl vrátit skóre pro všechny záměry. Můžete buď přidat utterances do dvou tříd Intent, které indikují rozdíly mezi nimi s volbou word a uspořádání, nebo se může mít LEOŠ volání aplikaci, například chatbot, programová rozhodování o způsobu zpracování dva hlavní záměry. 

## <a name="return-prediction-score-for-all-intents"></a>Vrátí předpovědi skóre pro všechny záměry
Výsledek testu nebo koncový bod může zahrnovat všechny záměry. Tato konfigurace je nastavený na [koncový bod](https://aka.ms/v1-endpoint-api-docs) s `verbose=true` dvojici název – hodnota řetězce dotazu. 

## <a name="review-intents-with-similar-scores"></a>Zkontrolujte záměry s podobnou skóre
Kontrola skóre pro všechny záměry je dobrý způsob, jak ověřte, že je identifikován správný záměr nejen, ale že další identifikovat záměr na skóre je výrazně nižší konzistentně utterances. 

Pokud máte více tříd Intent zavřít předpovědi skóre, na základě kontextu utterance LEOŠ přepínání záměry. Chcete-li odstranit tento problém, pokračujte pro přidání utterances pro každý záměr s širší rozsah kontextové rozdíly.   

## <a name="e-exponent-notation"></a>Zápis E (exponent)

Skóre předpovědi můžete použít exponentu zápis, *zobrazování* větší než 0-1 v rozsahu, jako například `9.910309E-07`. Tato skóre je označením velmi **malé** číslo.

|Skóre zápis E |Skutečné skóre|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="differences-with-predictions"></a>Rozdíly mezi předpovědi
Když cvičení stejného modelu v jiné aplikaci a skóre se tento neshodují, je to proto, že je element náhodnosti v školení. Za druhé všechny překrytí utterance k více než jeden záměr znamená, že hlavní záměr pro stejné utterance můžete měnit v závislosti na školení.

Pokud vaše chatbot vyžaduje konkrétní skóre LEOŠ udávajících důvěru záměrem, měli byste místo toho použít skóre rozdíl mezi nejvyšší dvou tříd Intent. To nabízí flexibilitu pro rozdíly v školení. 

## <a name="next-steps"></a>Další postup

V tématu [přidat entity](luis-how-to-add-entities.md) Další informace o tom, jak přidat do aplikace LEOŠ entity.

[LUIS]:luis-reference-regions.md
---
title: Popisek entity příkladu utterance
titleSuffix: Azure Cognitive Services
description: Naučte se, jak na stránce s podrobnostmi záměru na utterance portálu LUIS označit entitu získanou počítačem s podsoučástmi v příkladu.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 87b7375b2da0effbc18fff6a7e9d67383c93a403
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2019
ms.locfileid: "74135107"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>Označení entity náročné na počítač v příkladu utterance

Označení entity v příkladu utterance ukazuje, že LUIS je příkladem entity a kde se entita může zobrazit v utterance. 

## <a name="labeling-machine-learned-entity"></a>Označování uživatelem zjištěné entity

Vezměte v úvahu frázi `hi, please I want a cheese pizza in 20 minutes`. 

1. Vyberte nejvíce vlevo a pak vyberte text v entitě nejvíce vpravo. _Úplná objednávka_ je označena na následujícím obrázku.

    > [!div class="mx-imgBorder"]
    > ![popisek dokončit](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)ou entitu, kterou získal počítač

1. V místním okně vyberte entitu. Entita označená jako dokončená objednávka Pizza zahrnuje všechna slova (zleva doprava v angličtině), která jsou označená. 

> [!TIP]
> Entity, které jsou k dispozici v místním okně, jsou relativní vzhledem k kontextu, ve kterém se text zobrazuje. Pokud máte například určitou počítačovou entitu s podporou strojového přístupu a vyberete text na třetí úrovni (označený názvem entity s označením "v příkladu utterance), jsou entity, které jsou k dispozici v místním okně, omezené na kontext dílčích komponent 3. Level (4. podsoučásti úrovně 4). 

## <a name="review-labeled-text"></a>Zkontrolovat text s popiskem

Po označení si Projděte příklad utterance. LUIS použije aktuální model na příklad utterance po návěští. Plná čára indikuje, že text byl označený. 

> [!div class="mx-imgBorder"]
> ![označený jako dokončený počítač – získaná entita](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="when-to-train"></a>Kdy naučit

Pokud má aktuální model podporovat vaši označenou entitu, ale příklad utterance pokračuje v zobrazování textu jako předpokládaný, ale není označený, můžete svou aplikaci naučit.  

## <a name="confirm-predicted-entity"></a>Potvrdit předpovězenou entitu

Pokud je indikátor vizuálu nad utterance, indikuje to, že text je předpovězený, ale _ještě není označený_. Chcete-li tuto předpověď převést na popisek, vyberte utterance a pak vyberte **Potvrdit entitu předpovědi**.

> [!div class="mx-imgBorder"]
> ![předpovědět kompletní entitu získanou počítačem](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

## <a name="label-subcomponent-entity-by-painting-with-entity-palette-cursor"></a>Entita popisku dílčí komponenty malováním pomocí ukazatele na paletě entity

1. Chcete-li opravit předpovědi (entity, které se zobrazí nad příkladem utterance), otevřete paletu entity. 

    > [!div class="mx-imgBorder"]
    > ![ová paleta entit pro](media/label-utterances/pizza-entity-palette-with-pizza-type-selected.png) získaná počítačová entita

1. Vyberte dílčí komponentu entity. Tato akce je vizuálně označena novým kurzorem. Po přesunutí na portálu se ukazatel myši sleduje. 

    > [!div class="mx-imgBorder"]
    > ![ová paleta entit pro](media/label-utterances/pizza-type-entity-palette-cursor.png) získaná počítačová entita

1. V příkladu utterance _malujte_ entitu pomocí kurzoru. 

    > [!div class="mx-imgBorder"]
    > ![ová paleta entit pro](media/label-utterances/paint-subcomponent-with-entity-palette-cursor.png) získaná počítačová entita

## <a name="labeling-matching-text-entities-to-a-machine-learned-entity"></a>Označování spárovaných textových entit na počítačově učenou entitu

Mezi párové textové entity patří předem připravené entity, entity regulárních výrazů a seznam entit. Můžete je přidat do konkrétního počítače, který je v rámci omezení na dílčí součást, když vytváříte nebo upravujete entitu získanou počítačem. 

**Po přidání těchto omezení není nutné označit shodný text v příkladu utterance.**

## <a name="entity-prediction-errors"></a>Chyby předpovědi entity

Chyby předpovědi entit ukazují indikátor upozornění. To znamená, že předpovězená entita neodpovídá označené entitě. 

> [!div class="mx-imgBorder"]
> ![ová paleta entit pro](media/label-utterances/example-utterance-indicates-prediction-error.png) získaná počítačová entita

## <a name="next-steps"></a>Další kroky

K vylepšení kvality předpovědi vaší aplikace použijte [řídicí panel](luis-how-to-use-dashboard.md) a [Prohlédněte si projevy koncového bodu](luis-how-to-review-endpoint-utterances.md) .

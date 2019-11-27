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
ms.date: 11/21/2019
ms.author: diberry
ms.openlocfilehash: 58e813d30273db4e011039aa43cd59c61507895e
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2019
ms.locfileid: "74383708"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>Označení entity náročné na počítač v příkladu utterance

Označení entity v příkladu utterance poskytuje LUIS příklad, co je entita a kde se entita může zobrazit v utterance. 

## <a name="labeling-machine-learned-entity"></a>Označování uživatelem zjištěné entity

Vezměte v úvahu frázi `hi, please I want a cheese pizza in 20 minutes`. 

1. Vyberte nejvíce vlevo a pak vyberte text v entitě nejvíce vpravo a potom v tomto případě vyberte entitu, se kterou chcete popisek provést. _Úplná objednávka_ je označena na následujícím obrázku.

    > [!div class="mx-imgBorder"]
    > ![popisek dokončit](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)ou entitu, kterou získal počítač

1. V místním okně vyberte entitu. Entita označená jako dokončená objednávka Pizza zahrnuje všechna slova (zleva doprava v angličtině), která jsou označená. 

## <a name="review-labeled-text"></a>Zkontrolovat text s popiskem

Po označení se podívejte na příklad utterance a ujistěte se, že vybraný rozsah textu byl podtržený zvolenou entitou. Plná čára indikuje, že text byl označený. 

> [!div class="mx-imgBorder"]
> ![označený jako dokončený počítač – získaná entita](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>Potvrdit předpovězenou entitu

Pokud je kolem rozsahu textu tečkovaný obdélník a název entity je nad utterance, znamená to, že text je předpovězený, ale _ještě není označený_. Chcete-li tuto předpověď převést na popisek, vyberte řádek utterance a pak vyberte **Potvrdit entitu předpovědi**.

> [!div class="mx-imgBorder"]
> ![předpovědět kompletní entitu získanou počítačem](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

Alternativně můžete vybrat název entity nad textem a pak vybrat **Potvrdit předpověď** z nabídky, která se zobrazí.

> [!div class="mx-imgBorder"]
> ![odhadnout entitu s](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted-menu.png)ou získanou počítačem

## <a name="label-entity-by-painting-with-entity-palette-cursor"></a>Popisek entity podle vykreslování pomocí ukazatele na paletě entity

Paleta entit nabízí alternativu k předchozímu prostředí označování. Umožňuje přenášet text na text, abyste ho mohli hned označit entitou.

1. Otevřete paletu entity tak, že vyberete ikonu zvýrazňovače v pravém horním rohu tabulky utterance. 

    > [!div class="mx-imgBorder"]
    > ![ová paleta entit pro](media/label-utterances/example-1-label-machine-learned-entity-palette.png) získaná počítačová entita

2. Vyberte součást entity. Tato akce je vizuálně označena novým kurzorem. Po přesunutí na portálu se ukazatel myši sleduje. 

    > [!div class="mx-imgBorder"]
    > ![ová paleta entit pro](media/label-utterances/example-1-label-machine-learned-entity-palette-menu.png) získaná počítačová entita

3. V příkladu utterance _malujte_ entitu pomocí kurzoru.

    > [!div class="mx-imgBorder"]
    > ![ová paleta entit pro](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png) získaná počítačová entita

## <a name="labeling-subcomponents-of-a-machine-learned-entity"></a>Označování dílčích součástí zjištěné entity počítače

Dílčí součásti v entitách jsou označeny stejným způsobem jako entity nejvyšší úrovně. Při výběru textu jsou entity, které jsou k dispozici v místním okně, relativní vzhledem k kontextu, ve kterém se text zobrazuje. Pokud máte například určitou počítačovou entitu s podporou strojového přístupu a vyberete text, který byl označený na 1 a 2 úrovni (označený názvem entity s označením v příkladu utterance), jsou entity, které jsou k dispozici v místním okně, omezené na kontext komponent na úrovni 3. Chcete-li text označit dalšími entitami, vyberte možnost **Označit jako jinou entitu** .

> [!div class="mx-imgBorder"]
> ![ová paleta entit pro](media/label-utterances/example-1-label-machine-learned-entity-subcomponent.png) získaná počítačová entita

Dílčí součásti lze označit pouze v případě, že je nadřazený také popisek. 

## <a name="unlabel-entities"></a>Odznačení entit

Chcete-li zrušit označení entity, vyberte název entity pod textem a vyberte možnost zrušit **označení**. Pokud entita, kterou se pokoušíte označit jako odznačení, má označené dílčí komponenty, pak musí být dílčí součásti před označením označeny jako první. 

## <a name="editing-labels-using-the-entity-palette"></a>Úprava popisků pomocí palety entit

Pokud při označování dojde k chybě, je paleta entit jednoduchý nástroj, který umožňuje rychlé úpravy. Pokud například popisek entity zahrnuje nadbytečné slovo omylem a již má označené dílčí komponenty, můžete použít paletu entit ke štětci přes požadované kratší rozsah slov.

Například:

1. Dílčí komponenta typu Pizza zahrnuje "sýr Pizza s", který zahrnuje nadbytečné nesprávné slovo--"with".

    > [!div class="mx-imgBorder"]
    > ![ová paleta entit pro](media/label-utterances/edit-label-with-palette-1.png) získaná počítačová entita

2. Pomocí palety entit můžete vybrat typ Pizza a štětec přes "sýr pizza". Výsledkem je, že se teď jako typ Pizza označí jenom sýr Pizza.

    > [!div class="mx-imgBorder"]
    > ![ová paleta entit pro](media/label-utterances/edit-label-with-palette-2.png) získaná počítačová entita


## <a name="labels-for-matching-text-entities"></a>Popisky pro párové entity textu

Mezi párové textové entity patří předem připravené entity, entity regulárních výrazů, seznam entit a vzor. jakékoli entity. Tyto jsou automaticky označeny pomocí LUIS, takže není nutné je ručně popsat podle uživatelů.

## <a name="entity-prediction-errors"></a>Chyby předpovědi entity

Chyby předpovědi entit označují, že předpovězená entita neodpovídá označené entitě. Toto je vizuálně s indikátorem upozornění vedle utterance.

> [!div class="mx-imgBorder"]
> ![ová paleta entit pro](media/label-utterances/example-utterance-indicates-prediction-error.png) získaná počítačová entita

## <a name="next-steps"></a>Další kroky

K vylepšení kvality předpovědi vaší aplikace použijte [řídicí panel](luis-how-to-use-dashboard.md) a [Prohlédněte si projevy koncového bodu](luis-how-to-review-endpoint-utterances.md) .
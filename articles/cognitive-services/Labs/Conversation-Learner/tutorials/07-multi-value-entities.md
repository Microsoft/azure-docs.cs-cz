---
title: Jak používat vícehodnotové entity s modelem Conversation Learner Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Naučte se používat entity s více hodnotami s modelem Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 7911dd561da0f1e7b62b1b457ae5b059d5d54767
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704091"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Používání vícehodnotových entit s modelem Conversation Learner
V tomto kurzu se zobrazuje vlastnost s více hodnotami entit.

## <a name="video"></a>Video

[![Kurz pro entity s více hodnotami – Preview](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby byl spuštěný robot pro obecné kurzy.

    npm run tutorial-general

## <a name="details"></a>Podrobnosti
Entity s více hodnotami sčítají hodnoty v seznamu místo uložení jedné hodnoty.  Tyto entity jsou užitečné, když uživatelé můžou zadat víc než jednu hodnotu. Toppings například na Pizza.

Entity označené jako multi-Value budou mít každou rozpoznanou instanci entity, která je připojena k seznamu v paměti robota. Následné rozpoznávání se připojí k hodnotě entity místo přepsání.

## <a name="steps"></a>Kroky

Začněte na domovské stránce ve webovém uživatelském rozhraní.

### <a name="create-the-model"></a>Vytvoření modelu

1. Vyberte **Nový model**.
2. Jako **název**zadejte **MultiValueEntities** .
3. Vyberte **Vytvořit**.

### <a name="entity-creation"></a>Vytvoření entity

1. Na levém panelu vyberte **entity** a pak **novou entitu**.
2. Vyberte **vlastní školení** pro **typ entity**.
3. Jako **název entity**zadejte **toppings** .
4. Zaškrtněte **,** Pokud chcete, aby entita mohla shromažďovat jednu nebo více hodnot.
5. Zkontroluje **negaci**.
6. Vyberte **Vytvořit**.

![](../media/T07_entity_create.png)

### <a name="create-the-first-action"></a>Vytvoření první akce

1. Na levém panelu vyberte **Akce** a pak **Nová akce**.
2. **Sem zadejte svoje toppings: $toppings** pro **odpověď robota...** Úvodní znak dolaru indikuje odkaz na entitu.
3. Vyberte **Vytvořit**.

![](../media/T07_action_create_1.png)

### <a name="create-the-second-action"></a>Vytvoření druhé akce

1. Na levém panelu vyberte **Akce** a pak **Nová akce**.
2. Zadejte, **co toppings** chcete pro **odpověď robota...**
3. Pro nekvalifikované **nároky**zadejte **toppings** .
4. Vyberte **Vytvořit**.

Nyní máte dvě akce.

![](../media/T07_action_create_2.png)

### <a name="train-the-model"></a>Výuka modelu

1. V levém panelu vyberte **dialogová okna výuka** a pak klikněte na **nový výukový dialog**.
2. Do levého panelu chatu zadejte **dobrý den** pro utterance uživatele.
3. Vyberte **Akce skóre**.
4. Vyberte, **co toppings chcete?** ze seznamu akcí. Percentil je 100% jako jediná platná akce založená na omezeních.
5. Do levého panelu chatu zadejte **sýry a žampiony** pro utterance uživatele.
6. Zvýrazněte **sýr** a pak vyberte **+ toppings**.
7. Zvýrazněte **žampiony** a pak vyberte **+ toppings**.
8. Vyberte **Akce skóre**.
9. V seznamu akcí vyberte **toppings: $toppings** .
10. Do levého panelu chatu zadejte pro další utterance uživatele **Přidat papriku** .
11. Zvýrazněte **papriku** a pak vyberte **+ toppings**.
12. Vyberte **Akce skóre**.
13. V seznamu akcí vyberte **toppings: $toppings** .
14. Do levého panelu chatu zadejte pro třetí utterance uživatele **Odebrat sýry** .
15. Zvýrazněte **sýr** a pak vyberte **-toppings**.
16. Vyberte **Akce skóre**.
17. V seznamu akcí vyberte **toppings: $toppings** .

![](../media/T07_training.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Předem připravené entity](./08-pre-trained-entities.md)

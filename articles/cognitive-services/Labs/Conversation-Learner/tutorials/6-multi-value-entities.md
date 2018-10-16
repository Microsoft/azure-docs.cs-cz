---
title: Jak používat vícehodnotový entity s modelem konverzace Learner – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Další informace o použití vícehodnotový entity s modelem Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: a80577bb226cbec080edf5e06dbd0f31c80a5890
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49321423"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Jak používat více hodnotami entity s modelem Learner konverzace
Tento kurz ukazuje vlastnost "vícehodnotový" entit.

## <a name="video"></a>Video

[![Kurz 6 Preview](http://aka.ms/cl-tutorial-06-preview)](http://aka.ms/blis-tutorial-06)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna obecné kurz bot

    npm run tutorial-general

## <a name="details"></a>Podrobnosti
Entitou, která je "vícehodnotový" nahromadí hodnot v seznamu a místo ukládání jednu hodnotu.  To je užitečné pro entity, ve kterém může uživatel zadat více než jednu hodnotu, jako je například toppings na pizza.

Namítají Pokud entita je označená jako "vícehodnotový", pak každá rozpoznaná instanci entity, připojí se do seznamu v bodu robotů také paměti (spíše než přepíše hodnotu jedné entity).

## <a name="steps"></a>Kroky

### <a name="create-the-model"></a>Vytvoření modelu

1. V webového uživatelského rozhraní klikněte na nový Model
2. Do pole Název zadejte MultiValueEntities. Pak klikněte na tlačítko vytvořit.

### <a name="create-an-entity"></a>Vytvoření entity

1. Klikněte na entity a pak novou entitu.
2. V názvu Entity zadejte Toppings.
3. Kontrola více Vážíme si toho.
    - Vícehodnotový entity accumulate jednu nebo více hodnot v dané entitě.
2. Kontrola Negatable.  
    - To vám umožní uživateli odebrat toppings ze svého seznamu nahromaděné pizza toppings.
3. Klikněte na Vytvořit.

![](../media/tutorial6_entities.PNG)

### <a name="create-two-actions"></a>Vytvořte dvě akce

1. Klikněte na tlačítko akce a potom novou akci
2. V odpovědi zadejte "jaké toppings požadujete?".
3. Vyřazení entity zadejte Toppings.
3. Kliknutí na Vytvořit

Vytvořte druhou akci.

1. Klikněte na tlačítko akce a pak novou akci na vytvoří druhou akci.
3. V odpovědi, zadejte "tady je váš toppings: $Toppings".
4. Kliknutí na Vytvořit

Nyní máte dvě akce.

![](../media/tutorial6_actions.PNG)

### <a name="train-the-bot"></a>Natrénování robota

1. Klikněte na dialogová okna trénování, pak nové dialogové okno trénování.
2. Zadejte "hello".
3. Klikněte na výsledek akce a vyberte "jaké toppings požadujete?"
2. Zadejte "hub a produkci minipivovarů ve wisconsinu". 
    - Nula, jeden nebo více než jeden z entity, které lze označit.
3. Klikněte na tlačítko "hub" a vyberte Toppings.
4. Klikněte na tlačítko "produkci minipivovarů ve wisconsinu" a vyberte Toppings.
5. Klikněte na výsledek akce
    - Tyto dvě hodnoty jsou teď k dispozici v entitě Toppings. 
6. Vyberte "tady je váš toppings: $Toppings".

Můžeme přidat více k tomuto:

7. Zadejte "Přidat papriky".
    - Klikněte na "papriky" v části rozpoznávání entit a vyberte Toppings.
3. Klikněte na výsledek akce.
    - "papriky" se teď zobrazí jako další hodnoty v Toppings.
6. Vyberte "tady je váš toppings: $Toppings".

Můžeme odebrat přičemž a přidejte ho:

2. Zadejte "odeberte papriky a přidejte salám".
1. Klikněte na "papriky" a klikněte na červený symbol x, abyste ji odebrali.
2. Klikněte na "papriky" a vyberte "-Toppings'.
3. Klikněte na výsledek akce.
    - byla odstraněna "papriky" a "salám" se přidala.
6. Vyberte "tady je váš toppings: $Toppings".

Nyní pojďme si vyzkoušet všechno, co odstranění:

6. Zadejte "hub odebrat, odeberte produkci minipivovarů ve wisconsinu a odeberte salám".
7. Klikněte na každý ze tří a vyberte "-Toppings'.
7. Klikněte na výsledek akce.
    - Zrušte zaškrtnutí všech toppings.
2. Vyberte "jaké toppings požadujete?"
3. Klikněte na Hotovo výuky

![](../media/tutorial6_dialogs.PNG)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Integrované entity](./7-built-in-entities.md)

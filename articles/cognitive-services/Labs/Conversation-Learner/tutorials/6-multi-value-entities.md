---
title: Jak používat s více hodnotami entity s aplikací konverzace student - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Naučte se používat s aplikací konverzace student entit s více hodnotami.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 865b50747b2c9574b5f88d4902bea9e4c8e0e032
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343240"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-application"></a>Jak používat s více hodnotami entity s konverzace student aplikací
Tento kurz ukazuje vlastnost "vícehodnotový" entit.

##<a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby obecné kurz robota běží

    npm run tutorial-general

## <a name="details"></a>Podrobnosti
Entitou, která je "vícehodnotový" shromažďuje hodnot v seznamu a místo ukládání jednu hodnotu.  To je užitečné pro entity, které může uživatel zadat více než jednu hodnotu, jako je například toppings na pizza.

Namítají Pokud entitu je označena jako "vícehodnotový", pak všechny uznávané instanci entity se připojí k seznam v robota paměti (nikoli přepsal hodnotu jedné entity).

## <a name="steps"></a>Kroky

### <a name="create-the-application"></a>Vytvoření aplikace

1. Ve webovém uživatelském rozhraní klikněte na nové aplikace
2. Do pole Název zadejte MultiValueEntities. Klikněte na tlačítko vytvořit.

### <a name="create-an-entity"></a>Vytvořte entitu

1. Klikněte na tlačítko entity, pak novou entitu.
2. Název Entity zadejte Toppings.
3. Kontrola více hodnotami.
    - Entity s více hodnotami hromadit jeden nebo více hodnot v entitě.
2. Zkontrolujte Negatable.  
    - To vám umožní uživatele k odebrání toppings z jejich seznam Akumulovaná pizza toppings.
3. Klikněte na Vytvořit.

![](../media/tutorial6_entities.PNG)

### <a name="create-two-actions"></a>Vytvořte dvě akce

1. Klikněte na tlačítko akce, pak nová akce
2. V odpovědi zadejte, jaký toppings chcete?".
3. Vyřazení entity zadejte Toppings.
3. Kliknutí na Vytvořit

Pak vytvořte druhou akci.

1. Klikněte na tlačítko akce pak nová akce vytvoří druhou akci.
3. V odpovědi, zadejte: Zde jsou vaše toppings: $Toppings'.
4. Kliknutí na Vytvořit

Nyní máte dvě akce.

![](../media/tutorial6_actions.PNG)

### <a name="train-the-bot"></a>Cvičení robota

1. Klikněte na tlačítko Train dialogová okna, dialogové okno pak nový vlaku.
2. Zadejte text "hello".
3. Klikněte na tlačítko akce skóre a vyberte, jaké toppings chcete?"
2. Zadejte "hub a sýr". 
    - Můžete Označit nula, jeden nebo více než jedna entita.
3. Klikněte na tlačítko 'hub' a vyberte Toppings.
4. Klikněte na tlačítko "sýry" a vyberte Toppings.
5. Klikněte na tlačítko akce skóre
    - Upozorňujeme, že tyto dvě hodnoty jsou teď součástí Toppings entity. 
6. Vyberte, zde jsou vaše toppings: $Toppings'.

Jsme můžete přidat více k tomuto:

7. Zadejte 'přidat zeleninová'.
    - Klikněte na 'paprik"v části Entity detekce a vyberte Toppings.
3. Klikněte na tlačítko akce skóre.
    - Všimněte si, že zeleninová se zobrazí jako další hodnota v Toppings.
6. Vyberte, zde jsou vaše toppings: $Toppings'.

Umožňuje odebrat přičemž a přidat:

2. Zadejte "Odebrat zeleninová a přidat salám".
1. Klikněte na "paprik" a klikněte na červené x jeho odebrání.
2. Klikněte na "paprik" a vyberte "-Toppings'.
3. Klikněte na tlačítko akce skóre.
    - Upozorňujeme, že byla odstraněna 'papriky zeleninové' a 'salám' byla přidána.
6. Vyberte, zde jsou vaše toppings: $Toppings'.

Nyní Pojďme zkuste odebrat vše:

6. Zadejte, odeberte hub, odeberte sýr a odeberte salám'.
7. Klikněte na každém ze tří a vyberte "-Toppings'.
7. Klikněte na tlačítko akce skóre.
    - Všimněte si, že všechny toppings vyjmuty.
2. Vyberte, jaké toppings chcete?"
3. Klikněte na tlačítko Hotovo vyučující

![](../media/tutorial6_dialogs.PNG)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Předdefinované entity](./7-built-in-entities.md)

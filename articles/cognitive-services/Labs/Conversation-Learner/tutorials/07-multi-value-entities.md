---
title: Jak vícehodnotový entity pomocí modelu Learner konverzace – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Další informace o použití vícehodnotový entity s modelem Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 8544d63f38f88a0e623dff343bf8b5133931b70b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228300"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Jak používat více hodnotami entity s modelem Learner konverzace
Tento kurz ukazuje vlastnost vícehodnotový entit.

## <a name="video"></a>Video

[![Parametr s více hodnotami entity kurz ve verzi Preview](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna obecné kurz bot

    npm run tutorial-general

## <a name="details"></a>Podrobnosti
Vícehodnotový entity kumulování hodnot v seznamu a místo ukládání jednu hodnotu.  Tyto entity jsou užitečné, pokud uživatelé mohou zadat více než jednu hodnotu. Toppings na pizza pro příklad.

Entity označené jako vícehodnotový bude mít každý rozpoznaný instanci Entity připojí v paměti bodu robotů také v seznamu. Následné schválení se připojí k hodnota Entity, než přepsání.

## <a name="steps"></a>Kroky

### <a name="create-the-model"></a>Vytvoření modelu

1. Ve webové uživatelské rozhraní klikněte na tlačítko "Nový Model."
2. V poli "Name" typ "MultiValueEntities" a stiskněte enter.
3. Klikněte na tlačítko "Vytvořit".

### <a name="entity-creation"></a>Vytvoření entity

1. Na levém panelu klikněte na tlačítko "," subjekty a potom na tlačítko "Nová entita".
2. Vyberte "Vlastní školení" u "Typu Entity."
3. Zadejte "toppings" u "Názvu Entity."
4. Zkontrolujte "Více Vážíme si toho" zaškrtávací políčko.
    - Vícehodnotový entity accumulate jednu nebo více hodnot v dané entitě.
5. Zaškrtněte políčko "Negatable".
    - Vlastnost "Negatable" se věnují další kurz.
6. Klikněte na tlačítko "Vytvořit".

![](../media/tutorial6_entities.PNG)

### <a name="create-the-first-action"></a>Vytvoření první akci

1. Na levém panelu klikněte na tlačítko "Akce" a potom na tlačítko "Nová akce".
2. "Bodu robotů také odpovědi na..." pole, zadejte "tady je váš toppings: $toppings"
    - Úvodní znak dolaru označuje odkazu na entitu
3. Klikněte na tlačítko "Vytvořit".

### <a name="create-the-second-action"></a>Vytvořte druhou akci

1. Na levém panelu klikněte na tlačítko "Akce" a potom na tlačítko "Nová akce".
2. "Bodu robotů také odpovědi na..." pole, zadejte "jaké toppings způsobem?"
3. V poli "Vyřazení opravňuje" typ "toppings."
4. Klikněte na tlačítko "Vytvořit".

Nyní máte dvě akce.

![](../media/tutorial6_actions.PNG)

### <a name="train-the-model"></a>Trénování modelu

1. Na levém panelu klikněte na tlačítko "Dialogů Train" a potom na tlačítko "Dialogové okno Nový Train".
2. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "Dobrý den."
3. Klikněte na tlačítko "Skóre akce".
4. Vyberte odpověď na "jaké toppings přejete?"
    - Na percentilu je 100 %, protože jediné platné akce podle omezení.
5. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "produkci minipivovarů ve wisconsinu a hub"
6. Klikněte na tlačítko "produkci minipivovarů ve wisconsinu" a vyberte popisek "toppings plus (+)
7. Klikněte na tlačítko "hub" a vyberte popisek "toppings plus (+)
8. Klikněte na tlačítko "Skóre akce".
9. Vyberte odpověď na "tady je váš toppings: $toppings"
10. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "Přidání pepř"
11. Klikněte na tlačítko "pepř" a vyberte popisek "toppings plus (+)
12. Klikněte na tlačítko "Skóre akce".
13. Vyberte odpověď na "tady je váš toppings: $toppings"
14. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "remove produkci minipivovarů ve wisconsinu."
15. Klikněte na tlačítko "produkci minipivovarů ve wisconsinu" a vyberte popisek "-toppings"
16. Klikněte na tlačítko "Skóre akce".
17. Vyberte odpověď na "tady je váš toppings: $toppings"

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Předem Vytrénovaných entity](./08-pre-trained-entities.md)

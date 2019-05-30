---
title: Jak vícehodnotový entity pomocí modelu Learner konverzace – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Další informace o použití vícehodnotový entity s modelem Learner konverzace.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 1f62def5e498f3f744beaed0cda207e1a75bfdf2
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387948"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Jak používat více hodnotami entity s modelem Learner konverzace
Tento kurz ukazuje vlastnost vícehodnotový entit.

## <a name="video"></a>Video

[![Vícehodnotový entity kurz ve verzi Preview](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna obecné kurzu robota

    npm run tutorial-general

## <a name="details"></a>Podrobnosti
Vícehodnotový entity kumulování hodnot v seznamu a místo ukládání jednu hodnotu.  Tyto entity jsou užitečné, pokud uživatelé mohou zadat více než jednu hodnotu. Toppings na pizza pro příklad.

Entity označené jako vícehodnotový bude mít každý rozpoznaný instanci Entity připojí v paměti bodu robotů také v seznamu. Následné schválení se připojí k hodnota Entity, než přepsání.

## <a name="steps"></a>Kroky

Začněte na domovskou stránku webového uživatelského rozhraní.

### <a name="create-the-model"></a>Vytvoření modelu

1. Vyberte **nový Model**.
2. Zadejte **MultiValueEntities** pro **název**.
3. Vyberte **Vytvořit**.

### <a name="entity-creation"></a>Vytvoření entity

1. Vyberte **entity** na levém panelu, pak **novou entitu**.
2. Vyberte **vlastní školení** pro **typ Entity**.
3. Zadejte **toppings** pro **název Entity**.
4. Zkontrolujte **více Vážíme si toho** umožňující Entity accumulate jednu nebo více hodnot.
5. Zkontrolujte **Negatable**.
6. Vyberte **Vytvořit**.

![](../media/T07_entity_create.png)

### <a name="create-the-first-action"></a>Vytvoření první akci

1. Vyberte **akce** na levém panelu, pak **novou akci**.
2. Zadejte **tady je váš toppings: $toppings** pro **odezvy bodu robotů také...** . Úvodní znak dolaru označuje odkazu na entitu.
3. Vyberte **Vytvořit**.

![](../media/T07_action_create_1.png)

### <a name="create-the-second-action"></a>Vytvořte druhou akci

1. Vyberte **akce** na levém panelu, pak **novou akci**.
2. Zadejte **jaké toppings přejete?** pro **odezvy bodu robotů také...** .
3. Zadejte **toppings** pro **vyřazení opravňuje**.
4. Vyberte **Vytvořit**.

Nyní máte dvě akce.

![](../media/T07_action_create_2.png)

### <a name="train-the-model"></a>Trénování modelu

1. Vyberte **trénování dialogů** na levém panelu, pak **dialogové okno Nový trénování**.
2. Zadejte **Dobrý den** pro uživatele utterance na panelu vlevo konverzace.
3. Vyberte **skóre akce**.
4. Vyberte **jaké toppings přejete?** ze seznamu akcí. Na percentilu je 100 % jako jediné platné akce podle omezení.
5. Zadejte **produkci minipivovarů ve wisconsinu a hub** pro uživatele utterance na panelu vlevo konverzace.
6. Zvýrazněte **produkci minipivovarů ve wisconsinu** vyberte **+ toppings**.
7. Zvýrazněte **hub** vyberte **+ toppings**.
8. Vyberte **skóre akce**.
9. Vyberte **tady je váš toppings: $toppings** ze seznamu akcí.
10. Zadejte **přidat pepř** pro další utterance uživatele na panelu vlevo konverzace.
11. Zvýrazněte **pepř** vyberte **+ toppings**.
12. Vyberte **skóre akce**.
13. Vyberte **tady je váš toppings: $toppings** ze seznamu akcí.
14. Zadejte **odebrat produkci minipivovarů ve wisconsinu** pro třetí utterance uživatele na panelu vlevo konverzace.
15. Zvýrazněte **produkci minipivovarů ve wisconsinu** vyberte **-toppings**.
16. Vyberte **skóre akce**.
17. Vyberte **tady je váš toppings: $toppings** ze seznamu akcí.

![](../media/T07_training.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Předem Vytrénovaných entity](./08-pre-trained-entities.md)

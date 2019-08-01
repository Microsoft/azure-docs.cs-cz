---
title: Postup použití operátorů negace s modelem Conversation Learner Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Naučte se používat neConversation Learnerelné entity s modelem.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 4edbfc396d4ab32b991b084a7b738f04b5205418
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704112"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Použití operátoru negace s modelem Conversation Learner

V tomto kurzu se dozvíte o vlastnosti entit typu "pro negaci".

## <a name="video"></a>Video

[![Kurz pro negaci entit ve verzi Preview](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby byl spuštěný robot pro obecné kurzy.

    npm run tutorial-general

## <a name="details"></a>Podrobnosti
Vlastnost "negace" entity umožňuje označit normální (pozitivní) i záporné instance entity, naučit se na základě pozitivních a záporných modelů a vymazat hodnotu existující entity. Entity, u kterých je nastavena vlastnost "negace", se nazývají jako neodpovídající entity v štíhlé konverzaci.

## <a name="steps"></a>Kroky

Začněte na domovské stránce ve webovém uživatelském rozhraní.

### <a name="create-the-model"></a>Vytvoření modelu

1. Vyberte **Nový model**.
2. Jako **název**zadejte **NegatableEntity** .
3. Vyberte **Vytvořit**.

### <a name="entity-creation"></a>Vytvoření entity

1. Na levém panelu vyberte **entity** a pak **novou entitu**.
2. Vyberte **vlastní školení** pro **typ entity**.
3. Zadejte **název** **entity**.
4. Zaškrtněte možnost negace, pokud chcete uživatelům umožnit zadat hodnotu entity, nebo když něco *není hodnota* entity, tím se odstraní hodnota odpovídajícího subjektu.
5. Vyberte **Vytvořit**.

![](../media/T06_entity_create.png)

### <a name="create-the-first-action"></a>Vytvoření první akce

1. Na levém panelu vyberte **Akce** a pak **Nová akce**.
2. Zadejte **jméno,** které neznáte. pro **odpověď robota...**
3. Zadejte **název** pro **vyřazení nároků**.
4. Vyberte **Vytvořit**.

![](../media/T06_action_create_1.png)

### <a name="create-the-second-action"></a>Vytvoření druhé akce

1. Na levém panelu vyberte **Akce** a pak **Nová akce**.
2. Zadejte **název, který znáte. Je $name.** pro **odpověď robota...**
3. Vyberte **Vytvořit**.

> [!NOTE]
> **Název** entity byl automaticky přidán jako **požadované entity** podle odkazu v utterance odpovědi.

Nyní máte dvě akce.

![](../media/T06_action_create_2.png)

### <a name="train-the-model"></a>Výuka modelu

1. V levém panelu vyberte **dialogová okna výuka** a pak klikněte na **nový výukový dialog**.
2. Do levého panelu chatu zadejte Hello pro uživatele utterance.
3. Vyberte **Akce skóre**.
4. Vyberte **, že neznáte své jméno.** ze seznamu akcí. Percentil je 100% jako jediná platná akce založená na omezeních.
5. Do levého panelu chatu zadejte **jméno Jan** utterance uživatele.
6. Zvýrazněte **Jan** a pak vyberte **+ název**. Entity s možností negace mají dvě instance: (+) plus přidává nebo Přepisuje hodnotu; (-) minus odebere hodnotu.
7. Vyberte **Akce skóre**. **Název** entity je teď v paměti modelu definovaný jako **Jan** **, takže si poznáte své jméno. Je to $name** akce je k dispozici.
8. Vyberte **název, který znáte. Je $name.** ze seznamu akcí.
9. Zadejte **moje jméno není Jan.** pro uživatele utterance na levém panelu chatu.
10. Zvýrazněte **Jan** a potom vyberte **-Name** pro vymazání hodnoty z entity **Name (název** ).
11. Vyberte **Akce skóre**.
12. Vyberte **, že neznáte své jméno.** ze seznamu akcí.
13. Zadejte **moje jméno Zuzana.** pro třetí utterance uživatele na levém panelu chatu.
14. Zvýrazněte **Zuzana** a **+ Name** 

![](../media/T06_training.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Entity s více hodnotami](./07-multi-value-entities.md)

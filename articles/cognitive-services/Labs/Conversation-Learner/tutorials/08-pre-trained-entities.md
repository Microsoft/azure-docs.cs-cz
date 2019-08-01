---
title: Postup přidání předem vyškolených entit do modelu Conversation Learner Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Naučte se, jak přidat předem připravené entity do modelu Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: fceca459b80725e1a9c8b7dbc6a4387ea98dbb7b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704120"
---
# <a name="how-to-add-pre-trained-entities"></a>Postup přidání předem proučených entit
V tomto kurzu se dozvíte, jak přidat předem připravené entity do modelu Conversation Learner.

## <a name="video"></a>Video

[![Kurz předučených entit ve verzi Preview](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities_Preview)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby byl spuštěný robot pro obecné kurzy.

    npm run tutorial-general

## <a name="details"></a>Podrobnosti

Předem připravené entity rozpoznávají běžné typy entit, jako jsou čísla, kalendářní data, peněžní částky a další.  Fungují "předem připravené", nevyžadují žádné školení a jejich chování nelze změnit na rozdíl od vlastních entit.  Ve výchozím nastavení jsou předem připravené entity s více hodnotami a nahromadění všech identifikovaných instancí entity.

## <a name="steps"></a>Kroky

Začněte na domovské stránce ve webovém uživatelském rozhraní.

### <a name="create-the-model"></a>Vytvoření modelu

1. Vyberte **Nový model**.
2. Jako **název**zadejte **PretrainedEntities** .
3. Vyberte **Vytvořit**.

### <a name="entity-creation"></a>Vytvoření entity

1. Na levém panelu vyberte **entity** a pak **novou entitu**.
2. Pro **typ entity**vyberte **předem vyškolený/datetimeV2** .
3. Zaškrtněte **,** Pokud chcete, aby entita mohla shromažďovat jednu nebo více hodnot. Poznámka: předem připravené entity nemůžou být pro negaci.
4. Vyberte **Vytvořit**.

![](../media/T08_entity_create.png)

1. Na levém panelu vyberte **Akce** a pak **Nová akce**.
2. Zadejte **datum $Builtin – datetimev2** pro **odpověď robota...**
3. Vyberte **Vytvořit**.

![](../media/T08_action_create_1.png)

### <a name="create-the-second-action"></a>Vytvoření druhé akce

1. Na levém panelu vyberte **Akce** a pak **Nová akce**.
2. Zadejte **, co je datum?** pro **odpověď robota...** . Předem připravené entity nemůžou být **požadované entity** , protože jsou ve výchozím nastavení rozpoznávány pro všechny projevy.
3. Pro **nekvalifikované entity**zadejte **Builtin-datetimev2** .
4. Vyberte **Vytvořit**.

![](../media/T08_action_create_2.png)

### <a name="train-the-model"></a>Výuka modelu

1. V levém panelu vyberte **dialogová okna výuka** a pak klikněte na **nový výukový dialog**.
2. Do levého panelu chatu zadejte Hello pro uživatele utterance.
3. Vyberte **Akce skóre**.
4. Vyberte, **co je datum?** ze seznamu akcí
5. Do levého panelu chatu zadejte dnešního utterance uživatele.
    - Utterance je **dnes** automaticky rozpoznáno předem vyškolenými modely v Luis.
    - Najetí myší na hodnoty předem proučených entit zobrazuje další data, která poskytuje LUIS.

![](../media/T08_training.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Překladače entit](./09-entity-resolvers.md)

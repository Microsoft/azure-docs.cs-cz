---
title: Postup přidání Pre-Trained entity do modelu Learner konverzace – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Zjistěte, jak přidat Pre-trained entity do modelu Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: dade442cb04f79ef75e65d6eb29128b105e72b40
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565406"
---
# <a name="how-to-add-pre-trained-entities"></a>Postup přidání Pre-trained entity
Tento kurz ukazuje, jak přidat Pre-Trained entity do modelu Learner konverzace.

## <a name="video"></a>Video

[![Kurz ve verzi Preview předem Vytrénovaných entity](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities_Preview)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna obecné kurzu robota

    npm run tutorial-general

## <a name="details"></a>Podrobnosti

Předem Vytrénovaných entity rozpoznávání běžných typů entit, jako je například čísla, kalendářní data, peněžních částek a dalších.  Fungují "out-of-the-box," nevyžadují žádné školení a nedá se změnit jejich chování, na rozdíl od vlastních entit.  Ve výchozím nastavení, Pre-Trained entity jsou více Vážíme si toho, shromažďování všech identifikovaných instanci Entity.

## <a name="steps"></a>Kroky

Začněte na domovskou stránku webového uživatelského rozhraní.

### <a name="create-the-model"></a>Vytvoření modelu

1. Vyberte **nový Model**.
2. Zadejte **PretrainedEntities** pro **název**.
3. Vyberte **Vytvořit**.

### <a name="entity-creation"></a>Vytvoření entity

1. Vyberte **entity** na levém panelu, pak **novou entitu**.
2. Vyberte **Trained Předprodukčním, datetimeV2** pro **typ Entity**.
3. Zkontrolujte **více Vážíme si toho** umožňující Entity accumulate jednu nebo více hodnot. Mějte na paměti, nemůže být negatable Pre-Trained entity.
4. Vyberte **Vytvořit**.

![](../media/T08_entity_create.png)

1. Vyberte **akce** na levém panelu, pak **novou akci**.
2. Zadejte **datum je $builtin-datetimev2** pro **odezvy bodu robotů také...** .
3. Vyberte **Vytvořit**.

![](../media/T08_action_create_1.png)

### <a name="create-the-second-action"></a>Vytvořte druhou akci

1. Vyberte **akce** na levém panelu, pak **novou akci**.
2. Zadejte **jaké je datum?** pro **odezvy bodu robotů také...** . Předem Vytrénovaných entity nemůže být **požadované entity** rozpoznané ve výchozím nastavení pro všechny projevy.
3. Zadejte **builtin datetimev2** pro **vyřazení entity**.
4. Vyberte **Vytvořit**.

![](../media/T08_action_create_2.png)

### <a name="train-the-model"></a>Trénování modelu

1. Vyberte **trénování dialogů** na levém panelu, pak **dialogové okno Nový trénování**.
2. Zadejte **hello** pro uživatele utterance na panelu vlevo konverzace.
3. Vyberte **skóre akce**.
4. Vyberte **jaké je datum?** ze seznamu akcí
5. Zadejte **Dnes** pro uživatele utterance na panelu vlevo konverzace.
    - **Dnes** utterance je automaticky rozpoznán předem natrénovaných modelů služby LUIS.
    - Ukazatel myši hodnoty entit Pre-Trained zobrazuje další data poskytované služby LUIS.

![](../media/T08_training.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Překladače entity](./09-entity-resolvers.md)

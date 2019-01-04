---
title: Postup přidání Pre-Trained entity do modelu Learner konverzace – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Zjistěte, jak přidat Pre-trained entity do modelu Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: c198dfc19a350188f500af86c531be9a9ac424ce
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796710"
---
# <a name="how-to-add-pre-trained-entities"></a>Postup přidání Pre-trained entity
Tento kurz ukazuje, jak přidat Pre-Trained entity do modelu Learner konverzace.

## <a name="video"></a>Video

[![Kurz ve verzi Preview předem Vytrénovaných entity](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities_Preview)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna obecné kurz bot

    npm run tutorial-general

## <a name="details"></a>Podrobnosti

Předem Vytrénovaných entity rozpoznávání běžných typů entit, jako je například čísla, kalendářní data, peněžních částek a dalších.  Fungují "out-of-the-box," nevyžadují žádné školení a nedá se změnit jejich chování, na rozdíl od vlastních entit.  Ve výchozím nastavení, Pre-Trained entity jsou více Vážíme si toho, shromažďování všech identifikovaných instanci Entity.

## <a name="steps"></a>Kroky

### <a name="create-the-model"></a>Vytvoření modelu

1. Ve webové uživatelské rozhraní klikněte na tlačítko "Nový Model."
2. V poli "Name" typ "PretrainedEntities" a stiskněte enter.
3. Klikněte na tlačítko "Vytvořit".

### <a name="entity-creation"></a>Vytvoření entity

1. Na levém panelu klikněte na tlačítko "," subjekty a potom na tlačítko "Nová entita".
2. Vyberte "Předprodukční-Trained/datetimeV2" u "Typu Entity."
3. Zkontrolujte "Více Vážíme si toho" zaškrtávací políčko.
    - Vícehodnotový entity accumulate jednu nebo více hodnot v dané entitě.
    - Negatable vlastnosti jsou zakázané Pre-Trained entit.
4. Klikněte na tlačítko "Vytvořit".

![](../media/tutorial7_entities_a.PNG)

### <a name="create-the-first-action"></a>Vytvoření první akci

1. Na levém panelu klikněte na tlačítko "Akce" a potom na tlačítko "Nová akce".
2. "Bodu robotů také odpovědi na..." pole, zadejte "datum je $builtin-datetimev2"
3. Klikněte na tlačítko "Vytvořit".

![](../media/tutorial7_actions_a.PNG)

### <a name="create-the-second-action"></a>Vytvořte druhou akci

1. Na levém panelu klikněte na tlačítko "Akce" a potom na tlačítko "Nová akce".
2. "Bodu robotů také odpovědi na..." pole, zadejte "Jaké je datum?"
    - Předem Vytrénovaných entity nemůže být požadované entity rozpoznané ve výchozím nastavení pro všechny projevy uživatele.
3. V poli "Vyřazení opravňuje" typ "builtin datetimev2."
4. Klikněte na tlačítko "Vytvořit".

![](../media/tutorial7_actions2_a.PNG)

### <a name="train-the-model"></a>Trénování modelu

1. Na levém panelu klikněte na tlačítko "Dialogů Train" a potom na tlačítko "Dialogové okno Nový Train".
2. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "hello".
3. Klikněte na tlačítko "Skóre akce".
4. Vyberte odpověď na "Jaké je datum?"
5. V panelu chatu, kde stavu "Typ zprávy...", zadejte "dnes"
    - Informací o dnešku utterance je automaticky rozpoznán předem natrénovaných modelů služby LUIS.
    - Ukazatel myši hodnoty entit Pre-Trained zobrazuje další data poskytované služby LUIS.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Překladače entity](./09-entity-resolvers.md)

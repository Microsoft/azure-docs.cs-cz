---
title: Jak používat Negatable entity pomocí modelu Learner konverzace – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Další informace o použití Negatable entity s modelem Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 34806803c50308a288007bf3bad129075ca8bc6d
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796766"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Jak používat Negatable entity s modelem Learner konverzace

Tento kurz ukazuje vlastnost "Negatable" entit.

## <a name="video"></a>Video

[![Kurz ve verzi Preview negatable entity](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna obecné kurz bot

    npm run tutorial-general

## <a name="details"></a>Podrobnosti
Vlastnost "Negatable" entity umožňuje označovat popisky obou normální (pozitivní) a zápornou instance entity, představuje založené na kladné a záporné modely a smazat hodnotu existující Entity. Entity s jejich nastavenou vlastnost "Negatable" nazývají Negatable entity v Štíhlejší konverzace.

## <a name="steps"></a>Kroky

### <a name="create-the-model"></a>Vytvoření modelu

1. Ve webové uživatelské rozhraní klikněte na tlačítko "Nový Model."
2. V poli "Name" typ "NegatableEntity" a stiskněte enter.
3. Klikněte na tlačítko "Vytvořit".

### <a name="entity-creation"></a>Vytvoření entity

1. Na levém panelu klikněte na tlačítko "," subjekty a potom na tlačítko "Nová entita".
2. Vyberte možnost "Vlastní" u "Typu Entity."
3. Zadejte "name" u "Názvu Entity."
4. Zaškrtněte políčko "Negatable".
    - Kontroluje se tato vlastnost umožňuje uživateli zadat hodnotu entity nebo Dejme tomu, že se něco *není* hodnota entity. V druhém případě je výsledek odstranění odpovídající hodnota entity.
5. Klikněte na tlačítko "Vytvořit".

![](../media/tutorial5_entities.PNG)

### <a name="create-the-first-action"></a>Vytvoření první akci

1. Na levém panelu klikněte na tlačítko "Akce" a potom na tlačítko "Nová akce".
2. "Bodu robotů také odpovědi na..." pole, zadejte "Nevím své jméno."
3. V poli "Vyřazení opravňuje" typ "name".
4. Klikněte na tlačítko "Vytvořit".

### <a name="create-the-second-action"></a>Vytvořte druhou akci

1. Na levém panelu klikněte na tlačítko "Akce" a potom na tlačítko "Nová akce".
2. "Bodu robotů také odpovědi na..." pole, zadejte "poznám, že vaše jméno. Je $name."
3. Klikněte na tlačítko "Vytvořit".

> [!NOTE]
> "Název" Entity se automaticky přidal jako "Požadované entity" podle odkazu v odpovědi.

Nyní máte dvě akce.

![](../media/tutorial5_actions.PNG)

### <a name="train-the-model"></a>Trénování modelu

1. Na levém panelu klikněte na tlačítko "Dialogů Train" a potom na tlačítko "Dialogové okno Nový Train".
2. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "hello".
3. Klikněte na tlačítko "Skóre akce".
4. Vyberte odpověď na "Nevím své jméno."
    - Na percentilu je 100 %, protože jediné platné akce podle omezení.
5. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "Moje jméno je Frank"
6. Vyberte "Frank" a vyberte popisek "+ název"
    - Existují dvě instance pro "name" Entity: "+ Jmeno" a "-name".  (+) A navíc přidává nebo přepíše hodnotu. (-) Minus odebere hodnotu.
7. Klikněte na tlačítko "Skóre akce".
    - "name" Entity teď je definovaný jako "Jan" v paměti modelu, tak "poznám, že vaše jméno. Je $name"akce je k dispozici.
8. Vyberte odpověď na "poznám, že vaše jméno. Je $name."
9. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "Moje jméno není Frank."
10. Vyberte "Frank" a vyberte popisek "-name"
    - Volíme "-name" zrušte aktuální hodnota Entity.
11. Klikněte na tlačítko "Skóre akce".
12. Vyberte odpověď na "Nevím své jméno."
13. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "Moje jméno je Susan."
14. Vyberte "Susan" a vyberte popisek "+ název"

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vícehodnotový entity](./07-multi-value-entities.md)

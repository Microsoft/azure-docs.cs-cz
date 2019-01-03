---
title: Jak používat entity pomocí modelu Learner konverzace – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Informace o používání entit s modelem Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 4239cbfe369060516653145b04a2bcfa0882f20d
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796737"
---
# <a name="introduction-to-entities"></a>Úvod do entit

Tento kurz představuje entity, vyřazení entity, vyžaduje entit a jejich použití v rámci Learner konverzace.

## <a name="video"></a>Video

[![Úvod do entit kurz ve verzi Preview](https://aka.ms/cl_Tutorial_v3_IntroEntities_Preview)](https://aka.ms/cl_Tutorial_v3_IntroEntities)

## <a name="requirements"></a>Požadavky

Tento kurz vyžaduje, zda je spuštěna obecné kurzu robota

    npm run tutorial-general

## <a name="details"></a>Podrobnosti

Entity zachytit informace vyžadující robota k provádění svých úkolů, buď prostřednictvím extrakce z projevy uživatele nebo přiřazení vlastním kódem. Entity, sami lze také omezit dostupnost akce podle klasifikován explicitně jako "Required" nebo "Vyřazením."

- Požadované entity musí být obsažená v paměti modelu v pořadí pro akce, která má být k dispozici
- Vyřazení entity musí *není* nacházet v paměti modelu v pořadí pro akce, která má být k dispozici

Tento kurz se zaměřuje na vlastní entity. Předem školení, více hodnotami, Negatable a programové entit jsou popsané v dalších kurzech.

## <a name="steps"></a>Kroky

### <a name="create-the-model"></a>Vytvoření modelu

1. Ve webové uživatelské rozhraní klikněte na tlačítko "Nový Model."
2. V poli "Name" typ "IntroToEntities" a stiskněte enter.
3. Klikněte na tlačítko "Vytvořit".

### <a name="entity-creation"></a>Vytvoření entity

1. Na levém panelu klikněte na tlačítko "," subjekty a potom na tlačítko "Nová entita".
2. Vyberte "Vlastní školení" u "Typu Entity."
3. Zadejte "city" u "Názvu Entity."
4. Klikněte na tlačítko "Vytvořit".

> [!NOTE]
> Vlastní Trénovaného typ entity znamená, že Trénink tuto entitu, na rozdíl od jiných typů entit.

### <a name="create-the-actions"></a>Vytvoření akce

1. Na levém panelu klikněte na tlačítko "Akce" a potom na tlačítko "Nová akce".
2. "Bodu robotů také odpovědi na..." pole, zadejte "Nevím městě chcete, aby."
3. V poli "Vyřazení entity" typ "city".
4. Klikněte na tlačítko "Vytvořit".

> [!NOTE]
> Přidání "city" Entity do "vyřazení"subjekty by tato akce v úvahu bodu robotů také vyřadit, když "city" Entity je definována v paměti bodu robotů také.

Nyní vytvoří druhou akci.

1. Na levém panelu klikněte na tlačítko "Akce" a potom na tlačítko "Nová akce".
2. "Bodu robotů také odpovědi na..." pole, zadejte "o počasí v $city je pravděpodobně hezky."
3. Klikněte na tlačítko "Vytvořit".

> [!NOTE]
> "city" Entity se přidala automaticky v seznamu požadovaných entit pomocí odkazu v odpovědi.

![](../media/tutorial3_actions.PNG)

### <a name="train-the-model"></a>Trénování modelu

1. Na levém panelu klikněte na tlačítko "Dialogů Train" a potom na tlačítko "Dialogové okno Nový Train".
2. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "hello".
    - To simuluje uživatele na straně konverzace.
3. Klikněte na tlačítko "Skóre akce".
4. Vyberte odpověď na "Nevím městě chcete, aby."
5. Jako uživatel, odpoví, "Seattle".
6. Klikněte na tlačítko "Skóre akce".
7. Vyberte odpověď, "O počasí v $city je pravděpodobně hezky."
8. Klikněte na tlačítko "Save".

![](../media/tutorial3_entities.PNG)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Očekávanou entitu](./05-expected-entity.md)

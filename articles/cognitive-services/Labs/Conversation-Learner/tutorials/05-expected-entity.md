---
title: Jak používat vlastnost "Očekáván Entity" konverzace Learner akce – služba Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Zjistěte, jak používat vlastnost "Očekáván Entity" modelu Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: c6696d0f22026ac333c526b505732d15a4b01be7
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796728"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>Jak používat vlastnost "Očekáván Entity" akcí

Tento kurz ukazuje vlastnost "Očekáván Entity" akcí.

## <a name="video"></a>Video

[![Kurz ve verzi Preview očekávanou entitu](https://aka.ms/cl_Tutorial_v3_ExpectedEntity_Preview)](https://aka.ms/cl_Tutorial_v3_ExpectedEntity)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna obecné kurz bot

    npm run tutorial-general

## <a name="details"></a>Podrobnosti
Použijte vlastnost "Očekáván Entity" akci Uložit odpověď uživatele na tuto akci do Entity.

Po přidání entity do vlastnost "Očekáván Entity" akce, bude systém:

1. Začněte tím, že pokusu o shodu entit s využitím strojového učení na základě modelu extrakce Entity
2. Přiřadit utterance celé uživatelské $entity podle heuristickými metodami, pokud nejsou nalezeny žádné entity
3. Volání `EntityDetectionCallback`a pokračujte na výběr akce.

## <a name="steps"></a>Kroky

### <a name="create-the-model"></a>Vytvoření modelu

1. Ve webové uživatelské rozhraní klikněte na tlačítko "Nový Model."
2. V poli "Name" typ "ExpectedEntities" a stiskněte enter.
3. Klikněte na tlačítko "Vytvořit".

### <a name="entity-creation"></a>Vytvoření entity

1. Na levém panelu klikněte na tlačítko "," subjekty a potom na tlačítko "Nová entita".
2. Vyberte "Vlastní školení" u "Typu Entity."
3. Zadejte "name" u "Názvu Entity."
4. Klikněte na tlačítko "Vytvořit".

> [!NOTE]
> Vlastní Trénovaného typ entity znamená, že Trénink tuto entitu, na rozdíl od jiných typů entit.

![](../media/tutorial4_entities.PNG)

### <a name="create-the-first-action"></a>Vytvoření první akci

1. Na levém panelu klikněte na tlačítko "Akce" a potom na tlačítko "Nová akce".
2. "Bodu robotů také odpovědi na..." pole, zadejte "Jaký je název vašeho?"
3. V poli "očekáván subjekty" zadejte "name".
4. Klikněte na tlačítko "Vytvořit".

> [!NOTE]
> Entity zjištěna a extrahovat z odpovědi uživatele se uloží do entity "name" Pokud je vybrána tato akce. Pokud nejsou zjištěny žádné entity, celá odpověď se uloží do této entity.

### <a name="create-the-second-action"></a>Vytvořte druhou akci

1. Na levém panelu klikněte na tlačítko "Akce" a potom na tlačítko "Nová akce".
2. "Bodu robotů také odpovědi na..." pole, zadejte "Hi $name!"
3. Klikněte na tlačítko "Vytvořit".

> [!NOTE]
> "Název" Entity se automaticky přidal jako "Požadované entity" podle odkazu v odpovědi.

Nyní máte dvě akce.

![](../media/tutorial4_actions.PNG)

### <a name="train-the-model"></a>Trénování modelu

1. Na levém panelu klikněte na tlačítko "Dialogů Train" a potom na tlačítko "Dialogové okno Nový Train".
2. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "Dobrý den."
    - To simuluje uživatele na straně konverzace.
3. Klikněte na tlačítko "Skóre akce".
4. Vyberte odpověď na "Jaký je název vašeho?"
    - "Hi $name!" odpověď nejde vybrat, protože vyžaduje "name" entita, která má být definován v paměti modelu teď tuto odpověď.
5. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "Frank."
    - "Frank" je zvýrazněn jako Entity podle heuristiky, který nastavíme starší uložit odpověď jako Entity.
6. Klikněte na tlačítko "Skóre akce".
    - "name" Entity nyní je definován jako "Frank" v paměti modelu, takže je lze vybrat jako akci "Hello $name" akce.
7. Vyberte odpověď "Hi $name!"
8. Klikněte na tlačítko "Save".

Přidání další železniční alternativní vstupy modelu.

1. V poli "Přidat alternativní vstup..." typ "Jsem Jose."
    - Model nerozpozná název jako Entity, takže vybere celý blok textu jako hodnotu subjektu
2. Klikněte na frázi "Jsem Jose" a klikněte na ikonu odpadkového koše.
3. Klikněte na "Jose" a pak klikněte na "name" v seznamu entit.
4. Klikněte na výsledek akce.
5. Vyberte odpověď na "Frank Dobrý den!"
6. Klikněte na tlačítko "Save".

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Negatable entity](./06-negatable-entities.md)

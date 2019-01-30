---
title: Jak používat entity detekce zpětného volání s modelem konverzace Learner – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Další informace o použití zjišťování entit zpětného volání s modelem Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 576dc6bd44360f73c4133907233e59e5f51837b1
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55212525"
---
# <a name="how-to-use-entity-detection-callback"></a>Jak používat entity detekce zpětného volání

Tento kurz ukazuje zpětného volání zjišťování entit a znázorňuje běžný vzor pro vyřešení entity.

## <a name="video"></a>Video

[![Entita detekce zpětného volání kurz ve verzi Preview](https://aka.ms/cl_Tutorial_v3_EntityDetection_Preview)](https://aka.ms/cl_Tutorial_v3_EntityDetection)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby `tutorialEntityDetectionCallback` bot běží.

    npm run tutorial-entity-detection

## <a name="details"></a>Podrobnosti
Zpětná volání zjišťování entit povolit úpravu chování rozpoznávání entit a entit manipulace prostřednictvím kódu. Tato funkce předvedeme procházením prostřednictvím návrhový vzor typické entity detekce zpětného volání. Například řešení "velký objem apple" k "new york".

## <a name="steps"></a>Kroky

### <a name="create-the-model"></a>Vytvoření modelu

1. Ve webové uživatelské rozhraní klikněte na tlačítko "Nový Model."
2. V poli "Name" typ "EntityDetectionCallback" a stiskněte enter.
3. Klikněte na tlačítko "Vytvořit".

V tomto příkladu jsou potřeba tři entity, Pojďme vytvořit tři.

### <a name="create-the-custom-trained-entity"></a>Vytvoření vlastní Entity Trénovaného

1. Na levém panelu klikněte na tlačítko "," subjekty a potom na tlačítko "Nová entita".
2. Vyberte "Vlastní školení" u "Typu Entity."
3. Typ "Město" u "Názvu Entity."
4. Klikněte na tlačítko "Vytvořit".

### <a name="create-the-first-programmatic-entity"></a>Vytvoření první Entity prostřednictvím kódu programu

1. Klikněte na tlačítko "Nová entita".
2. Vyberte "Programový" u "Typu Entity."
3. Typ "CityUnknown", "Název Entity."
4. Klikněte na tlačítko "Vytvořit".

### <a name="create-the-first-programmatic-entity"></a>Vytvoření první Entity prostřednictvím kódu programu

1. Klikněte na tlačítko "Nová entita".
2. Vyberte "Programový" u "Typu Entity."
3. Typ "CityResolved", "Název Entity."
4. Klikněte na tlačítko "Vytvořit".

Teď vytvořte tři akce.

### <a name="action-creation"></a>Vytvoření akce

1. Na levém panelu klikněte na tlačítko "Akce" a potom na tlačítko "Nová akce".
2. "Bodu robotů také odpovědi na..." pole, zadejte "Město, který chcete?"
3. Do pole "Očekávané Entity v odpovědi uživatele..." zadejte "City".
4. V poli "Vyřazení entity" typ "City".
5. Klikněte na tlačítko "Vytvořit".
6. Klikněte na tlačítko "Nová akce".
7. "Bodu robotů také odpovědi na..." pole, zadejte "Město, který chcete?"
8. Do pole "Očekávané Entity v odpovědi uživatele..." zadejte "Nevím tento města."
9. Klikněte na tlačítko "Vytvořit".
10. Klikněte na tlačítko "Nová akce".
11. "Bodu robotů také odpovědi na..." pole, zadejte "$CityResolved je opravdu dobré."
12. Klikněte na tlačítko "Vytvořit".

Tady je kód zpětného volání:

![](../media/tutorial10_callbackcode.PNG)

### <a name="train-the-model"></a>Trénování modelu

1. Na levém panelu klikněte na tlačítko "Dialogů Train" a potom na tlačítko "Dialogové okno Nový Train".
2. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "hi"
3. Klikněte na tlačítko "Skóre akce".
4. Vyberte odpověď na "Město, který chcete?"
5. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "velký objem apple"
6. Klikněte na tlačítko "Skóre akce".
    - Kliknutím na tlačítko aktivuje zpětného volání zjišťování entit
    - Zpětné volání kód nastaví hodnota CityResolved Entity správně na "new york"
7. Vyberte odpověď na "new york je opravdu dobré."

Tento model je typický pro mnoho scénářů robota. Uživatel projevy a extrahované entity se poskytne obchodní logiky a tuto logiku transformace utterance do kanonického tvaru, který se pak uloží do programové entity, pro následné zapne dialogového okna.

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zpětná volání relace](./13-session-callbacks.md)

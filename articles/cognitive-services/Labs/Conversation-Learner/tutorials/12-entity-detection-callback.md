---
title: Použití zpětného volání pro detekci entit s modelem Conversation Learner Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Naučte se používat zpětné volání detekce entit pomocí Conversation Learnerho modelu.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 3f6094da7b103f1c99e9bb3fa9313354fcb2a8bc
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703992"
---
# <a name="how-to-use-entity-detection-callback"></a>Použití zpětného volání pro detekci entit

V tomto kurzu se zobrazuje zpětné volání detekce entit a znázorňuje společný vzor pro řešení entit.

## <a name="video"></a>Video

[![Kurz zpětného volání detekce entit ve verzi Preview](https://aka.ms/cl_Tutorial_v3_EntityDetection_Preview)](https://aka.ms/cl_Tutorial_v3_EntityDetection)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby `tutorialEntityDetectionCallback` robot běžel.

    npm run tutorial-entity-detection

## <a name="details"></a>Podrobnosti
Zpětná volání detekce entit umožňují úpravu chování při rozpoznávání entit a manipulaci s entitami prostřednictvím kódu. Tuto funkci provedeme provedením vzoru návrhu zpětného volání pro rozpoznávání entit. Například řešení "Big Apple" na "New York".

## <a name="steps"></a>Kroky

### <a name="create-the-model"></a>Vytvoření modelu

1. Ve webovém uživatelském rozhraní klikněte na nový model.
2. Do pole název zadejte "EntityDetectionCallback" a stiskněte klávesu ENTER.
3. Klikněte na tlačítko vytvořit.

V tomto příkladu jsou potřeba tři entity, takže vytvoříme tři.

### <a name="create-the-custom-trained-entity"></a>Vytvořit vlastní školenou entitu

1. Na levém panelu klikněte na entity a pak na tlačítko nová entita.
2. Pro typ entity vyberte vlastní vzdělání.
3. Jako "název entity" zadejte "City".
4. Klikněte na tlačítko vytvořit.

### <a name="create-the-first-programmatic-entity"></a>Vytvořit první programovou entitu

1. Klikněte na tlačítko nová entita.
2. Jako typ entity vyberte program.
3. Zadejte "CityUnknown" pro "název entity".
4. Klikněte na tlačítko vytvořit.

### <a name="create-the-first-programmatic-entity"></a>Vytvořit první programovou entitu

1. Klikněte na tlačítko nová entita.
2. Jako typ entity vyberte program.
3. Zadejte "CityResolved" pro "název entity".
4. Klikněte na tlačítko vytvořit.

Nyní vytvořte tři akce.

### <a name="action-creation"></a>Vytvoření akce

1. Na levém panelu klikněte na akce a pak na tlačítko Nová akce.
2. V "reakci na robota..." Zadejte "které město chcete?"
3. V části očekávaná entita v odpovědi uživatele... Zadejte "City".
4. Do pole "vyřazení entit" zadejte "City".
5. Klikněte na tlačítko vytvořit.
6. Klikněte na tlačítko Nová akce.
7. V "reakci na robota..." Zadejte "které město chcete?"
8. V části očekávaná entita v odpovědi uživatele... Zadejte text "neznáte toto město."
9. Klikněte na tlačítko vytvořit.
10. Klikněte na tlačítko Nová akce.
11. V "reakci na robota..." Zadejte text "$CityResolved je velmi dobrý."
12. Klikněte na tlačítko vytvořit.

Tady je kód zpětného volání:

![](../media/tutorial10_callbackcode.PNG)

### <a name="train-the-model"></a>Výuka modelu

1. Na levém panelu klikněte na možnost výuka dialogových oken a pak klikněte na tlačítko "nový výukový dialog".
2. Na panelu chat, kde říká "zadejte zprávu..." zadejte text "Dobrý den".
3. Klikněte na tlačítko "akce skóre".
4. Vyberte odpověď, "kterou chcete City?"
5. Na panelu chat, kde říká "zadejte zprávu..." zadejte text "Big Apple".
6. Klikněte na tlačítko "akce skóre".
    - Kliknutí na tlačítko aktivuje zpětné volání detekce entit.
    - Kód zpětného volání nastaví hodnotu entity CityResolved správně na "New York".
7. Vyberte odpověď. "Praha je velmi dobrá."

Tento model je typický pro mnoho scénářů robotů. Uživatelské projevy a extrahované entity jsou dodány do obchodní logiky a tato logika transformuje utterance na kanonický tvar, který se pak uloží do programových entit pro následné spuštění dialogového okna.

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zpětná volání relace](./13-session-callbacks.md)

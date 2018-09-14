---
title: Jak používat čekání a bez čekání akce s modelem konverzace Learner – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Další informace o použití čekání a bez čekání akce s modelem Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f349dab23b9156d3a5656e8275533ebe6a82cdf9
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2018
ms.locfileid: "45540896"
---
# <a name="wait-and-non-wait-actions"></a>Počkejte a bez čekání akce

Tento kurz ukazuje rozdíl mezi čekání a bez čekání akcí v Learner konverzace.

## <a name="video"></a>Video

[![Tutoriál 2 ve verzi Preview](http://aka.ms/cl-tutorial-02-preview)](http://aka.ms/blis-tutorial-02)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna obecné kurz bot

    npm run tutorial-general

## <a name="details"></a>Podrobnosti

- Počkejte, akce: poté, co systém provede akci "Čekání", to zastaví provádění akcí a počkat na vstup uživatele.
- Akce non-wait: poté, co systém provede akci "bez čekání", bude okamžitě použit jiná akce (bez čekání na vstup nejprve uživatele).

## <a name="steps"></a>Kroky

### <a name="create-a-new-model"></a>Vytvořit nový model

1. V webového uživatelského rozhraní klikněte na nový Model
2. Do pole Název zadejte WaitNonWait. Pak klikněte na tlačítko vytvořit.

### <a name="create-the-first-wait-action"></a>Vytvoření první akce Wait

1. Klikněte na tlačítko akce a potom novou akci.
2. V odpovědi zadejte "které zvířat požadujete?".
    - Toto je akce Wait, takže políčko nechte čekání zaškrtnuté políčko odpovědi.
3. Klikněte na Vytvořit.

### <a name="create-a-non-wait-action"></a>Vytvoření akce Non-Wait

1. Klikněte na tlačítko Nová akce
2. V odpovědi zadejte "Krav Řekněme, že mú".
3. Zrušte zaškrtnutí políčka čekat na odpověď zaškrtávací políčko.
4. Kliknutí na Vytvořit

### <a name="create-a-second-non-wait-action"></a>Vytvoří druhou akci Non-Wait

1. Klikněte na tlačítko Nová akce
2. V odpovědi zadejte "Kachnami Řekněme, že quack".
3. Zrušte zaškrtnutí políčka čekat na odpověď zaškrtávací políčko.
4. Kliknutí na Vytvořit

![](../media/tutorial2_actions.PNG)

### <a name="train-the-bot"></a>Natrénování robota

1. Klikněte na dialogová okna trénování, pak nové dialogové okno trénování.
2. Typ "hello"
3. Klikněte na tlačítko akce skóre a vyberte "které zvířat požadujete?".
4. Zadejte "mléka.
5. Klikněte na tlačítko akce skóre a vyberte "Krav Řekněme, že mú".
    - Robot nebude počkat na vstup a další kroky.
2. Vyberte "které zvířat požadujete?".
3. Zadejte "duck.
5. Klikněte na tlačítko akce skóre a vyberte "Kachnami Řekněme, že quack".

![](../media/tutorial2_dialogs.PNG)

> [!NOTE]
> Posloupnost odpovědi robotů s ohledem na čekání a bez čekání akce.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Úvod do entit](./3-introduction-to-entities.md)

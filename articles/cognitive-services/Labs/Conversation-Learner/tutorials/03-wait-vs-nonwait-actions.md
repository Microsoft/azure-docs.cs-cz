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
ms.openlocfilehash: 95cf223004c50583701e65d3adf02f57bd541fae
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796723"
---
# <a name="wait-and-non-wait-actions"></a>Počkejte a bez čekání akce

Tento kurz ukazuje rozdíl mezi čekání a bez čekání akcí v Learner konverzace.

## <a name="video"></a>Video

[![Počkejte vs Non-Wait kurzu ve verzi Preview](https://aka.ms/cl_Tutorial_v3_WaitnonWait_Preview)](https://aka.ms/cl_Tutorial_v3_WaitnonWait)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna obecné kurz bot

    npm run tutorial-general

## <a name="details"></a>Podrobnosti

- Počkejte, akce: Poté, co systém provede akci "Čekání", bude zastavit provádění akcí a počkat na vstup uživatele.
- Non-wait akce: Poté, co systém provede akci "bez čekání", bude okamžitě použit jinou akci (bez čekání na vstup uživatele).

## <a name="steps"></a>Kroky

### <a name="create-a-new-model"></a>Vytvořit nový model

1. V webového uživatelského rozhraní klikněte na nový Model
2. V poli "Name" typ "Počkejte Non-Wait", stiskněte klávesu enter nebo klikněte na tlačítko "Vytvořit".

### <a name="create-the-first-two-wait-actions"></a>Vytvoření první dva počkejte akcí

1. Na levém panelu klikněte na tlačítko "Akce" a potom na tlačítko "Nová akce".
2. "Bodu robotů také odpovědi na..." pole, zadejte "jaké pizza způsobem?".
    - Toto je akce Wait, takže políčko nechte zaškrtnuté políčko "Čekat pro odpověď".
3. Klikněte na tlačítko "Vytvořit".
4. Opakováním těchto kroků, vytvořte další akce s "Pizza se připravují!" jako odpověď robota.

### <a name="train-using-those-wait-actions"></a>Pomocí těchto akcí počkejte trénování

1. Na levém panelu klikněte na tlačítko "Dialogů Train" a potom na tlačítko "Dialogové okno Nový Train".
2. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "Hi". 
    - To simuluje uživatele na straně konverzace.
3. Klikněte na tlačítko "Skóre akce".
4. Vyberte odpověď na "jaké pizza přejete?".
5. Jako uživatel, odpoví, "Margherita".
6. Klikněte na tlačítko "Skóre akce".
7. Vyberte odpověď "Pizza se připravují!".
8. Klikněte na tlačítko "Save".

### <a name="create-a-non-wait-action-while-training"></a>Vytvořit bez čekání akci při školení
I když můžete vytvořit bez čekání akce stejně jako dříve, můžete také vytvořit z v rámci celodenního školení.
1. Klikněte na tlačítko "Dialogové okno Nový Train".
2. Jako typ uživatele v "Hello".
3. Klikněte na tlačítko "Skóre akce".
4. Klikněte na "+ akce" tlačítko. 
    - Tím přejdete do dialogového okna známých "Vytvoření Action".
5. Zadejte odpověď bodu robotů také jako "Vítejte Pizza Bot!"
6. Zrušte zaškrtnutí políčka zaškrtávací políčko "Čekat pro odpověď".
7. Klikněte na tlačítko "Vytvořit".
    - Všimněte si, že odpoví Bot okamžitě s "Vítejte Pizza Bot!" a že se znovu zobrazí výzva k zadání jiného odpovědi robotů. Je to proto bodu robotů také odpověď byla akce Non-Wait jsme právě vytvořili.
9. Vyberte odpověď na "jaké pizza přejete?".
10. Jako uživatel, odpoví, "Margherita".
11. Klikněte na tlačítko "Skóre akce".
12. Vyberte odpověď "Pizza se připravují!".
13. Klikněte na tlačítko "Save".

> [!NOTE]
> Posloupnost odpovědi robotů s ohledem na čekání a bez čekání akce.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Úvod do entit](./04-introduction-to-entities.md)

---
title: Použití alternativních vstupů s Conversation Learner-Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Naučte se používat alternativní vstupy s Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 7416939db8d6552f79fba700b8432de2ad228846
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704202"
---
# <a name="how-to-use-alternative-inputs"></a>Jak používat alternativní vstupy

V tomto kurzu se dozvíte, jak používat pole alternativní vstupy pro uživatele projevy v rozhraní učebny.

## <a name="video"></a>Video

[![Kurz alternativních vstupů ve verzi Preview](https://aka.ms/cl_Tutorial_v3_AlternativeInputs_Preview)](https://aka.ms/cl_Tutorial_v3_AlternativeInputs)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby byl spuštěný robot pro obecné kurzy.

    npm run tutorial-general

## <a name="details"></a>Podrobnosti
Alternativní vstupy jsou alternativní, sémanticky rovnocenný uživatel projevy, které uživatel mohl mít v rámci školicího dialogového okna. Tyto alternativní vstupy vám umožňují kompaktnější určení variací projevy bez nutnosti řešit jednotlivé variace v samostatných školicích dialogových oknech.

## <a name="steps"></a>Kroky

### <a name="create-the-model"></a>Vytvoření modelu

1. Ve webovém uživatelském rozhraní klikněte na nový model.
2. Do pole název zadejte "AlternativeInputs" a stiskněte klávesu ENTER.
3. Klikněte na tlačítko vytvořit.

### <a name="entity-creation"></a>Vytvoření entity

1. Na levém panelu klikněte na entity a pak na tlačítko nová entita.
2. Pro typ entity vyberte vlastní vzdělání.
3. Jako "název entity" zadejte "City".
4. Klikněte na tlačítko vytvořit.

![](../media/T10_actions.png)

Nyní vytvoříme tři akce.

### <a name="create-the-first-action"></a>Vytvoření první akce

1. Na levém panelu klikněte na akce a pak na tlačítko Nová akce.
2. V "reakci na robota..." Zadejte "které město?"
3. V části očekávaná entita v odpovědi uživatele... Zadejte "City".
4. Do pole "vyřazení opravňujících" zadejte "City".
5. Klikněte na tlačítko vytvořit.

![](../media/T10_action_create_1.png)

### <a name="create-the-second-action"></a>Vytvoření druhé akce

1. Na levém panelu klikněte na akce a pak na tlačítko Nová akce.
2. V "reakci na robota..." Zadejte text "počasí v $city je pravděpodobně Slunečné."
3. Klikněte na tlačítko vytvořit.

![](../media/T10_action_create_2.png)

### <a name="create-the-third-action"></a>Vytvořit třetí akci

1. Na levém panelu klikněte na akce a pak na tlačítko Nová akce.
2. V "reakci na robota..." Zadejte text "zkusit dotaz na počasí".
3. Do pole "vyřazení opravňujících" zadejte "City".
4. Klikněte na tlačítko vytvořit.

![](../media/T10_action_create_3.png)

Nyní máte tři akce.

![](../media/T10_actions.png)

### <a name="train-the-model"></a>Výuka modelu

1. Na levém panelu klikněte na možnost výuka dialogových oken a pak klikněte na tlačítko "nový výukový dialog".
2. Na panelu chat, kde říká "napsat zprávu..." zadejte "Co je počasí?"
3. Klikněte na tlačítko "akce skóre".
4. Vyberte odpověď "město?".
5. Na panelu chat, kde říká "zadejte zprávu..." zadejte "Denver".
6. Klikněte na tlačítko "akce skóre".
7. Vyberte odpověď "počasí v Denver je pravděpodobně Slunečné."
8. Klikněte na tlačítko odeslat změny.

![](../media/T10_training_1.png)

Vydejte si model ještě více vytvořením dalšího výukového dialogového okna.

### <a name="second-model-train-dialog"></a>Druhý dialog s výukou modelu

1. Na levém panelu klikněte na možnost výuka dialogových oken a pak klikněte na tlačítko "nový výukový dialog".
2. Na panelu chat, kde text "zadejte zprávu..." zadejte text "co můžete udělat?"
3. Klikněte na tlačítko "akce skóre".
4. Vyberte odpověď "zkusit dotaz na počasí".
5. Na panelu chat, kde říká "napsat zprávu..." zadejte text "Co je počasí v Seattlu?"
6. Klikněte na "Seattle" a potom v seznamu entit klikněte na "City".
7. Klikněte na tlačítko "akce skóre".
8. Vyberte odpověď "počasí v Seattlu je pravděpodobně Slunečné."
9. Klikněte na tlačítko odeslat změny.

![](../media/T10_training_2.png)

### <a name="third-model-train-dialog-using-alternative-input"></a>Dialogové okno výuka třetího modelu s použitím alternativního vstupu

1. Na levém panelu klikněte na možnost výuka dialogových oken a pak klikněte na tlačítko "nový výukový dialog".
2. Na panelu chat, kde říká "zadejte zprávu..." zadejte text "Help".
3. Klikněte na tlačítko "akce skóre".
    - Model je nejistý pro nejlepší možnost, takže by ve výchozím nastavení zvolil nejvyšší percentil.
4. Klikněte na tlačítko opustit výuku a pak na tlačítko Potvrdit.

![](../media/T10_training_3.png)

Pojďme tento systém lépe ladit pomocí alternativních vstupů. Můžete přidat alternativní vstup při výuce nebo později.

1. Na levém panelu klikněte na možnost výuka dialogových oken a pak vyberte "co můžete udělat?" ze seznamu výukových dialogových oken.
1. Klikněte na "co můžete udělat?" utterance na panelu chatu.
1. V části Přidat alternativní vstup... Zadejte text "Help" a stiskněte klávesu ENTER.
1. Klikněte na tlačítko Uložit změny.

![](../media/T10_training_4.png)

Pojďme přidat další alternativní vstup pro zpracování Houstonu.

1. Klikněte na "Co je počasí v Seattlu?" utterance na panelu chatu.
1. V části Přidat alternativní vstup... Zadejte do pole "FORECAST pro Houstonu" a stiskněte ENTER.
   - Chybová zpráva zdůrazňuje, že alternativní vstupy faktů musí být sémanticky ekvivalentní a obsahovat stejné entity jako původní utterance; Nejedná se pouze o stejné hodnoty entit. Je nutné, aby byly přítomny stejné entity.
1. Klikněte na "Houstonu" a v seznamu entity vyberte City (město).
1. V části Přidat alternativní vstup... Zadejte do pole "FORECAST pro Seattle" a stiskněte klávesu ENTER.
1. Klikněte na "Seattle" a v seznamu entity vyberte City (město).
1. Klikněte na tlačítko Uložit změny.
1. Klikněte na tlačítko Uložit úpravy.

![](../media/T10_training_5.png)

### <a name="testing-the-model"></a>Testování modelu

1. Na levém panelu klikněte na možnost protokolovat dialogy a pak na nový dialog protokolu.
2. Na panelu chat, kde text "zadejte zprávu..." zadejte text "Help mi".
3. Na panelu chat, kde říká "zadejte zprávu..." zadejte "FORECAST for Denver".

![](../media/T10_logdialog.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Dialogy protokolu](./11-log-dialogs.md)

---
title: Použití akcí Wait a non-Wait s Conversation Learnerm modelem Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Naučte se používat akce Wait a non-Wait s Conversation Learnerm modelem.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 5d9a67f0b413b624baec369a219547cf18e9dbd2
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705606"
---
# <a name="wait-and-non-wait-actions"></a>Čekací a nečekací akce

V tomto kurzu se zobrazuje rozdíl mezi akcemi čekání a akcemi, které nečekají v Conversation Learner.

## <a name="video"></a>Video

[![Počkat v kurzu a bez čekání na verzi Preview](https://aka.ms/cl_Tutorial_v3_WaitnonWait_Preview)](https://aka.ms/cl_Tutorial_v3_WaitnonWait)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby byl spuštěný robot pro obecné kurzy.

    npm run tutorial-general

## <a name="details"></a>Podrobnosti

- Akce čekání: Poté, co systém přijme akci "čekání", přestane přebírat akce a čekat na vstup uživatele.
- Akce bez čekání: Poté, co systém provede akci typu "nečekat", okamžitě vybere jinou akci (bez čekání na vstup uživatele).

## <a name="steps"></a>Kroky

### <a name="create-a-new-model"></a>Vytvořit nový model

1. Ve webovém uživatelském rozhraní klikněte na nový model.
2. Do pole název zadejte "Wait non-Wait", stiskněte klávesu ENTER nebo klikněte na tlačítko vytvořit.

### <a name="create-the-first-two-wait-actions"></a>Vytvoření prvních dvou akcí čekání

1. Na levém panelu klikněte na akce a pak na tlačítko Nová akce.
2. V "reakci na robota..." Zadejte "co Pizza chcete?".
    - Jedná se o akci čekání, takže nechte zaškrtnuté políčko čekat na odpověď.
3. Klikněte na tlačítko vytvořit.
4. Opakováním těchto kroků vytvořte další akci s "Pizzaem" na cestě. jako odpověď robota.

### <a name="train-using-those-wait-actions"></a>Výuka pomocí těchto čekacích akcí

1. Na levém panelu klikněte na možnost výuka dialogových oken a pak klikněte na tlačítko "nový výukový dialog".
2. Na panelu chat, kde říká "zadejte zprávu..." zadejte text "Dobrý den". 
    - Tím se simuluje strana uživatele v konverzaci.
3. Klikněte na tlačítko "akce skóre".
4. Vyberte odpověď "jakou Pizza chcete?".
5. Jako uživatel odpoví na "Margherita".
6. Klikněte na tlačítko "akce skóre".
7. Vyberte odpověď Pizza způsobem.
8. Klikněte na tlačítko Uložit.

### <a name="create-a-non-wait-action-while-training"></a>Vytvoření akce bez čekání během školení
I když byste mohli vytvořit akci, kterou nečekají, jako byste to provedli dříve, můžete ji vytvořit také v rámci školicích cvičení.
1. Klikněte na tlačítko nového výukového dialogového okna.
2. Jako typ uživatele v zadejte "Hello".
3. Klikněte na tlačítko "akce skóre".
4. Klikněte na tlačítko + akce. 
    - Tím přejdete do známého dialogového okna vytvořit akci.
5. Zadejte v reakci robota jako "Vítejte v Pizza bot!".
6. Zrušte kontrolu v poli Čekání na odpověď.
7. Klikněte na tlačítko vytvořit.
    - Všimněte si, že robot odpoví hned a "Vítá vás Pizza robot!". a že se znovu zobrazí výzva k zadání jiné odpovědi robota. Důvodem je to, že reakce robota byla právě vytvořená akce bez čekání.
9. Vyberte odpověď "jakou Pizza chcete?".
10. Jako uživatel odpoví na "Margherita".
11. Klikněte na tlačítko "akce skóre".
12. Vyberte odpověď Pizza způsobem.
13. Klikněte na tlačítko Uložit.

> [!NOTE]
> Sekvence odpovědí robota s ohledem na čekací a nečekací akce.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Seznámení s entitami](./04-introduction-to-entities.md)

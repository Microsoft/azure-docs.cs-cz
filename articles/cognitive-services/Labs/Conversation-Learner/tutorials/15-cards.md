---
title: Jak používat karty s modelem Conversation Learner, část 1 – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Naučte se používat karty s modelem Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 426d7c8de29abeb88833e94962a7291a641702ac
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703751"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Jak používat karty (část 1 ze 2)

V tomto kurzu se dozvíte, jak do robota přidat a použít jednoduchou kartu.

> [!NOTE]
> Conversation Learner v současné době očekává, že vaše soubory definice karet budou umístěny v adresáři s názvem "karty", který je přítomen v adresáři, kde je spuštěn robot.

## <a name="video"></a>Video

[![Kurz karet Preview](https://aka.ms/cl_Tutorial_v3_Cards_Preview)](https://aka.ms/cl_Tutorial_v3_Cards)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby byl spuštěný robot pro obecné kurzy.

    npm run tutorial-general

## <a name="details"></a>Podrobnosti

Karty jsou prvky uživatelského rozhraní, které umožňují uživateli vybrat možnost v konverzaci. 

### <a name="open-the-demo"></a>Otevřete ukázku.

Ve webovém uživatelském rozhraní klikněte na "Import kurzů" a vyberte model s názvem "kurz – 15-Card".

### <a name="the-card"></a>Karta

Definice karty je v následujícím umístění: C:\<installedpath\>\src\cards\prompt.json.

Systém očekává, že v tomto adresáři "karty" najdete definice vašich karet.

![](../media/tutorial13_prompt.PNG)

> [!NOTE]
> Všimněte si, že typ textu "TextBlock" a zástupný symbol {{Question}} v textovém poli.
> K dispozici jsou dvě tlačítka Odeslat a text, který se pro každou z nich odešle.

### <a name="actions"></a>Akce

Vytvořili jsme tři akce. Jak vidíte níže, první akce je karta.

![](../media/tutorial13_actions.PNG)

Pojďme se podívat, jak se vytvořil typ akce karty:

![](../media/tutorial13_cardaction.PNG)

> [!NOTE]
> Karta obsahuje tři různé parametry – vstup dotazů, tlačítko 1 a tlačítko 2. Tyto prvky jsou odkazy na šablony na kartě, kde zadáte otázku a příslušné odpovědi. Můžete také odkazovat a používat entity nebo kombinaci textu a entit.

Ikona oka vám ukáže, jak karta vypadá.

### <a name="practicing-creating-card-actions"></a>Praktické akce při vytváření karet

1. Na levém panelu klikněte na akce a pak na tlačítko Nová akce.
2. Jako typ akce vyberte karta.
3. V seznamu Šablona vyberte Dotázat se.
4. Do pole "Question" zadejte "Přejít doleva nebo doprava"
5. Do pole Button1 zadejte Left.
6. Do pole Button2 zadejte Right.
7. Klikněte na tlačítko Storno.

### <a name="train-dialog-using-an-adaptive-card"></a>Dialog výuka s použitím adaptivní karty

1. Na levém panelu klikněte na možnost výuka dialogových oken a pak klikněte na tlačítko "nový výukový dialog".
2. Na panelu chat, kde říká "zadejte zprávu..." zadejte text "Dobrý den".
3. Klikněte na tlačítko "akce skóre".
4. Vyberte odpověď, dotaz: otázka: Chcete přejít doleva nebo doprava? "
    - Ikona oka se dá použít k zobrazení náhledu karty.
5. Na panelu chat klikněte na tlačítko vlevo na vygenerovaném příkazovém řádku.
6. Klikněte na tlačítko "akce skóre".
7. Vyberte odpověď "vlevo".
8. Klikněte na tlačítko Uložit.
9. Vyberte odpověď, dotaz: otázka: Chcete přejít doleva nebo doprava? "
10. Na panelu chat klikněte na tlačítko vpravo na vykreslené příkazovém řádku.
11. Klikněte na tlačítko "akce skóre".
12. Vybrat odpověď, "vpravo"

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Hybridní roboty](./16-hybrid-bots.md)

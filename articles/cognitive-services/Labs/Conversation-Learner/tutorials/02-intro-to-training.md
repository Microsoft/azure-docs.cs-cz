---
title: Úvod do školení modelu Conversation Learner – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Naučte se naučit model, včetně větvení a úprav předchozích školení prostřednictvím Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: c657025ce588363cf76ce10868d809a9aff69222
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705616"
---
# <a name="introduction-to-training"></a>Úvod do školení

V tomto kurzu se seznámíte se základy školení modelu, rozvětvení nového školení založeného na předchozím školení a úpravou odpovědi robota, abyste ji mohli změnit.

## <a name="video"></a>Video

[![Úvod do kurzu školení – Preview](https://aka.ms/cl_Tutorial_v3_IntroTraining_Preview)](https://aka.ms/cl_Tutorial_v3_IntroTraining)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby byl spuštěný robot pro obecné kurzy.

    npm run tutorial-general

## <a name="details"></a>Podrobnosti

- Akce: Odpověď robotu na vstup uživatele
- Průřez Způsob, jakým se robot učí reagovat na vstup uživatele.
- Větvení Úprava vstupu uživatele v rámci uloženého výukového dialogového okna pro účely vytvoření nového výukového dialogu, který začíná stejně jako původní, ale přebírá konverzaci v jiném směru.

## <a name="steps"></a>Kroky

### <a name="create-a-new-model"></a>Vytvořit nový model

1. Ve webovém uživatelském rozhraní klikněte na nový model.
2. Jako "název" zadejte "inspirovat bot". Pak klikněte na vytvořit.

### <a name="create-an-action"></a>Vytvořit akci

1. Na levém panelu klikněte na akce a pak na tlačítko Nová akce.
2. V poli "odpověď robota" zadejte "Dobrý den! Přejete si se nechte inspirovat ještě dnes? ".
    - Ponechte všechna ostatní pole a zaškrtávací políčka na jejich výchozím nastavení.
3. Klikněte na Vytvořit.

### <a name="first-training-and-creating-another-action-while-training"></a>První školení a vytvoření další akce během školení

1. Na levém panelu klikněte na možnost výuka dialogových oken a pak klikněte na tlačítko "nový výukový dialog".
2. Na panelu chat, kde říká "zadejte zprávu..." zadejte text "Hello". 
    - Tím se simuluje strana uživatele v konverzaci.
3. Klikněte na možnost Akce skóre.
4. Vyberte odpověď "Dobrý den! Přejete si se nechte inspirovat ještě dnes? ".
5. Jako uživatel odpoví na Ano.
6. Klikněte na možnost Akce skóre.
7. Klikněte na tlačítko + akce. 
    - Tím přejdete do známého dialogového okna vytvořit akci.
8. Napište nám odpověď robota jako "super!".
9. Klikněte na Vytvořit.
10. Všimněte si, že robot reaguje okamžitě.
11. Klikněte na tlačítko Uložit.

### <a name="branch-a-second-training-off-of-the-first-training"></a>Vyskočit druhé školení od prvního školení
1. Klikněte na řádek mřížky, který shrnuje první školení. 
    - To vám umožní zobrazit a upravit stávající školení.
2. Klikněte na odpověď uživatele "Ano". 
    - Tím se zpřístupní ovládací prvky pro úpravy.
3. Klikněte na ikonu větve. 
    - Tím se zobrazí výzva k zadání jiného vstupu uživatele pro novou konverzaci.
4. Zadejte text "ne", stiskněte klávesu ENTER nebo klikněte na tlačítko vytvořit. 
    - V tomto okamžiku budete mít novou instanci dialogu s výukou, takže původní zůstane beze změny.
5. Klikněte na možnost Akce skóre.
6. Klikněte na nesprávnou odpověď robota, která se právě zobrazila.
7. Klikněte na tlačítko + akce. 
    - abychom mohli vytvořit novou akci, kterou má robot reagovat.
8. Zadejte odpověď robota jako "žádný problém!". Máte skvělý den! "
9. Kliknutí na Vytvořit
10. Všimněte si, že robot reaguje okamžitě.
11. Klikněte na tlačítko Uložit.

### <a name="test-the-trainings"></a>Testování školení
1. Na levém panelu klikněte na možnost protokolovat dialogy a pak na nový dialog protokolu.
2. Zadejte zprávu "Dobrý den". 
3. Všimněte si, že robot reaguje automaticky podle způsobu, jakým ho vyškoleme.
4. Zadejte odpověď uživatele "Ano".
5. Všimněte si, že odpověď robota ukazuje, že první školení funguje.
6. Klikněte na tlačítko časový limit relace. Díky tomu Conversation Learner chceme začít znovu, ale konverzace se bude ignorovat.
7. Zadejte zprávu "Dobrý den". 
8. Všimněte si, že robot reaguje automaticky podle způsobu, jakým ho vyškoleme.
9. Zadejte odpověď uživatele "ne".
10. Všimněte si, že odpověď robota ukazuje, že druhé školení funguje.
11. Klikněte na tlačítko dokončené testování.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Čekací a nečekací akce](./03-wait-vs-nonwait-actions.md)

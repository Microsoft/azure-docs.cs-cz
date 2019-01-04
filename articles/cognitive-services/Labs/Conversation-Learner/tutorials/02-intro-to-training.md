---
title: Úvod k trénování modelu konverzace Learner – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Zjistěte, jak pro trénování modelu, včetně větvení a úpravy předchozí školení prostřednictvím Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e7405583a04699ce0481c95b22912e717f76743a
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796722"
---
# <a name="introduction-to-training"></a>Úvod do školení

Tento kurz ukazuje základy trénování modelu, větvení vypnout nový školení na základě předchozích trénování a úpravy odpovědi robotů aby bylo možné ho změnit.

## <a name="video"></a>Video

[![Úvod do školení kurz ve verzi Preview](https://aka.ms/cl_Tutorial_v3_IntroTraining_Preview)](https://aka.ms/cl_Tutorial_v3_IntroTraining)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna obecné kurz bot

    npm run tutorial-general

## <a name="details"></a>Podrobnosti

- Akce: Bot reakci na vstup uživatele.
- Regrese: Způsob, jakým jsme naučit robota reagovat na vstup uživatele.
- Větvení: Úpravy vstupu uživatele uložených vlaku dialogu za účelem vytvoření nové dialogové okno trénování, která je na začátku stejný jako původní, ale trvá konverzace v různých směru.

## <a name="steps"></a>Kroky

### <a name="create-a-new-model"></a>Vytvořit nový model

1. V webového uživatelského rozhraní klikněte na nový Model
2. "Name" zadejte "Inspirovat Bot". Pak klikněte na tlačítko vytvořit.

### <a name="create-an-action"></a>Vytvoření akce

1. Na levém panelu klikněte na tlačítko "Akce" a potom na tlačítko "Nová akce".
2. V odpovědi bodu robotů také"" zadejte "Dobrý den! Chtěli byste ještě dnes se inspirovat? ".
    - Všechna pole a zaškrtávacích políček ponechte jejich výchozí nastavení.
3. Klikněte na Vytvořit.

### <a name="first-training-and-creating-another-action-while-training"></a>Nejprve školení a vytvořit další akci při školení

1. Na levém panelu klikněte na tlačítko "Dialogů Train" a potom na tlačítko "Dialogové okno Nový Train".
2. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "hello". 
    - To simuluje uživatele na straně konverzace.
3. Klikněte na tlačítko "Skóre akce".
4. Vyberte odpověď "Dobrý den! Chtěli byste ještě dnes se inspirovat? ".
5. Jako uživatel odpoví, "Ano".
6. Klikněte na tlačítko "Skóre akce".
7. Klikněte na "+ akce" tlačítko. 
    - Tím přejdete do dialogového okna známých "Vytvoření Action".
8. Zadejte odpověď bodu robotů také jako "Už Super!"
9. Klikněte na Vytvořit.
10. Všimněte si, že okamžitě odpovídá robota.
11. Klikněte na tlačítko "Save".

### <a name="branch-a-second-training-off-of-the-first-training"></a>Větev druhý školení z první školení
1. Klikněte na řádek mřížky, která shrnuje první školení. 
    - To umožňuje zobrazit a upravit existující školení.
2. Klikněte na odpověď uživatele "Ano". 
    - Tím se zpřístupňují ovládací prvky pro úpravy.
3. Klikněte na ikonu větve. 
    - Otevře se řádek pro jiný uživatelský vstup pro novou konverzaci.
4. Typ v "Ne", stiskněte klávesu zadejte nebo klikněte na tlačítko "Vytvořit". 
    - V tomto okamžiku budete mít novou instanci třídy dialogového okna trénování, původní zůstane beze změny.
5. Klikněte na tlačítko "Skóre akce".
6. Klikněte na bodu robotů také nesprávné odpovědi, které se objevily.
7. Klikněte na "+ akce" tlačítko 
    - takže můžeme vytvořit nová akce pro robota, aby odpoví.
8. Zadejte odpověď bodu robotů také jako "žádné problém! "Přeji příjemný den!"
9. Kliknutí na Vytvořit
10. Všimněte si, že okamžitě odpovídá robota.
11. Klikněte na tlačítko "Save".

### <a name="test-the-trainings"></a>Test školení
1. Na levém panelu klikněte na tlačítko "Protokolu dialogů" a "Dialogové okno Nový protokol".
2. Zadejte zprávu, "hi". 
3. Všimněte si, že reaguje robota, automaticky tak, jak jsme školili.
4. Zadejte odpověď uživatele "Ano".
5. Všimněte si, že odpovědi robotů, ukazuje, že první školení funguje.
6. Klikněte na tlačítko "Časový limit relace". To říká Learner konverzace, které že chceme začít znovu, ignoruje se konverzační zapne, které konal úplně stejně.
7. Zadejte zprávu, "hi". 
8. Všimněte si, že reaguje robota, automaticky tak, jak jsme školili.
9. Zadejte odpověď uživatele "Ne".
10. Všimněte si, že odpovědi robotů, ukazuje, že druhý školení funguje.
11. Klikněte na tlačítko "Testování Hotovo".

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Počkejte a bez čekání akce](./03-wait-vs-nonwait-actions.md)

---
title: Jak vytvořit Hello World Conversation Learner modelu – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Naučte se vytvořit model Conversation Learner "Hello World".
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: bb1d053af8813f05872c56d3b5609f2d7d7d5d8c
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705645"
---
# <a name="how-to-create-a-hello-world-model-with-conversation-learner"></a>Jak vytvořit model "Hello World" s Conversation Learner

V tomto kurzu se dozvíte, jak začít s Conversation Learner, včetně vytváření akcí, výukou robota v interaktivně a provádění oprav protokolovaných dialogů, které pocházejí od koncových uživatelů.

## <a name="video"></a>Video

[![Kurz Hello World Preview](https://aka.ms/cl_Tutorial_v3_HelloWorld_Preview)](https://aka.ms/cl_tutorial_v3_helloworld)


## <a name="requirements"></a>Požadavky
Pokud jste to ještě neudělali, ujistěte se, že jste dokončili všechny kroky `.env` instalace, včetně vytvoření souboru s vaším LUISm klíčem pro vytváření obsahu.  Podrobnosti najdete v tématu [rychlý Start](../quickstart.md) .

Tento kurz vyžaduje, aby byl spuštěný robot pro obecné kurzy.

    npm run tutorial-general

## <a name="steps"></a>Kroky

Začněte na domovské stránce ve webovém uživatelském rozhraní.

### <a name="create-the-model"></a>Vytvoření modelu
1. Klikněte na tlačítko Nový model.
2. Do pole název zadejte "Hello World".
3. Klikněte na tlačítko vytvořit.

Nyní byste měli vidět zobrazení modelu, který jste vytvořili.

### <a name="create-an-action"></a>Vytvořit akci
1. Na levém panelu klikněte na akce a pak na tlačítko Nová akce.
    - Akce může být textová zpráva, kterou Conversation Learner vrátí uživateli, volání rozhraní API nebo kartě.
2. V "reakci na robota..." Typ pole "Hello".
    - Toto je odpověď, kterou robot vrátí.
3. Klikněte na tlačítko vytvořit.

Vytvořili jste první akci, kterou robot může provést, tj. vrátí textovou odpověď.

### <a name="train-dialogs"></a>Výuková okna
Tady se naučíte modelovat, jak reagovat na projevy uživatele.

#### <a name="first-training-dialog"></a>První školení – dialogové okno

1. Na levém panelu klikněte na možnost výuka dialogových oken a pak klikněte na tlačítko "nový výukový dialog".
2. Zadejte "Dobrý den", stiskněte klávesu ENTER.
    - Příkladem toho, co uživatel může vyslovit na začátku konverzace.
3. Klikněte na tlačítko "akce skóre".
4. Vyberte "Hello".
    - Právě jste dokončili úplně jednu úplnou volbu v tomto ukázkovém dialogovém okně. 
5. Zadejte odpověď uživatele.
6. Klikněte na tlačítko "akce skóre".
7. Klikněte na tlačítko + akce.
8. Zadejte "rozdálení!" v "reakci na robota..." a potom klikněte na tlačítko vytvořit.
    - Všimněte si, že robot odpověděl s touto akcí, kterou jste právě vytvořili.
9. Klikněte na tlačítko Uložit. 
    - Tím se ukončí a uloží se toto dialogové okno školení.

Nyní máte v modelu jeden dialog školení společně s jednou entitou a dvěma akcemi.

#### <a name="second-training-dialog"></a>Druhý dialog školení
Pojďme udělat ještě jedno školení a podívat se, jak robot reaguje.

1. Klikněte na tlačítko nového výukového dialogového okna.
2. Zadat, "Dobrý den"
    - To je podobné jako v prvním dialogovém okně a očekáváme, že z robota získáme dobré skóre.
3. Klikněte na tlačítko "akce skóre".
    - Pozice a skóre mohou být stále dostatečně přesné a mohou vyžadovat další školení.
4. Vyberte "Hello".
5. Zadejte odpověď uživatele "bye".
6. Klikněte na tlačítko "akce skóre".
7. Vyberte možnost "rozdálení".
8. Klikněte na tlačítko Uložit.

### <a name="log-dialogs"></a>Dialogy protokolu
Tady můžete vyzkoušet, zobrazit a opravit konverzace, které jste vy nebo reálný uživatel měli s vaší robotem.

#### <a name="test-the-model-as-an-end-user"></a>Testování modelu jako koncového uživatele
1. Na levém panelu klikněte na možnost protokolovat dialogy a pak na tlačítko nový dialog protokolu.
2. Zadejte Hello.
3. Počkejte krátkou dobu, robot by měl automaticky reagovat pomocí "Hello".
4. Zadejte ' ByeBye '
5. Počkejte krátkou dobu a robot by měl automaticky reagovat pomocí "Hello".
6. Klikněte na tlačítko dokončené testování.

#### <a name="view-and-correct-a-user-conversation"></a>Zobrazení a oprava konverzace uživatele
Pomocí dialogových oken protokolu můžete zobrazit seznam uživatelů s konverzacemi, které se nacházejí ve vaší robotovi. Můžete je také upravit, aby se opravily odpovědi robota, a uložit interakce jako školicí dialogy. Postup:
1. V mřížce klikněte na protokol konverzace.
2. Klikněte na poslední akci robota, např. "Hello".
3. Vyberte možnost "rozdálení". Opravte robota.
4. Klikněte na tlačítko Uložit jako výukový dialog.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Úvod do školení](./02-intro-to-training.md)

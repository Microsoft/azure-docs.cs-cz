---
title: Jak vytvořit model "Hello World" konverzace Learner – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Zjistěte, jak vytvořit model Learner konverzace "Hello World".
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: c08e3d2e8f712f5eb7c56585507a283c7fd040c1
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796726"
---
# <a name="how-to-create-a-hello-world-model-with-conversation-learner"></a>Jak vytvořit model "Hello World" pomocí Learner konverzace

Tento kurz ukazuje, jak začít pracovat s Learner konverzace, včetně vytváření akcí, interaktivně vyučují robota a provádění oprav zaznamenané dialogová okna, které pocházejí z koncovým uživatelům.

## <a name="video"></a>Video

[![Hello World kurz ve verzi Preview](https://aka.ms/cl_Tutorial_v3_HelloWorld_Preview)](https://aka.ms/cl_tutorial_v3_helloworld)


## <a name="requirements"></a>Požadavky
Pokud jste tak dosud neučinili, nejdřív ověřte všechny instalační kroky nejsou dokončeny, včetně vytváření `.env` soubor vaše služba luis vytváření klíč.  Zobrazit [rychlý Start](../quickstart.md) podrobnosti.

Tento kurz vyžaduje, zda je spuštěna obecné kurzu robota

    npm run tutorial-general

## <a name="steps"></a>Kroky

Začněte na domovskou stránku webového uživatelského rozhraní.

### <a name="create-the-model"></a>Vytvoření modelu
1. Klikněte na tlačítko "Nový Model".
2. V poli "Name" zadejte "Hello World".
3. Klikněte na tlačítko "Vytvořit".

Teď byste měli vidět zobrazení modelu, který jste vytvořili.

### <a name="create-an-action"></a>Vytvoření akce
1. Na levém panelu klikněte na tlačítko "Akce" a potom na tlačítko "Nová akce".
    - Akce může být textová zpráva, která vrací Learner konverzace na uživatele, volání rozhraní API nebo karty.
2. Typ "Hello" pole "Bodu robotů také odpovědi na...".
    - Jedná se o odpověď, která vrátí robota.
3. Klikněte na tlačítko "Vytvořit".

Vytvořili jste první akce, které může provádět robota, to znamená vrácení text odpovědi.

### <a name="train-dialogs"></a>Trénování dialogová okna
To je, kde trénování modelu o tom, jak reagovat na projevy uživatele.

#### <a name="first-training-dialog"></a>Dialogové okno první školení

1. Na levém panelu klikněte na tlačítko "Dialogů Train" a potom na tlačítko "Dialogové okno Nový Train".
2. Typ "Hi", stiskněte enter.
    - Protože se příkladem co uživatel se může jednat na začátku konverzaci.
3. Klikněte na tlačítko "Skóre akce".
4. Vyberte "Hello".
    - Nyní jste dokončili jednu úplnou zapnout v tomto příkladu dialogu. 
5. Zadejte odpověď uživatele "Goodbye".
6. Klikněte na tlačítko "Skóre akce".
7. Klikněte "+ akce" tlačítko.
8. Typ "Goodbye!" "Bodu robotů také odpověď..." pole a potom klikněte na tlačítko "Vytvořit".
    - Všimněte si, že robota odpověděl zprávou, že akce jste právě vytvořili.
9. Klikněte na tlačítko "Save". 
    - To se ukončit a uložit toto dialogové okno školení.

Nyní máte jeden dialogové okno školení v modelu, společně s jednou entitou a dvě akce.

#### <a name="second-training-dialog"></a>Druhé dialogové okno školení
Pojďme provést jeden další školení a naleznete v tématu Jak robota reaguje.

1. Klikněte na tlačítko "Dialogové okno Nový Train".
2. Zadejte, "Hi"
    - Je to podobné jako první dialogové okno a Očekáváme, že k získání dobré skóre z robota.
3. Klikněte na tlačítko "Skóre akce".
    - Pozice a skóre ještě nemusí být dostatečně přesný a může vyžadovat další školicí.
4. Vyberte "Hello".
5. Zadejte odpověď uživatele "viděnou".
6. Klikněte na tlačítko "Skóre akce".
7. Vyberte "Goodbye"!
8. Klikněte na tlačítko "Save".

### <a name="log-dialogs"></a>Dialogová okna protokolu
Toto je, kde můžete testovat, zobrazení a opravte konverzace, které vy nebo skutečné koncoví uživatelé měli s vaším robotem.

#### <a name="test-the-model-as-an-end-user"></a>Otestování modelu jako koncový uživatel
1. Na levém panelu klikněte na tlačítko "Protokolu dialogů" a potom na tlačítko "Dialogové okno Nový protokol".
2. Zadejte "Ahoj".
3. Po chvilce, robot by měl odpovědět automaticky "Hello"
4. Zadejte "byebye.
5. Po chvilce, znovu robot by měl automaticky odpovědět "Hello".
6. Klikněte na tlačítko "Testování Hotovo".

#### <a name="view-and-correct-a-user-conversation"></a>Zobrazení a opravte konverzaci uživatele
Pomocí protokolu dialogová okna, můžete zobrazit seznam konverzacích uživatelů uspořádali svého robota. Můžete také upravit je, aby bylo možné opravit bodu robotů také odpovědi a uložení interakce jako dialogová okna školení. Postup:
1. V mřížce klikněte na protokol konverzace.
2. Například klikněte na poslední akci robota "Hello".
3. Vyberte "Goodbye"! Chcete-li opravit robota.
4. Klikněte na tlačítko "Uložit jako trénování dialogové okno".

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Úvod do školení](./02-intro-to-training.md)

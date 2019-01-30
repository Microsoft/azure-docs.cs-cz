---
title: Použití alternativní vstupy s konverzace Learner – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Další informace o použití alternativních vstupy s Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: c60dc2ca93547b93ce2ee457393570479069c899
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55216264"
---
# <a name="how-to-use-alternative-inputs"></a>Jak používat alternativní vstupy

Tento kurz ukazuje způsob použití pole alternativní vstupy pro uživatele projevy výukovému rozhraní.

## <a name="video"></a>Video

[![Alternativní vstupy kurz ve verzi Preview](https://aka.ms/cl_Tutorial_v3_AlternativeInputs_Preview)](https://aka.ms/cl_Tutorial_v3_AlternativeInputs)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna obecné kurz bot

    npm run tutorial-general

## <a name="details"></a>Podrobnosti
Alternativní vstupy jsou sémanticky ekvivalentní alternativní uživatelské projevy, které uživatel může mít říká, že v určitém místě v dialogovém okně školení. Tyto alternativní vstupy umožňují více kompaktně určit variace projevy aniž byste museli řešit každou změnu v dialogových oknech samostatné školení.

## <a name="steps"></a>Kroky

### <a name="create-the-model"></a>Vytvoření modelu

1. Ve webové uživatelské rozhraní klikněte na tlačítko "Nový Model."
2. V poli "Name" typ "AlternativeInputs" a stiskněte enter.
3. Klikněte na tlačítko "Vytvořit".

### <a name="entity-creation"></a>Vytvoření entity

1. Na levém panelu klikněte na tlačítko "," subjekty a potom na tlačítko "Nová entita".
2. Vyberte "Vlastní školení" u "Typu Entity."
3. Zadejte "city" u "Názvu Entity."
4. Klikněte na tlačítko "Vytvořit".

Teď vytvoříme tři akce.

### <a name="create-the-first-action"></a>Vytvoření první akci

1. Na levém panelu klikněte na tlačítko "Akce" a potom na tlačítko "Nová akce".
2. "Bodu robotů také odpovědi na..." pole, zadejte "Které city"?
3. Do pole "Očekávané Entity v odpovědi uživatele..." zadejte "city".
4. V poli "Vyřazení opravňuje" typ "city".
5. Klikněte na tlačítko "Vytvořit".

### <a name="create-the-second-action"></a>Vytvořte druhou akci

1. Na levém panelu klikněte na tlačítko "Akce" a potom na tlačítko "Nová akce".
2. "Bodu robotů také odpovědi na..." pole, zadejte "o počasí v $city je pravděpodobně hezky."
3. Klikněte na tlačítko "Vytvořit".

### <a name="create-the-third-action"></a>Třetí akce vytvořit

1. Na levém panelu klikněte na tlačítko "Akce" a potom na tlačítko "Nová akce".
2. "Bodu robotů také odpovědi na..." pole, zadejte "Vyzkoušejte si žádá o počasí."
3. V poli "Vyřazení opravňuje" typ "city".
4. Klikněte na tlačítko "Vytvořit".

Teď máte tři akce.

### <a name="train-the-model"></a>Trénování modelu

1. Na levém panelu klikněte na tlačítko "Dialogů Train" a potom na tlačítko "Dialogové okno Nový Train".
2. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "Jak se počasí?"
3. Klikněte na tlačítko "Skóre akce".
4. Vyberte odpověď "Které city"?
5. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "Ostravě."
6. Klikněte na tlačítko "Skóre akce".
7. Vyberte odpověď, "o počasí v Liberci je pravděpodobně hezky."
8. Klikněte na tlačítko "Save".

Pojďme trénování další tak, že vytvoříte další dialog trénování modelu.

### <a name="second-model-train-dialog"></a>Druhé dialogové okno Train Model

1. Na levém panelu klikněte na tlačítko "Dialogů Train" a potom na tlačítko "Dialogové okno Nový Train".
2. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "Co můžete dělat?"
3. Klikněte na tlačítko "Skóre akce".
4. Vyberte odpověď, "Vyzkoušejte si žádá o počasí."
5. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "Jak se o počasí v Praze?"
6. Klikněte na tlačítko "Seattle" a potom klikněte na "city" v seznamu entit.
7. Klikněte na tlačítko "Skóre akce".
8. Vyberte odpověď, "o počasí v Praze je pravděpodobně hezky."
9. Klikněte na tlačítko "Save".

### <a name="third-model-train-dialog-using-alternative-input"></a>Třetí dialogové okno Train Model pomocí alternativní vstupu

1. Na levém panelu klikněte na tlačítko "Dialogů Train" a potom na tlačítko "Dialogové okno Nový Train".
2. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "help"
3. Klikněte na tlačítko "Skóre akce".
    - Model nejistoty. pro nejlepší možností, takže ji byste zvolili nejvyšší percentilu ve výchozím nastavení.
4. Klikněte na tlačítko "Zrušit výuky" a potom tlačítko "Confirm".

![](../media/tutorial8_closescores.png)

Umožňuje lépe ladění systému pomocí alternativní vstupy. Můžete přidat alternativní vstup během vyučování nebo novější.

5. Na levém panelu klikněte na tlačítko "Dialogů Train" a pak vyberte "Co můžete dělat?" ze seznamu trénování dialogová okna.
6. Klikněte "co můžete dělat?" utterance panelu konverzace.
7. V "Přidáním Alternativní vstup..." pole, typ "Nápověda" a stiskněte enter.
8. Klikněte na tlačítko "Uložit změny".

![](../media/tutorial8_helpalternates.png)

Přidejme jiný Alternativní vstup pro zpracování Houstonu.

9. Klikněte "jak se o počasí v Praze?" utterance panelu konverzace.
10. V poli "Přidat alternativní vstup..." typ "předpovídáním Houstonu" a stiskněte enter.
    - Stručný přehled zpráva chyby vstupů alternativní fakt musí být sémanticky rovnocenné a musí obsahovat stejné entity jako původní utterance; nejen stejné hodnoty entity. Přítomnost stejné entity je povinný.
11. Klikněte na "Houstonu" a vyberte ze seznamu entit "city".
12. V poli "Přidat alternativní vstup..." typ "prognózy pro Seattle" a stiskněte enter.
13. Klikněte na "Seattle" a vyberte ze seznamu entit "city".
14. Klikněte na tlačítko "Uložit změny".
15. Klikněte na tlačítko "Upravit uložit".

### <a name="testing-the-model"></a>Testování modelu

1. Na levém panelu klikněte na tlačítko "Protokolu dialogů" a "Dialogové okno nové protokolu."
2. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "Nápověda me"
3. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "předpovědi pro Ostravě."

![](../media/tutorial8_altcities.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Dialogová okna protokolu](./11-log-dialogs.md)

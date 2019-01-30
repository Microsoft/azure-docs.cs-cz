---
title: Použití karty s modelem Learner konverzace, část 1 – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Další informace o použití karty s modelem Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: a2db74e4c5688c0a2a3975ff828caf20c3cf7904
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55222572"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Použití karty (část 1 ze 2)

Tento kurz ukazuje, jak přidat a používat jednoduché karty svého robota.

> [!NOTE]
> Konverzace Learner aktuálně očekává, že vaše karta definiční soubory umístěné v adresáři s názvem "karty", který se nachází v adresáři začínali robota.

## <a name="video"></a>Video

[![Kurz ve verzi Preview karty](https://aka.ms/cl_Tutorial_v3_Cards_Preview)](https://aka.ms/cl_Tutorial_v3_Cards)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna obecné kurz bot

    npm run tutorial-general

## <a name="details"></a>Podrobnosti

Karty jsou prvky uživatelského rozhraní, které umožní uživateli vybrat možnost v konverzaci. 

### <a name="open-the-demo"></a>Otevřete ukázku

Ve webovém uživatelském rozhraní klikněte na "Import kurzy" a vyberte model s názvem "Kurzu-15karty".

### <a name="the-card"></a>Karta

Karta definice je v následujícím umístění: C:\<installedpath\>\src\cards\prompt.json.

Systém očekává definic karty v tomto adresáři "karty".

![](../media/tutorial13_prompt.PNG)

> [!NOTE]
> Všimněte si, že text do textového pole zadejte "TextBlock" a zástupný symbol "{{otázky}}".
> Existují dvě odeslání tlačítka a text, který získá odeslané pro každý.

### <a name="actions"></a>Akce

Vytvořili jsme tři akce. Jak vidíte níže, je první akcí karta.

![](../media/tutorial13_actions.PNG)

Podívejme se, jak byl vytvořen typ akce karty:

![](../media/tutorial13_cardaction.PNG)

> [!NOTE]
> Karta obsahuje tři různé parametry - otázku vstup, tlačítka 1 a 2. Tyto prvky jsou odkazy na šablony v kartě, kde můžete zadat otázku a příslušných odpovědí. Lze také odkazovat a používat entity nebo kombinaci textu a entity.

Na ikonu oka se dozvíte, jak vypadá karta.

### <a name="practicing-creating-card-actions"></a>Cvičení vytváření karta Akce

1. Na levém panelu klikněte na tlačítko "Akce" a potom na tlačítko "Nová akce".
2. Vyberte "Karta" pro "Typ akce."
3. Ze seznamu "Šablona" Vyberte "řádku".
4. V poli "otázku" typ "Přejít doleva nebo doprava"
5. V poli "button1" typ "left"
6. V poli "button2" typ "vpravo"
7. Klikněte na tlačítko "Storno".

### <a name="train-dialog-using-an-adaptive-card"></a>Trénování dialogového okna pomocí adaptivní karta

1. Na levém panelu klikněte na tlačítko "Dialogů Train" a potom na tlačítko "Dialogové okno Nový Train".
2. V panelu chatu, kde říká "Zadejte zprávu...", zadejte "hi"
3. Klikněte na tlačítko "Skóre akce".
4. Vyberte odpověď na "řádku: dotaz: Přejít doleva nebo doprava?"
    - Na ikonu oka je možné zobrazit náhled karty
5. V panelu chat klikněte na tlačítko "Left" vykreslené příkazovém řádku.
6. Klikněte na tlačítko "Skóre akce".
7. Vyberte odpověď "Left"
8. Klikněte na tlačítko "Save".
9. Vyberte odpověď na "řádku: dotaz: Přejít doleva nebo doprava?"
10. V panelu chat klikněte na tlačítko "Vpravo" vykreslené příkazovém řádku.
11. Klikněte na tlačítko "Skóre akce".
12. Vyberte odpověď, "Vpravo"

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Hybridní robotů](./16-hybrid-bots.md)

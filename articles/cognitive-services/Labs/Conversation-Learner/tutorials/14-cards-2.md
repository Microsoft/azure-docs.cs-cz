---
title: Jak použít karty s aplikací student konverzace, část 2 - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Další informace o použití karty s aplikací student konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 254f0953fd3e281a35857e69d9795e3decebf45d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343225"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Postup použití karty (část 1 / 2)
Tento kurz ukazuje, jak přidat na kartě polí formuláře do vaší robota. Vám ukáže, jak přesunout polí formuláře do entity.

Konverzace student očekává, že vaše soubory karta Definice umístěný v adresář s názvem "znaky", který se nachází v adresáři, kde je spuštěna robota.

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby obecné kurz robota běží

    npm run tutorial-general

## <a name="details"></a>Podrobnosti

Karty jsou prvky uživatelského rozhraní, které umožňují uživateli vybrat možnost v konverzaci. 

### <a name="open-the-demo"></a>Otevřete ukázku

V seznamu aplikací webového uživatelského rozhraní klikněte na kurz-14karty-2. 

### <a name="the-card"></a>Karta

Karta definice je v následujícím umístění: C:\<installedpath\>\src\cards\shippingAddress.json.

Tato karta shromažďuje tři pole z adresy příjemce: Město, ulici a stavu.

![](../media/tutorial14_card.PNG)

### <a name="actions"></a>Akce

Vytvořili jsme tři akce. Jak vidíte níže, je první akcí karta.

![](../media/tutorial14_actions.PNG)

Podívejme se, jak byl vytvořen typ akce karty:

- Všimněte si, ulice, kde je typ Input.text a jeho ID.
- Podobně je adresa město a rozevírací seznam s ID z adres stavu.

ID jsou důležité, pokud pole se vyplní a odeslání, ty jsou názvy entit, které obdrží tyto hodnoty v robota.

## <a name="entities"></a>Entity
Jsme definovali tři entity odpovídající kartu, jak jsme viděli výše.

![](../media/tutorial14_entities.PNG)

## <a name="actions"></a>Akce

Jsme definovali dvě akce.

![](../media/tutorial14_actions.PNG)

- První je přenosů adresa karty, kde je pro typ akce karta a z rozevíracího seznamu jako shippingAddress je vybraná šablona.
- Druhá je jednoduchý akce číst obsah adresy příjemce.

![](../media/tutorial14_sa_card.PNG)

### <a name="train-dialog"></a>Dialogové okno Train

Projděme vyučující dialogové okno.

1. Klikněte na tlačítko Train dialogová okna, dialogové okno pak nový vlaku.
1. Zadejte "hi".
2. Klikněte na tlačítko akce skóre.
3. Kliknutím vyberte "Dodací adresy".
4. Zadejte na kartě a odeslat.
    - Všimněte si, že tyto hodnoty nyní byly přesunuty do paměti entity. Žádné analýza je potřeba, protože formulář již oddíly vstupy.
5. Klikněte na tlačítko akce skóre.
3. Kliknutím vyberte ' přesouvání $Address... ".
4. Klikněte na tlačítko Hotovo testování.

![](../media/tutorial14_train_dialog.PNG)

Jste nyní viděli, jak získat hodnoty z karty, který má polí k vyplnění a rozevíracích seznamů, a k zachycení a uloží do robota entity.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření větve a vrácení zpět](./15-branching-and-undo.md)

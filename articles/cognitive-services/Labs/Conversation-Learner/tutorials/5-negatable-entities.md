---
title: Jak používat negatable entity s aplikací konverzace student - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Naučte se používat s aplikací konverzace student negatable entity.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 3d65376c9c43ee1407468f3e8bf3e058048bd556
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343259"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-application"></a>Jak používat negatable entity s konverzace student aplikací

Tento kurz představuje vlastnost "negatable" entit.

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby obecné kurz robota běží

    npm run tutorial-general

## <a name="details"></a>Podrobnosti
Označit akce jako "negatable", pokud uživatel můžete "Vymazat" hodnota entity, jako "Ne, nechci $entity" nebo "Ne, není $entity." Například "Ne, není chcete opustit z Boston."

Namítají Pokud je nastavena vlastnost "negatable" entity:

- Při označování entity uvádí, vám umožní popisku normální (kladné) instance entity a "negativní" instance entity
- LEOŠ zjišťuje dva modely entity: jeden pro pozitivní instancí a druhý pro záporná instance
- Účinek záporné instanci entity je vymažte tuto hodnotu z proměnné entit (pokud existuje)

## <a name="steps"></a>Kroky

### <a name="create-the-application"></a>Vytvoření aplikace

1. Ve webovém uživatelském rozhraní klikněte na nové aplikace
2. Do pole Název zadejte NegatableEntity. Klikněte na tlačítko vytvořit.

### <a name="create-an-entity"></a>Vytvořte entitu

1. Klikněte na tlačítko entity, pak novou entitu.
2. Název Entity zadejte název.
3. Zkontrolujte Negatable.
    - To znamená, uživatel bude moct zadat hodnotu pro entitu, nebo je vyslovte něco *není* hodnota entity. V takovém případě bude výsledkem odstraňování hodnotu odpovídající entity.
3. Klikněte na Vytvořit.

![](../media/tutorial5_entities.PNG)

### <a name="create-two-actions"></a>Vytvořte dvě akce

1. Klikněte na tlačítko akce, pak nová akce
2. V odpovědi zadejte 'Neznámého název'.
3. Vyřazení entity zadejte název.
3. Kliknutí na Vytvořit

Pak vytvořte druhou akci.

1. Klikněte na tlačítko akce pak nová akce vytvoří druhou akci.
3. V odpovědi, zadejte "poznám, že vaše jméno. Je $name'.
4. Kliknutí na Vytvořit

Nyní máte dvě akce.

![](../media/tutorial5_actions.PNG)

### <a name="train-the-bot"></a>Cvičení robota

1. Klikněte na tlačítko Train dialogová okna, dialogové okno pak nový vlaku.
2. Zadejte text "hello".
3. Klikněte na tlačítko akce skóre a vyberte 'neznámého vaše jméno.
    - Všimněte si, že je získávání skóre 100 %, protože se jedná pouze platné akce.
2. Zadejte "Moje název je david.
3. Vyberte 'david' a vyberte štítek sčítání (+ název)
    - Všimněte si, existují dvě instance "název": '+ název' a '-název '.  Plus znamená, že společnost Microsoft poskytuje tuto hodnotu. Minus znamená nám informace o tom, systému, něco není hodnota.
5. Klikněte na tlačítko akce skóre
    - Poznámka: název hodnota je nyní v paměti robota.
    - ' Poznám, že vaše jméno. Je $name' je k dispozici pouze odpovědi. 
6. Vyberte ' poznám, že vaše jméno. Je $name'.

Pojďme vymažte negatable entity:

7. Zadejte 'Moje název není david'.
    - Všimněte si, není je vybraný jako název založený na předchozí vzorek. To je nesprávný.
2. Klikněte na "not" a potom na červené x. 
3. Klikněte na 'david'.
    - Toto je nyní záporné entity komunikaci, že toto není hodnota název entity.
2. Vyberte '-name'.
3. Klikněte na tlačítko akce skóre.
    - Všimněte si, že hodnota byl vymazán z paměti.
2. Vyberte "Neznámého název", což je jedinou akcí.

Další ukážeme, jak lze zadat novou hodnotu pro název.

3. Jako název zadejte "Jan". Poté vyberte "Jan" a klikněte na název.
4. Klikněte na tlačítko akce skóre.
5. Vyberte ' poznám, že vaše jméno. Je $name'.

Nyní zkuste vyměnit zadaný název.

6. Zadejte "Moje název je susan".
7. Vyberte ' poznám, že vaše jméno. Je $name'.
7. Klikněte na tlačítko akce skóre.
8. Všimněte si **susan** přepsaly **Jan** v hodnot entity.
9. Zadejte 'Moje název není susan'.
    - Všimněte si, že tento systém má označený jako záporné instance.
2. Klikněte na tlačítko akce skóre.
3. Vyberte "Neznámého název", což je jedinou akcí.
7. Klikněte na tlačítko Hotovo vyučující.

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vícehodnotový entity](./6-multi-value-entities.md)

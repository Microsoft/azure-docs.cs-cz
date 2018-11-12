---
title: Jak používat negatable entity pomocí modelu konverzace Learner – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Další informace o použití negatable entity s modelem Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: df6499410da3ae67715ade29dbc3cc4146fc2265
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51231987"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Jak používat negatable entity s modelem Learner konverzace

Tento kurz ukazuje vlastnost "negatable" entit.

## <a name="video"></a>Video

[![Kurz 5 Preview](https://aka.ms/cl-tutorial-05-preview)](https://aka.ms/blis-tutorial-05)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna obecné kurz bot

    npm run tutorial-general

## <a name="details"></a>Podrobnosti
Pokud uživatel může "clear" hodnota entity, stejně jako v "Ne, nechci $entity" označit akce jako "negatable" nebo "Ne, není $entity." Například "Ne, nechci se opustit z Bostonu."

Namítají Pokud je nastavená vlastnost "negatable" entity:

- Při označování entit zmínky, umožňují označit normální (pozitivní) instancí entity a "záporné" instance entity
- Služba LUIS naučí dvou modelech entity: jeden pro pozitivní instancí a druhý pro záporné instancí
- Efekt negativní instance entity je vymazat tuto hodnotu z entity proměnné (pokud existuje)

## <a name="steps"></a>Kroky

### <a name="create-the-model"></a>Vytvoření modelu

1. V webového uživatelského rozhraní klikněte na nový Model
2. Do pole Název zadejte NegatableEntity. Pak klikněte na tlačítko vytvořit.

### <a name="create-an-entity"></a>Vytvoření entity

1. Klikněte na entity a pak novou entitu.
2. V názvu Entity zadejte název.
3. Kontrola Negatable.
    - Tato vlastnost určuje, uživatel bude moct zadat hodnotu pro entitu nebo Dejme tomu, že se něco *není* hodnota entity. V takovém případě to způsobí odstranění odpovídající hodnota entity.
3. Klikněte na Vytvořit.

![](../media/tutorial5_entities.PNG)

### <a name="create-two-actions"></a>Vytvořte dvě akce

1. Klikněte na tlačítko akce a potom novou akci
2. V odpovědi zadejte "Nevím vaše jméno".
3. Vyřazení entit zadejte název.
3. Kliknutí na Vytvořit

Vytvořte druhou akci.

1. Klikněte na tlačítko akce a pak novou akci na vytvoří druhou akci.
3. V odpovědi, zadejte "poznám, že vaše jméno. Je $name ".
4. Kliknutí na Vytvořit

Nyní máte dvě akce.

![](../media/tutorial5_actions.PNG)

### <a name="train-the-bot"></a>Natrénování robota

1. Klikněte na dialogová okna trénování, pak nové dialogové okno trénování.
2. Zadejte "hello".
3. Klikněte na tlačítko akce skóre a vyberte "Nevím své jméno.
    - Skóre je 100 %, protože je platné pouze akce.
2. Zadejte "Moje jméno je david"
3. Vyberte "david" a vyberte popisek "+ název.
    - Existují dvě instance "name": "+ Jmeno" a "-name'.  (+) A navíc přidává nebo přepíše hodnotu. (-) Minus odebere hodnotu.
5. Klikněte na výsledek akce
    - Hodnota názvu je nyní v bodu robotů také paměti.
    - "Vím své jméno. Je $name' je k dispozici pouze odpovědi. 
6. Vyberte "poznám, že vaše jméno. Je $name ".

Pojďme si vyzkoušet vymazání negatable entity:

7. Zadejte "Moje jméno není david".
    - Všimněte si, že "not" je vybrán jako název bude vycházet z předchozího vzoru názvů. Tento popisek je nesprávný.
2. Klikněte na "not", pak červený symbol x. 
3. Klikněte na "david".
    - Toto je nyní záporné entity komunikaci, že to není hodnota název entity.
2. Vyberte "-name'.
3. Klikněte na výsledek akce.
    - Všimněte si, že hodnota se vymazala z paměti.
2. Vyberte "Nevím vaše jméno", což je jedinou akcí.

Vedle vám ukážeme, jak lze zadat novou hodnotu pro název.

3. Jako název zadejte "Jan". Potom vyberte "john" a klikněte na název.
4. Klikněte na výsledek akce.
5. Vyberte "poznám, že vaše jméno. Je $name ".

Teď zkuste vyměnit zadaný název.

6. Zadejte "Moje jméno je susan".
7. Vyberte "poznám, že vaše jméno. Je $name ".
7. Klikněte na výsledek akce.
8. Všimněte si, že **susan** přepsaly **Jan** hodnot entity.
9. Zadejte "Moje jméno není susan".
    - Všimněte si, že systém má to označeny jako záporné instance.
2. Klikněte na výsledek akce.
3. Vyberte "Nevím vaše jméno", což je jedinou akcí.
7. Klikněte na Hotovo výuky.

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vícehodnotový entity](./6-multi-value-entities.md)

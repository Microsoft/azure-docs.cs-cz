---
title: Jak používat vlastnost "očekáván entity" konverzace Learner akce – služba Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Zjistěte, jak použít vlastnost "očekáván entity" konverzace Learner modelu.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 9e685d2281330457e83abde0a8ac26e086da7479
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51229641"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>Jak používat vlastnost "Očekáván entity" akcí

Tento kurz ukazuje, pole "očekáván entity" akcí.

## <a name="video"></a>Video

[![Kurz 4 ve verzi Preview](https://aka.ms/cl-tutorial-04-preview)](https://aka.ms/blis-tutorial-04)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna obecné kurz bot

    npm run tutorial-general

## <a name="details"></a>Podrobnosti
Použijte pole "očekáván entity" akce pro komunikaci v systému, můžete očekávat, že odpověď uživatele na akci bude do stavu entity.

Namítají Pokud je pole "očekáván entity" akce nastavená $entity a potom na další utterance uživatele, systém bude:

1. Nejprve už jsme zvyklí, se pokusí vyhledat entity pomocí modelu extrakce entity na základě strojového učení
2. Pokud se nenajdou žádné entity v kroku 1, pak – jako heuristické metody – celé uživatelské utterance přiřadíte $entity.
3. Volání `EntityDetectionCallback` obvyklým způsobem a pokračujte na výběr akce.

## <a name="steps"></a>Kroky

### <a name="create-the-model"></a>Vytvoření modelu

1. V webového uživatelského rozhraní klikněte na nový Model
2. Do pole Název zadejte ExpectedEntities. Pak klikněte na tlačítko vytvořit.

### <a name="create-an-entity"></a>Vytvoření entity

1. Klikněte na entity a pak novou entitu.
2. V názvu Entity zadejte název.
3. Kliknutí na Vytvořit

> [!NOTE]
> Typ entity je "vlastní". Tato hodnota znamená, že entita může školení.  Existují také předem vytvořené entity, což znamená, že jejich chování nejde upravit.  Tyto entity se věnují [předem připravených entit kurzu](./7-built-in-entities.md).

![](../media/tutorial4_entities.PNG)

### <a name="create-two-actions"></a>Vytvořte dvě akce

1. Klikněte na tlačítko akce a potom novou akci.
2. V odpovědi zadejte "Jak se jmenuješ?".
3. Byl očekáván entity zadejte $name. Klikněte na Uložit.
    - Tato hodnota znamená, že pokud tento dotaz se zobrazí výzva, a odpověď uživatele nemá žádné entity, zjistili, robot by měl Předpokládejme, že celá odpověď uživatele je tuto entitu.
    - Entita je automaticky přidán jako disqualifying entity. 
2. Klikněte na tlačítko akce a pak novou akci na vytvoří druhou akci.
3. V odpovědi zadejte "Hello $name".
    - Entita je automaticky přidán jako požadované entity.
4. Klikněte na Uložit.

Nyní máte dvě akce.

![](../media/tutorial4_actions.PNG)

### <a name="train-the-bot"></a>Natrénování robota

1. Klikněte na dialogová okna trénování, pak nové dialogové okno trénování.
2. Zadejte "hello".
3. Klikněte na výsledek akce a vyberte "Jak se jmenuješ?"
    - Odpověď 'Hello $name' nelze vybrat, protože vyžaduje entita $name, která má být definována, a $name se nenachází v paměti bodu robotů také.
2. Zadejte "david". 
    - Název je zvýrazněn jako entity. Toto je z důvodu heuristiky, který nastavíme výše vyberte odpovědi jako entity.
5. Klikněte na výsledek akce
    - Hodnota názvu je nyní v bodu robotů také paměti.
    - "Hello $name" je teď dostupná jako odpověď. 
6. Vyberte "Hello $name".
7. Klikněte na Hotovo výuky.

Tady jsou dva příklady kde extrakce entity model strojového učení identifikuje název, tak heuristiky "očekáván entity" neaktivuje.

1. Kliknutím na dialogové okno Nový trénování.
2. Zadejte "Moje jméno je david".
    - David modelu identifikuje jako název entity, protože zaznamenal toto slovo před.
2. Klikněte na výsledek akce
3. Vyberte "Hello $name".
4. Zadejte "Moje jméno je susan".
    - Model identifikuje susan jako název, protože ji už zobrazit tento model.
2. Klikněte na výsledek akce.
2. Vyberte "Hello susan".
3. Klikněte na Hotovo výuky.

V následujících příkladech heuristiky "očekáván entity" spustí, ale je nesprávný. V příkladech potom ukazují, jak k provedení opravy.

1. Zadejte "zavolat jose".
    - Model nemůže rozpoznat název jako entity.
2. Klikněte na blog jose a vyberte název.
3. Klikněte na výsledek akce.
4. Vyberte hello $name.
5. Klikněte na Hotovo výuky.
1. Kliknutím na dialogové okno Nový trénování.
2. Zadejte "hello".
3. V reakci na "jak se jmenuješ" zadejte "Mi někdo volá frank".
    - Je zvýrazněn celou frázi. Je to proto statistické modelu nebyl nalezen název, takže heuristiky aktivoval a vybrat celé odpovědi jako název entity.
2. K opravit, klikněte na zvýrazněné věty a potom klikněte na na červenou ikonu koše. 
3. Kliknutím vyberte frank a potom klikněte na název.
2. Klikněte na výsledek akce
3. Vyberte "Hello $name".
4. Klikněte na Hotovo výuky.

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Negatable entity](./5-negatable-entities.md)

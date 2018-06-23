---
title: Jak používat vlastnost "očekávané entity" konverzace student akcí - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Naučte se používat vlastnost "očekávané entity" aplikace student konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 83911eba8112cf356af8c4cd562a87f746fbabc5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343256"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>Jak používat vlastnost "Očekávané entity" akcí

Tento kurz představuje pole "očekávané entity" akcí.

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby obecné kurz robota běží

    npm run tutorial-general

## <a name="details"></a>Podrobnosti
Použití pole "očekávané entity" akce ke komunikaci systému, které předpokládáte, že odpověď uživatele na akci bude do stavu entity.

Namítají Pokud je pole "očekávané entity" akce nastavená $entity a potom na další utterance uživatele, bude systém:

1. Nejprve obvyklým způsobem, pokus o vyhledání entit pomocí modelu machine learningu založené na entity extrakce
2. Pokud žádné entity se nacházejí v kroku 1, pak – jako Heuristika – přiřadíte utterance celé uživatelské $entity.
3. Volání `EntityDetectionCallback` obvyklým způsobem a pokračujte výběr akce.

## <a name="steps"></a>Kroky

### <a name="create-the-application"></a>Vytvoření aplikace

1. Ve webovém uživatelském rozhraní klikněte na nové aplikace
2. Do pole Název zadejte ExpectedEntities. Klikněte na tlačítko vytvořit.

### <a name="create-an-entity"></a>Vytvořte entitu

1. Klikněte na tlačítko entity, pak novou entitu.
2. Název Entity zadejte název.
3. Kliknutí na Vytvořit

Poznámka: typ entity je "vlastní" – to znamená, že entita může cvičení.  Existují také předem připravené entity, což znamená, že jejich chování nemůže být upravena – to jsou popsané v jiné kurzu.

![](../media/tutorial4_entities.PNG)

### <a name="create-two-actions"></a>Vytvořte dvě akce

1. Klikněte na tlačítko akce, pak nová akce
2. V odpovědi zadejte, co je název vaší?".
3. Očekávaný entity zadejte $name. Klikněte na tlačítko Uložit.
    - To znamená, že pokud tuto otázku se zobrazí výzva, a odpověď uživatele nemá žádné entity zjištěna, robota by měl předpokládají, že celé odpovědi uživatele je této entity.
2. Klikněte na tlačítko akce pak nová akce vytvoří druhou akci.
3. V odpovědi zadejte 'Hello $name'.
    - Všimněte si, že entita se automaticky přidá jako disqualifying entitu. 
4. Kliknutí na Uložit

Nyní máte dvě akce.

![](../media/tutorial4_actions.PNG)

### <a name="train-the-bot"></a>Cvičení robota

1. Klikněte na tlačítko Train dialogová okna, dialogové okno pak nový vlaku.
2. Zadejte text "hello".
3. Klikněte na tlačítko akce skóre a vyberte "Co je název vaší?"
    - Všimněte si, že odpověď "Hello $name' není k dispozici, protože ho requies entity $name, která má být definován a $name není v paměti na robota.
2. Zadejte 'david'. 
    - Všimněte si, že název je označený jako entity. To je kvůli heuristiky, které jsme vytvořili výše vybrat odpovědi jako entity.
5. Klikněte na tlačítko akce skóre
    - Poznámka: název hodnota je nyní v paměti robota.
    - 'Hello $name' je nyní k dispozici jako odpověď. 
6. Vyberte 'Hello $name'.
7. Klikněte na tlačítko Hotovo vyučující.

Tady jsou dva příklady kde extrakce model entity strojové učení identifikuje název, takže heuristiky "očekávané entity" není aktivována.

1. Kliknutím na dialogové okno Nový vlaku.
2. Zadejte "Moje název je david".
    - Všimněte si, že toto identifikaci david jako název entity protože zaznamenal slovo před.
2. Klikněte na tlačítko akce skóre
3. Vyberte 'Hello $name'.
4. Zadejte "Moje název je susan".
    - Všimněte si, že ho susan identifikuje jako název vzhledem k tomu, že má již vidět tento vzor.
2. Klikněte na tlačítko akce skóre.
2. Vyberte 'Hello susan'.
3. Klikněte na tlačítko Hotovo vyučující.

Tady je jsou dva další příklady kde aktivuje heuristiky "očekávané entity", ale není správný, a jak bychom mohli opravu.

1. Zadejte 'zavolat jose'.
    - Všimněte si, že jako entity nelze rozpoznat název.
2. Klikněte na jose a vyberte název.
3. Klikněte na tlačítko akce skóre.
4. Vyberte hello $name.
5. Klikněte na tlačítko Hotovo vyučující.
1. Kliknutím na dialogové okno Nový vlaku.
2. Zadejte text "hello".
3. V reakci na "co je název" zadejte 'Mě volání František'.
    - Všimněte si, že je označený celou frázi. Je to proto, že model statistické nebyl nalezen název, takže heuristiky aktivováno a vybrat celé odpovědi jako název entity.
2. Opravte ho, klikněte na zvýrazněné frázi a potom klikněte na červené x. 
3. Kliknutím vyberte František a potom klikněte na název.
2. Klikněte na tlačítko akce skóre
3. Vyberte 'Hello $name'.
4. Klikněte na tlačítko Hotovo vyučující.

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Negatable entity](./5-negatable-entities.md)

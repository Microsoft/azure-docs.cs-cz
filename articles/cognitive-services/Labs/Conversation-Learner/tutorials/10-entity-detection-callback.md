---
title: Jak používat entity detekce zpětné volání s aplikací konverzace student - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Naučte se používat s aplikací konverzace student entity detekce zpětného volání.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e41ea5930ff0c8395d0c93aa42e224ebfc894ba8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343244"
---
# <a name="how-to-use-entity-detection-callback"></a>Jak používat entity detekce zpětného volání

Tento kurz ukazuje zpětného volání detekce entity a znázorňuje běžný vzor řešení entity.

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna robota "tutorialEntityDetectionCallback".

    npm run tutorial-entity-detection

## <a name="details"></a>Podrobnosti
Zpětné volání detekce entity umožňuje pomocí vlastní kód pro zpracování obchodní pravidla související s entitami. V této ukázce použijeme zpětná volání a programové entity přeložit název města zadá uživatel kanonický název – například řešení "velký apple" na "Praha".

### <a name="open-the-demo"></a>Otevřete ukázku

V seznamu aplikací klepněte na kurz-10-EntityDetenctionCallback. 

### <a name="entities"></a>Entity

V aplikaci jsme definovali tři entity.

![](../media/tutorial10_entities.PNG)

1. Město je vlastní entity, který uživatel bude poskytovat jako zadávání textu.
2. CityUnknown je programový entita. Budou obsazeny v systému. Pokud systém nebude vědět, které města, je budou zkopírovány vstup uživatele.
3. CityResolved je město, které systém vědět o. Název v kanonickém tvaru města je například "velký apple, bude odkazující na, new york, bude.

### <a name="actions"></a>Akce

Vytvořili jsme tři akce. 

![](../media/tutorial10_actions.PNG)

1. Je první akcí, které města chcete?"
2. Druhá ' neznámého tento města $CityUknown. Které města chcete? "
3. Je třetí "uvedená $City a který lze přeložit na $CityResolved."

### <a name="callback-code"></a>Zpětné volání kódu

Podívejme se na kód. Metoda EntityDetectionCallback můžete najít v jednotce C:\<installedpath > \src\demos\tutorialSessionCallbacks.ts souboru.

![](../media/tutorial10_callbackcode.PNG)

Tato funkce volána po entity řešení došlo k chybě.
 
- První krok, který ji provede je zrušte $CityUknown. $CityUknown se uchová pouze pro jeden zapnout, jako jeho vždy získá zaškrtnutí zrušit na začátku.
- Potom se nám získat seznam města, které byly rozpoznány. Provést první z nich a pokuste se ho vyřešit.
- Je definována funkce, která přeloží ji (resolveCity) Další výše v kódu. Obsahuje seznam názvů kanonický města. Najde název města v seznamu, se vrátí. Jinak hledá v 'cityMap a vrátí název namapované. Pokud nemůže najít města, vrátí hodnotu null.
- Navíc pokud město rozpoznal na název, uložíme ho v $CityKnown entity. Jinak vymažte, co uživatel má uvedená a naplnit $CityUknown entity.

### <a name="train-dialogs"></a>Cvičení dialogová okna

1. Klikněte na tlačítko Train dialogová okna, dialogové okno pak nový vlaku.
2. Zadejte text "hello".
3. Klikněte na tlačítko akce skóre a vyberte, které města chcete?"
2. Zadejte, new york'.
    - Všimněte si, že získá rozpoznán jako města entity.
5. Klikněte na tlačítko akce skóre
    - Všimněte si, že jsou vyplněna Město a CityResolved.
6. Vyberte 'uvedená $City a který lze přeložit na $CityResolved'.
7. Klikněte na tlačítko Hotovo vyučující.

Přidejte další dialog příklad:

1. Kliknutím na dialogové okno Nový vlaku.
2. Zadejte text "hello".
3. Klikněte na tlačítko akce skóre a vyberte, které města chcete?"
2. Zadejte 'velký apple'.
    - Všimněte si, že získá rozpoznán jako města entity.
5. Klikněte na tlačítko akce skóre
    - Všimněte si, že CityResolved ukazuje účinek kód spuštěný.
6. Vyberte 'uvedená $City a který lze přeložit na $CityResolved'.
7. Klikněte na tlačítko Hotovo vyučující.

Přidejte další dialog příklad:

1. Kliknutím na dialogové okno Nový vlaku.
2. Zadejte text "hello".
3. Klikněte na tlačítko akce skóre a vyberte, které města chcete?"
2. Zadejte "foo".
    - Jedná se o příklad města, které systém nezná. 
5. Klikněte na tlačítko akce skóre
6. Vyberte ' neznámého tento města $CityUknown. Které města chcete? ".
7. Zadejte, new york'.
8. Klikněte na tlačítko akce skóre.
    - Upozorňujeme, že byl vymazán CityUknown a CityResolved se naplní.
6. Vyberte 'uvedená $City a který lze přeložit na $CityResolved'.
7. Klikněte na tlačítko Hotovo vyučující.

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zpětná volání relace](./11-session-callbacks.md)

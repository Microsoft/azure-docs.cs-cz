---
title: Jak používat entity detekce zpětného volání s modelem konverzace Learner – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Další informace o použití zjišťování entit zpětného volání s modelem Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f168018a23d03ffb957da2dd1f67881420a21208
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171099"
---
# <a name="how-to-use-entity-detection-callback"></a>Jak používat entity detekce zpětného volání

Tento kurz ukazuje zpětného volání zjišťování entit a znázorňuje běžný vzor pro vyřešení entity.

## <a name="video"></a>Video

[![Kurz 10 ve verzi Preview](http://aka.ms/cl-tutorial-10-preview)](http://aka.ms/blis-tutorial-10)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby `tutorialEntityDetectionCallback` bot běží.

    npm run tutorial-entity-detection

## <a name="details"></a>Podrobnosti
Zpětné volání zjišťování entit umožňuje psát vlastní kód pro zpracování obchodních pravidel související s entitami. Tato ukázka používá zpětná volání a programové entity název města zadaná uživatelem do kanonického názvu – například řešení "velký objem apple" k "new york".

### <a name="open-the-demo"></a>Otevřete ukázku

V seznamu model klikněte na kurz-10-EntityDetectionCallback. 

### <a name="entities"></a>Entity

Tři entity jsou definovány v modelu.

![](../media/tutorial10_entities.PNG)

1. Město je vlastní entitu, která uživatel poskytne jako textové zadání.
2. CityUnknown je entita, prostřednictvím kódu programu. Tuto entitu budou obsazeny v systému. Pokud systém nezná Město, které je budou zkopírovány uživatelský vstup.
3. CityResolved je město, která upozorní systém. Tato entita bude třeba se přeloží "velký objem apple" "new york" kanonický název města.

### <a name="actions"></a>Akce

Tři akce jsou definovány v modelu.

![](../media/tutorial10_actions.PNG)

1. První "action" je "Město, který chcete?"
2. Druhým je "Toto město $CityUknown nevím. Město, které si přejete? "
3. Třetí je "jsi $City a můžu vyřešit, který $CityResolved."

### <a name="callback-code"></a>Zpětné volání kódu

Podívejme se na kód. Metoda EntityDetectionCallback můžete najít v jednotce C:\<installedpath > \src\demos\tutorialSessionCallbacks.ts souboru.

![](../media/tutorial10_callbackcode.PNG)

Tato funkce volána po entity řešení došlo k chybě.
 
- První věc, které budou provádět, je vymazat $CityUknown. $CityUknown se zachová jenom pro jeden zapnout, jako jeho získá na začátku vždy zaškrtnuto.
- Potom Získejte seznam měst, které byly rozpoznány. Provést první z nich a pokusí se ho vyřešit.
- Funkce, který se přeloží ho (resolveCity) je definována další výše v kódu. Obsahuje seznam názvů canonical město. Najde v seznamu název města, vrátí jej. Jinak hledá v "cityMap" a vrátí mapované název. Pokud ji nemůžete najít Město, vrátí hodnotu null.
- Nakonec pokud město má přeložit na název, uložíme ho $CityKnown entity. Jinak zrušte, co uživatel má říká, že a naplnit $CityUknown entity.

### <a name="train-dialogs"></a>Trénování dialogová okna

1. Klikněte na dialogová okna trénování, pak nové dialogové okno trénování.
2. Zadejte "hello".
3. Klikněte na výsledek akce a vyberte "Město, který chcete?"
2. Zadejte "new york".
    - Text je rozpoznán jako město entity.
5. Klikněte na výsledek akce
    - `City` a `CityResolved` jsou vyplněna.
6. Vyberte "jsi $City a můžu vyřešit, který $CityResolved".
7. Klikněte na Hotovo výuky.

Přidáte jiný příklad – dialogové okno:

1. Kliknutím na dialogové okno Nový trénování.
2. Zadejte "hello".
3. Klikněte na výsledek akce a vyberte "Město, který chcete?"
2. Zadejte "velký objem apple".
    - Text je rozpoznán jako město entity.
5. Klikněte na výsledek akce
    - `CityResolved` Demonstruje účinek kód spuštěný.
6. Vyberte "jsi $City a můžu vyřešit, který $CityResolved".
7. Klikněte na Hotovo výuky.

Přidáte jiný příklad – dialogové okno:

1. Kliknutím na dialogové okno Nový trénování.
2. Zadejte "hello".
3. Klikněte na výsledek akce a vyberte "Město, který chcete?"
2. Zadejte "foo".
    - Toto je příklad, který systém nezná města. 
5. Klikněte na výsledek akce
6. Vyberte "Toto město $CityUknown nevím. Město, které si přejete? ".
7. Zadejte "new york".
8. Klikněte na výsledek akce.
    - `CityUknown` bylo vymazáno, a `CityResolved` naplnění.
6. Vyberte "jsi $City a můžu vyřešit, který $CityResolved".
7. Klikněte na Hotovo výuky.

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zpětná volání relace](./11-session-callbacks.md)

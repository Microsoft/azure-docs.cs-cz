---
title: Jak používat operace rozvětvení a vrácení zpět s modelem Conversation Learner Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Naučte se používat větvení a operace vrácení zpět s modelem Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: f4f3024451696dbd0244d9da39cba67b49447af1
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703623"
---
# <a name="how-to-use-branching-and-undo-operations"></a>Jak používat větvení a operace vrácení zpět
V tomto kurzu provedeme operace zrušení a větvení.


## <a name="details"></a>Podrobnosti
### <a name="undo"></a>Vrácení zpět
Umožňuje vývojářům vrátit zpět poslední vstup uživatele nebo volbu akce. Na pozadí ve skutečnosti "zpět" vytvoří nový dialog a přehraje ho až k předchozímu kroku.  To znamená, že zpětné volání detekce entit a volání rozhraní API v dialogovém okně budou volána znovu.

### <a name="branch"></a>Větev
Vytvoří nový vlakový dialog, který začíná stejným způsobem jako stávající dialogové okno s výukou. to znamená, že se změní úsilí ručního opětovného zadání dialogového okna. Na pozadí "větev" vytvoří nový dialog a přehraje stávající dialog vlaků až do vybraného kroku.  To znamená, že zpětné volání detekce entit a volání rozhraní API v dialogovém okně budou volána znovu.


## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby byl spuštěn robot, který přijímá Pizza objednávky:

    npm run demo-pizza

### <a name="open-or-import-the-demo"></a>Otevření nebo import ukázky

Pokud jste už pracovali v kurzu řazení Pizza a pak jednoduše otevřete tento model ze seznamu ve webovém uživatelském rozhraní. Jinak budete muset kliknout na "zkušební kurzy pro import" a vybrat model s názvem "demo-PizzaOrder".

## <a name="undo"></a>Vrácení zpět

Tady je příklad, jak zobrazit `Undo` funkci v akci:

### <a name="training-dialogs"></a>Dialogová okna pro školení
1. Na levém panelu klikněte na možnost výuka dialogových oken a potom klikněte `New Train Dialog` na tlačítko.
2. Zadejte příkaz ORDER by Pizza.
3. Klikněte na tlačítko `Score Actions`.
4. Kliknutím vyberte "co byste chtěli v pizza?"
5. Zadejte "cokoli".
6. Klikněte na tlačítko `Undo`.
    - Poslední položka je odebrána, takže poslední reakce na robot "co byste chtěli mít na vašem pizza?"

## <a name="branch"></a>Větev

V této ukázce otevřeme existující dialogové okno s výukou a vytvoříme z něj nové výukové dialogové okno pomocí větvení.

1. Na levém panelu klikněte na možnost výuka dialogových oken.
2. Všimněte si mřížky, měli byste vidět jenom jedno školení, které začíná na nové objednávce.
3. V mřížce klikněte na nové pořadí a otevřete dialogové okno existující vlak.
4. V dialogovém okně klikněte na poslední ne.
5. Klikněte na ikonu "větev", v tomto obrázku je červeně zakroužkovaná:
    - ![](../media/tutorial15_branch.PNG)
    - Celý dialog vlak před "ne" se zkopíruje do nového výukového dialogového okna.
    - Tím se ušetříte tím, že znovu zadáte novou konverzaci "větev" z tohoto bodu.
6. Zadejte "Ano", stiskněte klávesu ENTER.
7. Klikněte na tlačítko `Score Actions`.
    - V tomto okamžiku robot automaticky vybere odpověď, ale nelíbí se vám odpověď, abychom ji změnili.
8. Klikněte na poslední odpověď robota.
    - To nám umožní vybrat jinou odpověď.
9. Vyberte "UseLastToppings".
10. Klikněte na tlačítko `Score Actions`.
    - Znovu automaticky vybere robota odpověď. Mělo by se jednat o "uzení, sýry a žampiony na Pizza.". 
    - Tentokrát se mi líbí odpověď, abychom ji zachovali.
11. Klikněte na tlačítko `Score Actions`.
    - Zase robot automaticky vybere odpověď, měla by to vypadat nějak takto: "Přejete si něco jiného?"
12. Zadejte "ne".
13. Klikněte na tlačítko `Save Branch`.
14. Všimněte si, že mřížka teď obsahuje dvě školení, která začínají "novým pořadím".
    - Jedna z nich je ta, kterou jste použili k rozvětvení.
    - A druhá verze je větev, kterou jste právě uložili.
    - Kliknutím na každé z nich ověřte tyto očekávání.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Správa verzí a označování](./18-version-tag.md)

---
title: Jak používat větvení a zrušení operací s modelem konverzace Learner – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Zjistěte, jak používat větvení a zrušení operací s modelem Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: cca7b42c83be3ca428509ea48c387a1c35ddb35a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55206726"
---
# <a name="how-to-use-branching-and-undo-operations"></a>Jak používat větvení a operace vrátit zpět
V tomto kurzu jsme přejděte zpět a větvení operace.


## <a name="details"></a>Podrobnosti
### <a name="undo"></a>Zpět
Umožňuje vývojářům "zpět" poslední možnost vstup nebo akce uživatele. Na pozadí "zpět" ve skutečnosti vytváří nové dialogové okno a přehrává až v předchozím kroku.  To znamená, že entita detekce zpětného volání a rozhraní API volá v dialogovém okně opět bude volána.

### <a name="branch"></a>Branch
Vytvoří nový dialog trénovat na základě modelu, který začíná stejným způsobem jako existující trénování dialogové okno – tato akce uloží snaha o ručně znovu zadávat dialogové okno se změní. Na pozadí "větve" vytvoří nové dialogové okno a přehrává existující dialogu trénování až vybraný krok.  To znamená, že entita detekce zpětného volání a rozhraní API volá v dialogovém okně opět bude volána.


## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna robotů, která přebírá pizza objednávky:

    npm run demo-pizza

### <a name="open-or-import-the-demo"></a>Otevření nebo importovat ukázku

Pokud jste už pracovali v průběhu kurzu pořadí pizza, jednoduše spusťte tohoto modelu ze seznamu ve webovém uživatelském rozhraní. V opačném případě bude nutné klikněte na "Import kurzy" a vyberte model s názvem "Demo-PizzaOrder".

## <a name="undo"></a>Zpět

Tady je příklad toho, jak zobrazit `Undo` funkce v akci:

### <a name="training-dialogs"></a>Dialogová okna školení
1. Na levém panelu klikněte na tlačítko "Dialogů Train" a klikněte `New Train Dialog` tlačítko.
2. Zadejte "Order pizza".
3. Klikněte na tlačítko `Score Actions`.
4. Kliknutím vyberte "Co jste chtěli na váš pizza?"
5. Zadejte "nic".
6. Klikněte na tlačítko `Undo`.
    - Je odebrán poslední položky, a poslední odpovědi robotů "Co jste chtěli na váš pizza?"

## <a name="branch"></a>Branch

Pro tuto ukázku vytvoříme otevřete existující dialogu trénování a vytvořit nové dialogové okno vlak z něj rozvětvováním.

1. Na levém panelu klikněte na tlačítko "Dialogů Train".
2. Všimněte si, že mřížky, měli byste vidět jenom jeden školení, která začíná na "Nová objednávka".
3. V mřížce, klikněte na možnost "Nová objednávka" otevřete dialogové okno existující trénování.
4. Klikněte na poslední "Ne" v šabloně dialogu.
5. Klikněte na ikonu "Větve", je v kroužku červeně na tomto obrázku:
    - ![](../media/tutorial15_branch.PNG)
    - Celý Dialog trénování před "Ne" zkopírována do nové dialogové okno trénování.
    - Tím se ušetří, který vám znovu zadávat předchozí se změní prozkoumejte novou konverzaci "větev" od této chvíle.
6. Zadejte "Ano", stiskněte enter.
7. Klikněte na tlačítko `Score Actions`.
    - V tomto okamžiku robota automaticky vybere odpověď, ale můžeme nelíbí odpovědi, tedy budeme ho změnit.
8. Klikněte na poslední odpovědi robotů.
    - Dejte nám to vybere jinou odpověď.
9. Vyberte "UseLastToppings".
10. Klikněte na tlačítko `Score Actions`.
    - Znovu robota automaticky vybere odpověď. Ji by měl říct "Máte salám a produkci minipivovarů ve wisconsinu hub ve vašich pizza.". 
    - Tentokrát jsme proto budeme udržovat jej jako odpověď.
11. Klikněte na tlačítko `Score Actions`.
    - Znovu robota automaticky vybere odpověď, by mělo být uvedeno, "Chcete cokoli?"
12. Zadejte "žádný".
13. Klikněte na tlačítko `Save Branch`.
14. Všimněte si, že mřížka teď obsahuje dva školení, které začíná "Nová objednávka".
    - Jeden z nich je ten, který jste použili k větví sady.
    - A druhý je větvenou verze, kterou jste právě uložili.
    - Klikněte na každý z nich k ověření těchto očekávání.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Správa verzí a označování](./18-version-tag.md)

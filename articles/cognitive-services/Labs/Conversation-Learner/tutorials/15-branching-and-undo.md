---
title: Jak používat větvení a zrušení operací s modelem konverzace Learner – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Zjistěte, jak používat větvení a zrušení operací s modelem Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 05140693026e21a73b756ed0ea7bc9936bef067e
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173294"
---
# <a name="how-to-use-branching-and-undo-operations"></a>Jak používat větvení a operace vrácení zpět
V tomto kurzu jsme přejděte zpět a větvení operace.


## <a name="details"></a>Podrobnosti
- Vrácení zpět: umožňuje vývojářům "zpět" možnost vstup nebo akce uživatele. Na pozadí "zpět" ve skutečnosti vytváří nové dialogové okno a znovu hraje až v předchozím kroku.  To znamená, že entita detekce zpětného volání a rozhraní API volá v dialogovém okně opět bude volána.

- Větev: vytvoří nový dialog trénování, který začíná stejným způsobem jako existující trénování dialogové okno – tato akce uloží snaha o ručně znovu zadávat dialogové okno se změní. Na pozadí "větve" vytvoří nové dialogové okno a znovu hraje existující dialogu trénování až vybraný krok.  To znamená, že entita detekce zpětného volání a rozhraní API volá v dialogovém okně opět bude volána.


## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna bot pizza pořadí:

    npm run demo-pizza

### <a name="open-the-demo"></a>Otevřete ukázku

V seznamu modelu ve webovém uživatelském rozhraní kliknutím na TutorialDemo Pizza pořadí. 

Podrobnosti o ukázku Pizza pořadí najdete v kurzu Pizza pořadí.

## <a name="undo"></a>Zpět

Budeme součástí dialogu vrátit zpět a znovu jej vytvořte z tohoto kroku.

### <a name="training-dialogs"></a>Dialogová okna školení
Začněme celodenního školení. 

1. Klikněte na dialogová okna trénování, pak nové dialogové okno trénování.
1. Zadejte "pořadí pizza".
2. Klikněte na akci skóre.
3. Kliknutím vyberte "co chcete na vaše pizza?"
4. Zadejte "hub a produkci minipivovarů ve wisconsinu".
5. Klikněte na výsledek akce.
3. Kliknutím vyberte "máte $Toppings na vaše pizza".
6. Vyberte možnost "Chcete cokoli?"
7. Zadejte "odeberte hub a přidejte papriky".
    - Vyberte hub a zrušte zaškrtnutí políčka Toppings entity. Vytváříme akci, která se vrátíme zpět.
2. Klikněte na krok zpět.
    - Poslední položka je odebrána, a My jsme na "Chcete cokoli?"  (snímek)
2. Zadejte "odeberte hub a přidejte papriky".
8. Kliknutím vyberte "máte $Toppings na vaše pizza.
    - Ujistěte se, že jsou obě entity vybraný správně.
2. Klikněte na akci skóre. Můžete pokračovat v dialogovém okně opravený teď.
4. Klikněte na Hotovo výuky.

Nyní jste viděli použití zpět k odebrání vstupu uživatele a akce.

![](../media/tutorial15_undo.PNG)

## <a name="branch"></a>Větev

Jako příklad můžeme otevřete existující dialogu trénování a vytvořte další dialog trénování rozvětvováním.

1. Klikněte na tlačítko trénování dialogová okna a potom "Nová objednávka" otevřete existující dialogové okno. 
2. Klikněte na poslední 'ne' v dialogovém okně (viz následující snímek obrazovky).
3. Klikněte na větev.
    - "žádný" odebrána a celý dialog až k danému bodu je zkopírována do nové. 
    - Tím se ušetří, který vám znovu zadávat předchozí displeje prozkoumat nové "větve" od této chvíle.
1. Zadejte "yes".
2. Klikněte na akci skóre.
3. Vyberte "Máte $Toppings na vaše pizza".
6. Vyberte možnost "Chcete cokoli?"
7. Zadejte "žádný".
4. Klikněte na Hotovo výuky.

![](../media/tutorial15_branch.PNG)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Správa verzí a označování](./16-versioning-and-tagging.md)

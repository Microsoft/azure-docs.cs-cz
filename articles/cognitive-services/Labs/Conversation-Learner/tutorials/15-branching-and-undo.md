---
title: Jak používat vytvoření větve a vrátit zpět operace s aplikací konverzace student - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Naučte se používat vytvoření větve a vrátit zpět operace s aplikací student konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 724a9e47267e0bd7417130efe54c609ac7a465fb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343248"
---
# <a name="how-to-use-branching-and-undo-operations"></a>Operace vrácení zpět a jak používat vytvoření větve
V tomto kurzu jsme budou přenášeny po vrácení zpět a větvení operace.

## <a name="details"></a>Podrobnosti
- Vrácení zpět: umožňuje vývojáři "zrušit" volba vstup nebo akce uživatele. Na pozadí "zrušit" ve skutečnosti vytvoří nové dialogové okno a znovu ji hraje až v předchozím kroku.  To znamená, že detekce entity zpětného volání a rozhraní API, zavolá v dialogovém okně opět bude volána.

- Pobočky: vytvoří nový train dialog, který začíná stejným způsobem jako existující cvičení dialogové okno – to umožňuje ušetřit úsilí změní ručně znovu zadávat dialogové okno. Na pozadí "větve" vytvoří nové dialogové okno a znovu hraje dialogu existující train až vybraný krok.  To znamená, že detekce entity zpětného volání a rozhraní API, zavolá v dialogovém okně opět bude volána.


## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby robota pořadí pizza běží:

    npm run demo-pizza

### <a name="open-the-demo"></a>Otevřete ukázku

V seznamu aplikací webového uživatelského rozhraní klikněte na TutorialDemo Pizza pořadí. 

Podrobnosti o ukázku Pizza pořadí v tématu kurzu Pizza pořadí.

## <a name="undo"></a>Zpět

Jsme zruší součástí dialogové okno a znovu ji vytvořte z tohoto kroku.

### <a name="training-dialogs"></a>Školení dialogová okna
Začněme relaci školení. 

1. Klikněte na tlačítko Train dialogová okna, dialogové okno pak nový vlaku.
1. Zadejte, řazení pizza'.
2. Klikněte na tlačítko akce skóre.
3. Kliknutím vyberte, co chcete použít na vaše pizza?"
4. Zadejte "hub a sýr".
5. Klikněte na tlačítko akce skóre.
3. Kliknutím vyberte 'máte $Toppings na vaše pizza'.
6. Vyberte, chcete cokoliv jiného?"
7. Zadejte "Odebrat hub a přidat zeleninová".
    - Vyberte hub a zrušte zaškrtnutí Toppings entity. Vytváříme akci, která jsme se vrátit zpět.
2. Klikněte na tlačítko Zpět krok.
    - Upozorňujeme, že poslední položka je odebrána a jsme se vrátíte do 'chcete cokoliv jiného?"  (následující snímek obrazovky)
2. Zadejte "Odebrat hub a přidat zeleninová".
8. Kliknutím vyberte "máte $Toppings na vaše pizza.
    - Zkontrolujte, zda že jsou obě entity vybrané správně.
2. Klikněte na tlačítko akce skóre. Můžete pokračovat v dialogu opravené teď.
4. Klikněte na tlačítko Hotovo vyučující.

Jste nyní viděli, jak používat zpět, chcete-li odebrat vstup uživatele a akce.

![](../media/tutorial15_undo.PNG)

## <a name="branch"></a>Větev

Například můžeme otevřete dialogovým existující train a vytvořte další dialog train rozvětvováním.

1. Klikněte na tlačítko Train v dialogových oknech, pak 'nové pořadí, otevřete dialogové okno existující. 
2. Klikněte na poslední "Ne" v dialogovém okně (viz obrázek).
3. Klikněte na větev.
    - Upozorňujeme, že "žádný" odebere a dialogu celý až tento bod se zkopíruje do nové. 
    - Tím ušetříte, které můžete znovu zadávat předchozím změní prozkoumat nové "větve" z tohoto bodu.
1. Zadejte "Ano".
2. Klikněte na tlačítko akce skóre.
3. Vyberte, máte $Toppings na vaše pizza'.
6. Vyberte, chcete cokoliv jiného?"
7. Zadejte "žádné".
4. Klikněte na tlačítko Hotovo vyučující.

![](../media/tutorial15_branch.PNG)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Správa verzí a označování](./16-versioning-and-tagging.md)

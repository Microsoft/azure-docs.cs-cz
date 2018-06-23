---
title: Ukázka aplikace student konverzace, resetování hesla - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Informace o vytvoření ukázkové aplikace student konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 24d61787a79ee1a1a9737c417aa966cc8fd75930
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343235"
---
# <a name="demo-password-reset"></a>Ukázka: Resetování hesla
Tato ukázka znázorňuje robota jednoduché technické podpory, které vám pomohou při resetování hesla. 

Ukazuje, jak student konverzace další netriviální dialogu toky, pořadí více zapnout, včetně třídu na více systémů domény. Tato ukázka nepoužívá kód ani entity.

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna robota resetování hesla

    npm run demo-password

### <a name="open-the-demo"></a>Otevřete ukázku

V seznamu aplikací webového uživatelského rozhraní klikněte na kurz ukázku pro resetování hesla. 

### <a name="actions"></a>Akce

Vytvořili jsme sadu akcí, kde je uživatel hledá pomoc s své heslo, včetně řešení.

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>Školení dialogová okna

Existuje několik dialogů, školení. Existují také předvádění out třídy domény – například požadavků uživatele jako "řídí pokynů, jsou mimo doménu. robota udělil příklady pár mimo požadavky domény a může odpovědět, nelze zvýšit s třídou."

![](../media/tutorial_pw_reset_entities.PNG)

Jako příklad Zkusme vyučující relace.

1. Klikněte na tlačítko Train dialogová okna, dialogové okno pak nový vlaku.
1. Zadejte 'I ztráty hesla'.
2. Klikněte na tlačítko akce skóre.
3. Kliknutím vyberte "Je, že pro místní účet nebo účet Microsoft?"
4. Zadejte 'Místní účet'.
5. Klikněte na tlačítko akce skóre.
3. Kliknutím vyberte, kterou verzi Windows máte?"
4. Zadejte, Windows 8'.
5. Klikněte na tlačítko akce skóre.
6. Vyberte ' řešení: jak resetovat heslo v systému Windows 8. "
4. Klikněte na tlačítko Hotovo vyučující.

Nyní si vyzkoušíte, jak robota další třídu na více systémů domény.

1. Klikněte na tlačítko Train dialogová okna, dialogové okno pak nový vlaku.
1. Zadejte, webové vyhledávání'.
    - Jedná se o příklad třídy na více systémů domény. 
2. Klikněte na tlačítko akce skóre.
3. Kliknutím vyberte "Bohužel nelze zvýšit s třídou."
    - Všimněte si, že je nyní nízká skóre pro tuto možnost. Ale po trochu další vyučující získají skóre vyšší.
4. Klikněte na tlačítko Hotovo vyučující.

Viděli jste nyní, jak vytvořit základní technickou podporu ukázku a jak můžete naučit, abyste poskytovat řešení a taky zpracovat mimo ukázkové dotazy.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Demo - pizza pořadí](./demo-pizza-order.md)

---
title: Jak používat počkejte a bez čekání akce s aplikací konverzace student - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Další informace o použití počkejte a bez čekání akce s aplikací student konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: bb2cbd55b6c8be51213095926bb592169613d1fc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343219"
---
# <a name="wait-and-non-wait-actions"></a>Počkejte a bez čekání akce

Tento kurz ukazuje rozdíl mezi čekání akce a akce bez čekání v student konverzace.

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby obecné kurz robota běží

    npm run tutorial-general

## <a name="details"></a>Podrobnosti

- Počkejte akce: po systému používá akci "Čekání", bude zastavit provádění akcí a čekání na vstup uživatele.
- Akce bez čekání: po systému používá akci "bez čekání", ji budou okamžitě zvolte další akci (bez čekání na uživatele inpu nejprve).

## <a name="steps"></a>Kroky

### <a name="create-a-new-app"></a>Vytvoření nové aplikace

1. Ve webovém uživatelském rozhraní klikněte na nové aplikace
2. Do pole Název zadejte WaitNonWait. Klikněte na tlačítko vytvořit.

### <a name="create-the-first-wait-action"></a>Vytvoření první akce čekání

1. Klikněte na tlačítko akce, pak nová akce.
2. V odpovědi zadejte, které zvíře chcete?".
    - Toto je čekání akce, takže políčko nechte čekání zaškrtnutým políčkem odpovědi.
3. Klepněte na Hotovo.

### <a name="create-a-non-wait-action"></a>Vytvoří akci bez čekání

1. Klikněte na tlačítko Nová akce
2. V odpovědi zadejte 'Krav vyslovení mú'.
3. Zrušte zaškrtnutí čekání zaškrtávací políčko odpovědi.
4. Kliknutí na Vytvořit

### <a name="create-a-second-non-wait-action"></a>Vytvoří druhou akci bez čekání

1. Klikněte na tlačítko Nová akce
2. V odpovědi zadejte 'Kachny vyslovení quack'.
3. Zrušte zaškrtnutí čekání zaškrtávací políčko odpovědi.
4. Kliknutí na Vytvořit

![](../media/tutorial2_actions.PNG)

### <a name="train-the-bot"></a>Cvičení robota

1. Klikněte na tlačítko Train dialogová okna, dialogové okno pak nový vlaku.
2. Typ hello
3. Klikněte na tlačítko akce skóre a vyberte, které zvíře chcete?".
4. Zadejte "mléka.
5. Klikněte na tlačítko akce skóre a vyberte 'Krav vyslovení mú'.
    - Upozorňujeme, že robota nebudou čekat na vstupu a bude trvat další akce.
2. Vyberte, které zvíře chcete?".
3. Zadejte "kachní.
5. Klikněte na tlačítko akce skóre a vyberte 'Kachny vyslovení quack'.

![](../media/tutorial2_dialogs.PNG)

Poznámka: pořadí odpovědí robota s ohledem na počkejte a bez čekání akce.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Úvod do entity](./3-introduction-to-entities.md)

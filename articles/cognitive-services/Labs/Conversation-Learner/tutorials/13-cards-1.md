---
title: Jak použít karty s aplikací student konverzace, část 1 - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Další informace o použití karty s aplikací student konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e90ccd42b21eea6139c402937be7e20513d73c84
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343231"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Postup použití karty (část 1 / 2)

Tento kurz ukazuje, jak přidat a používat jednoduché karty ve vaší robota.

Všimněte si, že konverzace student očekává, že soubory karty definice umístěný v adresář s názvem "znaky", který se nachází v adresáři, kde je spuštěna robota.

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby obecné kurz robota běží

    npm run tutorial-general

## <a name="details"></a>Podrobnosti

Karty jsou prvky uživatelského rozhraní, které umožňují uživateli vybrat možnost v konverzaci. 

### <a name="open-the-demo"></a>Otevřete ukázku

V seznamu aplikací webového uživatelského rozhraní klikněte na kurz 13 karty 1. 

### <a name="the-card"></a>Karta

Karta definice je v následujícím umístění: C:\<installedpath\>\src\cards\prompt.json.

Systém očekává definic karty v tomto adresáři karty.

![](../media/tutorial13_prompt.PNG)

- Poznámka: TextBlock a šablony otázku.
- Existují dva odeslání tlačítka a text, který získá odeslané pro každou.

### <a name="actions"></a>Akce

Vytvořili jsme tři akce. Jak vidíte níže, je první akcí karta.

![](../media/tutorial13_actions.PNG)

Podívejme se, jak byl vytvořen typ akce karty:

![](../media/tutorial13_cardaction.PNG)

Poznámka: vstup otázku a tlačítka 1 a 2. Ty jsou šablony odkazy na kartě, kde zadáte na otázku a příslušných odpovědí. Můžete taky odkazovat a použít entit nebo s kombinaci textu a entity.

Ikona oka ukazuje, jak vypadá na kartu.

### <a name="train-dialog"></a>Dialogové okno Train

Projděme vyučující dialogové okno.

1. Klikněte na tlačítko Train dialogová okna, dialogové okno pak nový vlaku.
1. Zadejte "hi".
2. Klikněte na tlačítko akce skóre.
3. Kliknutím vyberte 'Výzva přejděte doleva nebo doprava'.
    - Kliknutím na tlačítko 'left' nebo 'right' je stejné, jako uživatel zadání 'left' nebo 'práva, v uvedeném pořadí. 
4. Klikněte na tlačítko akce skóre.
4. Kliknutím vyberte 'left'. Toto je akce bez čekání.
6. Kliknutím vyberte 'Výzva přejděte doleva nebo doprava'.
4. Klikněte na tlačítko "vpravo".
5. Klikněte na tlačítko akce skóre.
3. Kliknutím vyberte 'Right'
6. Kliknutím vyberte 'Výzva přejděte doleva nebo doprava'.
4. Klikněte na tlačítko Hotovo testování.

Nyní jste mohli vidět, jak fungují karty. V adresáři karty jsou definovány jako šablony json. V uživatelském rozhraní, které lze načíst pomocí řetězce nebo entitu nebo kombinaci obou bude surface šablony.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Karty část 2](./14-cards-2.md)

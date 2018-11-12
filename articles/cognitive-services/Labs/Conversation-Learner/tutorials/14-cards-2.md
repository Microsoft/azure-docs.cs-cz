---
title: Použití karty s modelem Learner konverzace, část 2 – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Další informace o použití karty s modelem Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 99c474978d4da1d80669505330b2dc6220d7ca5f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51226630"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Použití karty (část 1 ze 2)
Tento kurz ukazuje, jak přidat kartu polí formuláře do svého robota. Se zobrazí, jak přesunout polí formuláře do entity.

Konverzace Learner očekává, že vaše karta definiční soubory umístěné v adresáři s názvem "karty", který se nachází v adresáři začínali robota.

## <a name="video"></a>Video

[![Kurz 14 ve verzi Preview](https://aka.ms/cl-tutorial-14-preview)](https://aka.ms/blis-tutorial-14)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna obecné kurz bot

    npm run tutorial-general

## <a name="details"></a>Podrobnosti

Karty jsou prvky uživatelského rozhraní, které umožní uživateli vybrat možnost v konverzaci. 

### <a name="open-the-demo"></a>Otevřete ukázku

V seznamu modelu ve webovém uživatelském rozhraní kliknutím na kurz-14karty-2. 

### <a name="the-card"></a>Karta

Karta definice je v následujícím umístění: C:\<installedpath\>\src\cards\shippingAddress.json.

Tato karta shromažďuje tři pole příkazu dodací adresu: city, ulice a stavu.

![](../media/tutorial14_card.PNG)

### <a name="actions"></a>Akce

Vytvořili jsme tři akce. Jak vidíte níže, je první akcí karta.

![](../media/tutorial14_actions.PNG)

Podívejme se, jak byl vytvořen typ akce karty:

- Všimněte si, že ulice, ve kterém je typ Input.text a jejího ID.
- Podobně je adresu, Město a rozevírací seznam s ID nástroje-stav adresy.

ID jsou důležité, protože když pole se vyplní a odeslali, jsou názvy entit, které se zobrazí tyto hodnoty v robota.

## <a name="entities"></a>Entity
Definovali jsme tři entity odpovídající kartě, jako jsme viděli výše.

![](../media/tutorial14_entities.PNG)

## <a name="actions"></a>Akce

Definovali jsme dvě akce.

![](../media/tutorial14_actions.PNG)

- První je přenosů adresa kartu, kde typ akce je karta a vybrat šablonu z rozevíracího seznamu jako shippingAddress.
- Druhým je jednoduché akce číst zpět dodací adresu.

![](../media/tutorial14_sa_card.PNG)

### <a name="train-dialog"></a>Dialogové okno trénování

Projděme si dialogové okno výuky.

1. Klikněte na dialogová okna trénování, pak nové dialogové okno trénování.
1. Zadejte "hi".
2. Klikněte na akci skóre.
3. Kliknutím vyberte "Dodací adresa".
4. Zadejte karty a odeslat.
    - Všimněte si, že tyto hodnoty nyní byly přesunuty do paměti entity. Žádná analýza kódu je potřeba, protože formuláři již dělené vstupy.
5. Klikněte na výsledek akce.
3. Kliknutím vyberte ' na cestě k $Address... ".
4. Klikněte na tlačítko Hotovo testování.

![](../media/tutorial14_train_dialog.PNG)

Můžete teď viděli, jak získat hodnoty z karty, která má polí k vyplnění a rozevírací seznamy, a zachytit a shromažďovat v entitách robota.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Větvení a vrácení zpět](./15-branching-and-undo.md)

---
title: Použití karty s modelem Learner konverzace, část 1 – Microsoft Cognitive Services | Dokumentace Microsoftu
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
ms.openlocfilehash: da261beeec4f02dfa7c7cf9071e51dc17cf5c7cd
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51254379"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Použití karty (část 1 ze 2)

Tento kurz ukazuje, jak přidat a používat jednoduché karty svého robota.

> [!NOTE]
> Aktuálně konverzace Learner očekává, že vaše karta definiční soubory umístěné v adresáři s názvem "karty", který se nachází v adresáři začínali robota. Budeme to konfigurovat v budoucnu.

## <a name="video"></a>Video

[![Kurz 13 ve verzi Preview](https://aka.ms/cl-tutorial-13-preview)](https://aka.ms/blis-tutorial-13)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna obecné kurz bot

    npm run tutorial-general

## <a name="details"></a>Podrobnosti

Karty jsou prvky uživatelského rozhraní, které umožní uživateli vybrat možnost v konverzaci. 

### <a name="open-the-demo"></a>Otevřete ukázku

V seznamu modelu ve webovém uživatelském rozhraní kliknutím na kurz 13 karty 1. 

### <a name="the-card"></a>Karta

Karta definice je v následujícím umístění: C:\<installedpath\>\src\cards\prompt.json.

Systém se očekává, že vaše definice karta najít v tomto adresáři karty.

![](../media/tutorial13_prompt.PNG)

> [!NOTE]
> Všimněte si, že typ těla zprávy `TextBlock` a `{{question}}` zástupný symbol v textovém poli.
> Existují dvě odeslání tlačítka a text, který získá odeslané pro každý.

### <a name="actions"></a>Akce

Vytvořili jsme tři akce. Jak vidíte níže, je první akcí karta.

![](../media/tutorial13_actions.PNG)

Podívejme se, jak byl vytvořen typ akce karty:

![](../media/tutorial13_cardaction.PNG)

> [!NOTE]
> Vstupní dotaz a tlačítka 1 a 2. To jsou odkazy na šablony v kartě, kde můžete zadat otázku a příslušných odpovědí. Lze také odkazovat a používat entity nebo kombinaci textu a entity.

Na ikonu oka se dozvíte, jak vypadá karta.

### <a name="train-dialog"></a>Dialogové okno trénování

Projděme si dialogové okno výuky.

1. Klikněte na dialogová okna trénování, pak nové dialogové okno trénování.
1. Zadejte "hi".
2. Klikněte na akci skóre.
3. Kliknutím vyberte "Prompt Přejít doleva nebo doprava".
    - Klepnutím na tlačítko 'left' nebo 'right' je uživatel zadat text 'left' nebo 'přímo v uvedeném pořadí. 
4. Klikněte na výsledek akce.
4. Kliknutím vyberte 'left'. Toto je akce bez čekání.
6. Kliknutím vyberte "Prompt Přejít doleva nebo doprava".
4. Klikněte na tlačítko "vpravo".
5. Klikněte na výsledek akce.
3. Kliknutím vyberte 'Right'
6. Kliknutím vyberte "Prompt Přejít doleva nebo doprava".
4. Klikněte na tlačítko Hotovo testování.

Nyní jste viděli, jak karty fungují. V adresáři karet jsou definovány jako šablony json. Šablony, bude přinášet v uživatelském rozhraní, která můžete naplnit pomocí řetězce nebo entity nebo kombinaci obou.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [2. část karty](./14-cards-2.md)

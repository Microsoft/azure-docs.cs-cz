---
title: Postup použití entity s aplikací konverzace student - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Další informace o použití entity s aplikací student konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 85df31c2e2ff3ca81698921a1f17f415daefb6c5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343238"
---
# <a name="introduction-to-entities"></a>Úvod do entity

Tento kurz představuje entity a ukazuje způsob použití pole "Disqualifying entity" a "Vyžadována entity" v akce.

## <a name="requirements"></a>Požadavky

Tento kurz vyžaduje, aby obecné kurz robota běží

    npm run tutorial-general

## <a name="details"></a>Podrobnosti

Tento kurz ukazuje dvě běžné používá pro entity.  Entity nejprve, můžete extrahovat dílčích řetězců z uživatele zprávy, jako je identifikace Město v "co je počasí v Praze?".  Entity druhý, můžete omezit, když akce, které jsou k dispozici.  Akce můžete konkrétně seznamu entity jako nich "vyžaduje" nebo "vyřazením":
- Požadované entity akce musí být obsažená v paměti robota v pořadí pro akce, která má být k dispozici
- Vyřazení entit musí *není* nacházet v paměti robota v pořadí pro akce, která má být k dispozici

Ostatní kurzy zahrnovat další aspekty entitami, jako je například předdefinovaných entity, s více hodnotami a negatable entity, programová entity a manipulace s těmito entity v kódu.

## <a name="steps"></a>Kroky

### <a name="create-the-application"></a>Vytvoření aplikace

1. Ve webovém uživatelském rozhraní klikněte na nové aplikace
2. Do pole Název zadejte IntroToEntities. Klikněte na tlačítko vytvořit.

### <a name="create-entity"></a>Vytvořit entitu

1. Klikněte na tlačítko entity, pak novou entitu.
2. Název Entity zadejte města.
3. Kliknutí na Vytvořit

Poznámka: typ entity je "vlastní" – to znamená, že entita může cvičení.  Existují také předem připravené entity, což znamená, že jejich chování nemůže být upravena – to jsou popsané v jiné kurzu.

### <a name="create-two-actions"></a>Vytvořte dvě akce

1. Klikněte na tlačítko akce, pak nová akce
2. V odpovědi zadejte 'Neznámého jakém městě chcete'.
3. Vyřazení entity zadejte $city. Klikněte na tlačítko Uložit.
    - To znamená, že pokud tato entita je definována v robota na paměti, pak tato akce se *není* být k dispozici.
2. Klikněte na tlačítko akce pak nová akce vytvoří druhou akci.
3. V odpovědi zadejte, počasí v $city je pravděpodobně slunečného'.
4. V požadované entity Všimněte si, že entita města byl přidán automaticky vzhledem k tomu, že byl předán.
5. Kliknutí na Uložit

Nyní máte dvě akce.

![](../media/tutorial3_actions.PNG)

### <a name="train-the-bot"></a>Cvičení robota

1. Klikněte na tlačítko Train dialogová okna, dialogové okno pak nový vlaku.
2. Zadejte text "hello".
3. Klikněte na tlačítko akce skóre a vyberte "Neznámého jakém městě chcete?"
    - Všimněte si, že odpověď, kdy je potřeba města entity nelze vybrat, protože města entity není definován v paměti na robota.
2. Vyberte 'Neznámého jakém městě chcete'.
4. Zadejte "seattle". Zvýrazněte Praha a pak klikněte na města.
5. Klikněte na tlačítko akce skóre
    - Poznámka: hodnota města je teď v paměti robota.
    - 'Je pravděpodobně slunečného počasí v $city' je nyní k dispozici jako odpověď. 
6. Vyberte, počasí v $city je pravděpodobně slunečného'.

Řekněme, že uživatel zadá 'opakujte který'. 
1. Zadejte a zadejte. Upozorňujeme, že entitu města a jeho hodnota se v paměti a k dispozici.
2. Vyberte, počasí v $city je pravděpodobně slunečného'.

![](../media/tutorial3_entities.PNG)

Nyní jste vytvořili entity a s názvem bez přípony instancí ve zprávách uživatele.  Přítomnosti nebo absenci entity jste použili také v paměti robota na ovládací prvek, když jsou k dispozici prostřednictvím akce vyřazení a polí entit požadované akce.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Očekávaný entity](./4-expected-entity.md)

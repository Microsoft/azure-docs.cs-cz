---
title: Postup přidání předdefinovaných entity do aplikace konverzace student – kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Naučte se přidávat do aplikace student konverzace předem vytvořené entity.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f014464419bfac39a9e57e679fcd28a737e9ebdb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343600"
---
# <a name="how-to-add-pre-built-entities"></a>Postup přidání předdefinovaných entity
Tento kurz ukazuje, jak přidávat do aplikace konverzace student "předem vytvořené" entity.

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby obecné kurz robota běží

    npm run tutorial-general

## <a name="details"></a>Podrobnosti

Předem vytvořené entity rozpoznat běžné typy entit, jako je například čísel, kalendářních dat, peněžní objemy a dalších.  Na rozdíl od vlastní entity budou fungovat "out-of-the-box" a nevyžadují žádné školení.  Na rozdíl od vlastní entity nelze změnit jejich chování.  Ve výchozím nastavení předdefinovaných entity jsou více hodnot – to znamená robota paměti budou se hromadit všechny zjištěné instance entity.

## <a name="steps"></a>Kroky

### <a name="create-the-application"></a>Vytvoření aplikace

1. Ve webovém uživatelském rozhraní klikněte na nové aplikace
2. Do pole Název zadejte BuiltInEntities. Klikněte na tlačítko vytvořit.

### <a name="create-an-entity"></a>Vytvořte entitu

1. Klikněte na tlačítko entity, pak novou entitu.
2. Klikněte na rozevírací seznam EntityType a vyberte datetimev2.
    - Možnosti programovatelná a Negatable jsou zakázány, protože nevztahují před sestavením entity.
3. Klikněte na Vytvořit.

![](../media/tutorial7_entities.PNG)

### <a name="create-two-actions"></a>Vytvořte dvě akce

1. Klikněte na tlačítko akce, pak nová akce
2. V odpovědi, zadejte "datum je $luis-datetimev2'.
3. Klikněte na Vytvořit.

![](../media/tutorial7_actions.PNG)

Pak vytvořte druhou akci:

1. Klikněte na tlačítko akce pak nová akce vytvoří druhou akci.
3. V odpovědi zadejte, co je datum?".
4. Vyřazení entity zadejte 'Leoš datetimev2'.
4. Kliknutí na Vytvořit

![](../media/tutorial7_actions2.PNG)

Nyní máte dvě akce.

### <a name="train-the-bot"></a>Cvičení robota

1. Klikněte na tlačítko Train dialogová okna, dialogové okno pak nový vlaku.
2. Zadejte text "hello".
3. Klikněte na tlačítko akce skóre a vyberte, jaké je datum?"
2. Zadejte "dnes". 
    - Všimněte si ještě dnes se označí a objeví ve druhém řádku vzhledem k tomu, že je předem vytvořené entity a upravovat.
5. Klikněte na tlačítko akce skóre
    - Všimněte si, že datum nyní se zobrazí v části paměti Entity. 
    - Pokud jste myš přes data, se zobrazí další údaje poskytnuté LEOŠ, které mohou použít a smí uživatel manipulovat další v kódu. 
6. Vyberte ' datum je $luis-datetimev2'.
7. Klikněte na tlačítko Hotovo vyučující

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Alternativní vstupy](./8-alternative-inputs.md)

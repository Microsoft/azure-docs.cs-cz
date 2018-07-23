---
title: Postup přidání předem vytvořené entity do modelu konverzace Learner – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Zjistěte, jak přidat předem vytvořené entity do modelu Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 84d73add5586aaaf130253a8122a4152e39bcbe9
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171363"
---
# <a name="how-to-add-pre-built-entities"></a>Postup přidání předem připravených entit
Tento kurz ukazuje, jak přidat "předchystané" entity do modelu Learner konverzace.

## <a name="video"></a>Video

[![Kurz 7 Preview](http://aka.ms/cl-tutorial-07-preview)](http://aka.ms/blis-tutorial-07)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna obecné kurz bot

    npm run tutorial-general

## <a name="details"></a>Podrobnosti

Předem vytvořené entity rozpoznávání běžných typů entit, jako je například čísla, kalendářní data, peněžních částek a dalších.  Na rozdíl od vlastních entit jejich práce "out-of-the-box" a nevyžadují žádné školení.  Na rozdíl od vlastních entit nelze změnit jejich chování.  Ve výchozím nastavení, předem vytvořené entity jsou více Vážíme si toho – to znamená, bodu robotů také paměti shromáždí, všechny zjištěné instance entity.

## <a name="steps"></a>Kroky

### <a name="create-the-model"></a>Vytvoření modelu

1. V webového uživatelského rozhraní klikněte na nový Model
2. Do pole Název zadejte BuiltInEntities. Pak klikněte na tlačítko vytvořit.

### <a name="create-an-entity"></a>Vytvoření entity

1. Klikněte na entity a pak novou entitu.
2. Klikněte na typ entity, rozevírací seznam a vyberte datetimev2.
    - Programovatelná a Negatable možnosti jsou zakázány, protože se nevztahují před sestavením entity.
3. Klikněte na Vytvořit.

![](../media/tutorial7_entities.PNG)

### <a name="create-two-actions"></a>Vytvořte dvě akce

1. Klikněte na tlačítko akce a potom novou akci
2. V odpovědi, zadejte "datum je $luis-datetimev2".
3. Klikněte na Vytvořit.

![](../media/tutorial7_actions.PNG)

Vytvořte druhou akci:

1. Klikněte na tlačítko akce a pak novou akci na vytvoří druhou akci.
3. V odpovědi zadejte "Jaké je datum?".
4. Vyřazení entity zadejte "luis datetimev2".
4. Kliknutí na Vytvořit

![](../media/tutorial7_actions2.PNG)

Nyní máte dvě akce.

### <a name="train-the-bot"></a>Natrénování robota

1. Klikněte na dialogová okna trénování, pak nové dialogové okno trénování.
2. Zadejte "hello".
3. Klikněte na výsledek akce a vyberte "Jaké je datum?"
2. Zadejte 'dnes'. 
    - Všimněte si, že dnes se označí a zobrazí na druhém řádku, protože jde o předem připravených entit a nelze je upravovat.
5. Klikněte na výsledek akce
    - Všimněte si, že data teď se zobrazí v části Entity paměti. 
    - Pokud myší data se zobrazí další data LUIS, který je použitelný a můžete dál pracovat v kódu. 
6. Vyberte "datum je $luis-datetimev2".
7. Klikněte na Hotovo výuky

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Alternativní vstupy](./8-alternative-inputs.md)

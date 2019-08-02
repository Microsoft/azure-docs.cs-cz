---
title: Jak používat vlastnost "Očekávaná entita" Conversation Learner akcí – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Naučte se používat vlastnost "Očekávaná entita" modelu Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 5fbe4f09d377b9f157368184ab26341782e9aed1
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707328"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>Jak používat vlastnost "Očekávaná entita" akcí

Tento kurz ukazuje vlastnost "Očekávaná entita" akcí.

## <a name="video"></a>Video

[![Očekávaný kurz pro entity Preview](https://aka.ms/cl_Tutorial_v3_ExpectedEntity_Preview)](https://aka.ms/cl_Tutorial_v3_ExpectedEntity)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby byl spuštěný robot pro obecné kurzy.

    npm run tutorial-general

## <a name="details"></a>Podrobnosti
Pomocí vlastnosti očekávaná entita akce uložte reakci uživatele na tuto akci do entity.

Při přidávání entit do vlastnosti očekávaná entita akce bude systém:

1. Začněte tím, že se pokusíte spárovat entity pomocí modelu extrakce entity založené na strojovém učení
2. Pokud se nenajde žádné entity, přiřaďte utterance celého uživatele k $entity na základě heuristiky.
3. Zavolejte `EntityDetectionCallback`a pokračujte na výběr akce.

## <a name="steps"></a>Kroky

### <a name="create-the-model"></a>Vytvoření modelu

1. Ve webovém uživatelském rozhraní klikněte na nový model.
2. Do pole název zadejte "ExpectedEntities" a stiskněte klávesu ENTER.
3. Klikněte na tlačítko vytvořit.

### <a name="entity-creation"></a>Vytvoření entity

1. Na levém panelu klikněte na entity a pak na tlačítko nová entita.
2. Pro typ entity vyberte vlastní vzdělání.
3. Jako název entity zadejte Name.
4. Klikněte na tlačítko vytvořit.

> [!NOTE]
> Typ entity "Custom trained" znamená, že tuto entitu lze vyškolet na rozdíl od jiných typů entit.

![](../media/tutorial4_entities.PNG)

### <a name="create-the-first-action"></a>Vytvoření první akce

1. Na levém panelu klikněte na akce a pak na tlačítko Nová akce.
2. V "reakci na robota..." Zadejte "čemu vaše jméno?"
3. Do pole "očekávané entity" zadejte "název".
4. Klikněte na tlačítko vytvořit.

> [!NOTE]
> V případě, že se tato akce vybere, budou se entity zjištěné a extrahované z odpovědi uživatele ukládat do entity Name (název). Pokud se nezjistí žádné entity, uloží se do této entity celá odpověď.

### <a name="create-the-second-action"></a>Vytvoření druhé akce

1. Na levém panelu klikněte na akce a pak na tlačítko Nová akce.
2. V "reakci na robota..." Zadejte text "dobrý $name!"
3. Klikněte na tlačítko vytvořit.

> [!NOTE]
> Entita "název" byla automaticky přidána jako "požadované entity" odkazem v odpovědi.

Nyní máte dvě akce.

![](../media/tutorial4_actions.PNG)

### <a name="train-the-model"></a>Výuka modelu

1. Na levém panelu klikněte na možnost výuka dialogových oken a pak klikněte na tlačítko "nový výukový dialog".
2. Na panelu chat, kde říká "zadejte zprávu..." zadejte text "Dobrý den".
    - Tím se simuluje strana uživatele v konverzaci.
3. Klikněte na tlačítko "akce skóre".
4. Vyberte odpověď "čemu vaše jméno?"
    - "Dobrý $name!" odpověď nejde vybrat, protože tato odpověď vyžaduje, aby byla entita Name definovaná v paměti modelu.
5. Na panelu chat, kde říká "zadejte zprávu..." zadejte text "Jan".
    - "Jan" se zvýrazní jako entita založená na heuristické službě, kterou jsme vytvořili dříve k uložení odpovědi jako entity.
6. Klikněte na tlačítko "akce skóre".
    - Entita "Name" je teď definovaná jako "Jan" v paměti modelu, takže akce "Hello $name" je jako akce vybraná.
7. Vyberte odpověď "dobrý $name!"
8. Klikněte na tlačítko Uložit.

Přidávání alternativních vstupů dále navlakuje model.

1. V části Přidat alternativní vstup... Zadejte "I jsem Jose."
    - Model nerozpoznal název jako entitu, takže vybere celý blok textu jako hodnotu entity.
2. Klikněte na frázi "I jsem Jose" a pak klikněte na ikonu koše.
3. Klikněte na "Jose" a potom v seznamu entit klikněte na "název".
4. Klikněte na možnost Akce skóre.
5. Vyberte odpověď "dobrý Frank!"
6. Klikněte na tlačítko Uložit.

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Negace entit](./06-negatable-entities.md)

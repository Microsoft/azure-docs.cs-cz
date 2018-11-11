---
title: Jak používat entity pomocí modelu konverzace Learner – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Informace o používání entit s modelem Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 4b1c2d9390890618a9aa61eb425fbd132917f414
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51229044"
---
# <a name="introduction-to-entities"></a>Seznámení s entitami

Tento kurz představuje entity a ukazuje způsob použití pole "Disqualifying entity" a "Required entity" v akcích.

## <a name="video"></a>Video

[![Kurz 3 ve verzi Preview](https://aka.ms/cl-tutorial-03-preview)](https://aka.ms/blis-tutorial-03)

## <a name="requirements"></a>Požadavky

Tento kurz vyžaduje, zda je spuštěna obecné kurz bot

    npm run tutorial-general

## <a name="details"></a>Podrobnosti

Tento kurz ukazuje dvě běžné použití entity.  Entity můžete nejprve extrahujete dílčí řetězce ze zprávy pro uživatele, jako je identifikace města v "jak se o počasí v Praze?".  Za druhé entity lze omezit, až bude akce k dispozici.  Konkrétně akce můžete zobrazit seznam entitu, jako je "required" nebo "vyřazením":
- Akce požadované entity musí být obsažená v paměti bodu robotů také v pořadí pro akce, která má být k dispozici
- Vyřazení entity musí *není* nacházet v paměti bodu robotů také v pořadí pro akce, která má být k dispozici

Další kurzy zahrnují další aspekty entitami, jako je například předem připravených entit, více hodnotami a negatable entity, programové entity a manipulaci s entit v kódu.

## <a name="steps"></a>Kroky

### <a name="create-the-model"></a>Vytvoření modelu

1. V webového uživatelského rozhraní klikněte na nový Model
2. Do pole Název zadejte IntroToEntities. Pak klikněte na tlačítko vytvořit.

### <a name="create-entity"></a>Vytvoření entity

1. Klikněte na entity a pak novou entitu.
2. V názvu Entity zadejte město.
3. Kliknutí na Vytvořit

> [!NOTE]
> Typ entity je "vlastní" – to znamená, že entita může školení.  Existují také předem vytvořené entity, což znamená, že jejich chování nejde upravit – ty jsou popsané v další kurz.

### <a name="create-two-actions"></a>Vytvořte dvě akce

1. Klikněte na tlačítko akce a potom novou akci
2. V odpovědi zadejte "Nevím městě chcete".
3. Vyřazení entity zadejte $city. Klikněte na Uložit.
    - To znamená, že pokud se tato entita je definován v bodu robotů také paměti, pak tato akce bude *není* být k dispozici.
2. Klikněte na tlačítko akce a pak novou akci na vytvoří druhou akci.
3. V odpovědi zadejte "o počasí v $city je pravděpodobně hezky".
4. V požadované entity město entity se přidala automaticky vzhledem k tomu, že byl předán.
5. Kliknutí na Uložit

Nyní máte dvě akce.

![](../media/tutorial3_actions.PNG)

### <a name="train-the-bot"></a>Natrénování robota

1. Klikněte na dialogová okna trénování, pak nové dialogové okno trénování.
2. Zadejte "hello".
3. Klikněte na výsledek akce a vyberte "Nevím městě chcete?"
    - Odpověď, ve kterém jsou vyžadována město entity nelze vybrat, protože město entity není definován v bodu robotů také paměti.
2. Vyberte "Nevím městě chcete".
4. Zadejte "seattle". Zvýrazněte seattle a potom klikněte na město.
5. Klikněte na výsledek akce
    - Město hodnotu je nyní v bodu robotů také paměti.
    - "O počasí v $city je pravděpodobně hezky" je teď dostupná jako odpověď. 
6. Vyberte "O počasí v $city je pravděpodobně hezky".

Řekněme, že uživatel zadá "opakování, který". 
1. Typ, který a zadejte. Město entity a její hodnota je v paměti a k dispozici.
2. Vyberte "O počasí v $city je pravděpodobně hezky".

![](../media/tutorial3_entities.PNG)

Nyní jste vytvořili entitu a označené jako instance ve zprávách uživatele.  Také jste použili přítomnost nebo absence entity v bodu robotů také paměti pro ovládací prvek až bude akce k dispozici prostřednictvím akce vyřazení a požadované entity pole.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Očekávanou entitu](./4-expected-entity.md)

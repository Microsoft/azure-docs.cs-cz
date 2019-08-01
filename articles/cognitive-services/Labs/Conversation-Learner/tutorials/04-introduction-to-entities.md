---
title: Použití entit s modelem Conversation Learner Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Naučte se používat entity s modelem Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: cba12b6c09c1bdbf4e8f7841676a609c34109d93
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707333"
---
# <a name="introduction-to-entities"></a>Seznámení s entitami

V tomto kurzu se seznámíte s entitami, nekvalifikováním entit, vyžadovanými entitami a jejich využitím v Conversation Learner.

## <a name="video"></a>Video

[![Kurz Úvod do entit – Preview](https://aka.ms/cl_Tutorial_v3_IntroEntities_Preview)](https://aka.ms/cl_Tutorial_v3_IntroEntities)

## <a name="requirements"></a>Požadavky

Tento kurz vyžaduje, aby byl spuštěný robot pro obecné kurzy.

    npm run tutorial-general

## <a name="details"></a>Podrobnosti

Entity zachytí informace, které robot potřebuje k provedení jeho úkolu, a to buď prostřednictvím extrakce od uživatele projevy nebo přiřazení, vlastním kódem. Samotné entity můžou také omezit dostupnost akcí tím, že se explicitně klasifikují jako "povinné" nebo "neoprávněné".

- Aby byla akce k dispozici, musí být v paměti modelu přítomné požadované entity.
- Pokud chcete, aby byla akce k dispozici, *nesmí být* nekvalifikovatelné entity přítomné v paměti modelu.

Tento kurz se zaměřuje na vlastní entity. Předem připravené, s více hodnotami, negaci a programové entity jsou představeny v dalších kurzech.

## <a name="steps"></a>Kroky

### <a name="create-the-model"></a>Vytvoření modelu

1. Ve webovém uživatelském rozhraní klikněte na nový model.
2. Do pole název zadejte "IntroToEntities" a stiskněte klávesu ENTER.
3. Klikněte na tlačítko vytvořit.

### <a name="entity-creation"></a>Vytvoření entity

1. Na levém panelu klikněte na entity a pak na tlačítko nová entita.
2. Pro typ entity vyberte vlastní vzdělání.
3. Jako "název entity" zadejte "City".
4. Klikněte na tlačítko vytvořit.

> [!NOTE]
> Typ entity "Custom trained" znamená, že tuto entitu lze vyškolet na rozdíl od jiných typů entit.

### <a name="create-the-actions"></a>Vytvoření akcí

1. Na levém panelu klikněte na akce a pak na tlačítko Nová akce.
2. V "reakci na robota..." Zadejte text "Nevím, co město požadujete."
3. Do pole "vyřazení entit" zadejte "City".
4. Klikněte na tlačítko vytvořit.

> [!NOTE]
> Přidání entity City do "nekvalifikačních entit" zařadí tuto akci ze strany bot, pokud je entita "město" definována v paměti robota.

Nyní vytvořte druhou akci.

1. Na levém panelu klikněte na akce a pak na tlačítko Nová akce.
2. V "reakci na robota..." Zadejte text "počasí v $city je pravděpodobně Slunečné."
3. Klikněte na tlačítko vytvořit.

> [!NOTE]
> Entita "City" byla přidána automaticky v seznamu požadované entity odkazem v odpovědi.

![](../media/tutorial3_actions.PNG)

### <a name="train-the-model"></a>Výuka modelu

1. Na levém panelu klikněte na možnost výuka dialogových oken a pak klikněte na tlačítko "nový výukový dialog".
2. Na panelu chat, kde říká "zadejte zprávu..." zadejte text "Hello".
    - Tím se simuluje strana uživatele v konverzaci.
3. Klikněte na tlačítko "akce skóre".
4. Vyberte odpověď. nevíte, co město chcete.
5. Jako uživatel odpoví "Praha".
6. Klikněte na tlačítko "akce skóre".
7. Vyberte odpověď "počasí v $city pravděpodobně Slunečné."
8. Klikněte na tlačítko Uložit.

![](../media/tutorial3_entities.PNG)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Očekávaná entita](./05-expected-entity.md)

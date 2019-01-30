---
title: Překladače entit v modelu konverzace Learner – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Další informace o použití překladačů Entity v Learner konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e6a671470a87f4509e466bcdfe7845b7bf7ec8dc
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55209142"
---
# <a name="entity-resolvers"></a>Překladače entity

Tento kurz ukazuje použití překladačů Entity v Learner konverzace

## <a name="video"></a>Video

[![Entita překladače kurz ve verzi Preview](https://aka.ms/cl_Tutorial_v3_EntityResolvers_Preview)](https://aka.ms/cl_Tutorial_v3_EntityResolvers)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna obecné kurz bot

    npm run tutorial-general

## <a name="details"></a>Podrobnosti

- Volitelná vlastnost z vlastní entity je typ překladače.
- Překladače entity využívat sílu rozpoznávání předem vytrénovaných entity v LUIS poskytnout další podrobnosti a přehlednost pro vaše vlastní entitu.

## <a name="steps"></a>Kroky

### <a name="create-a-new-model"></a>Vytvořit nový Model

1. Ve webové uživatelské rozhraní klikněte na tlačítko "Nový Model".
2. V poli "Name" typ "Entity překladače", stiskněte klávesu enter nebo klikněte na tlačítko "Vytvořit".

### <a name="create-a-pair-of-entities"></a>Vytvoření páru entit

1. Na levém panelu klikněte na tlačítko "," subjekty a pak klikněte na tlačítko "Nová entita".
2. Do pole "Název Entity" zadejte "Výchozí".
3. V rozevíracím seznamu "Typ překladače" Vyberte "datetimeV2".
4. Klikněte na tlačítko "Vytvořit".
5. Po přečtení informací automaticky otevírané okno, klikněte na tlačítko "OK".
6. Následující stejné kroky vytvoření jiná entita s názvem "return", která má také typ překladače "datetimeV2".

### <a name="create-a-pair-of-actions"></a>Vytvoření páru akce

1. Na levém panelu klikněte na tlačítko "Akce" a pak klikněte na tlačítko "Nová akce".
2. V odpovědi bodu robotů také"" typ pole "jste opuštění na $departure a vrací na $return".
    - DŮLEŽITÉ – když zadáte "$[entityName]" je potřeba přístupů zadejte nebo klikněte na některou entitu v rozevíracím seznamu, jinak konverzace Learner zohlední to jako textu místo Entity.
    - Všimněte si, že pole "požadováno subjekty" zobrazí také tyto entity a nelze odebrat. To zabrání tato akce poté jsou dostupné, dokud se oba požadované že entity jsou k dispozici.
3. Klikněte na tlačítko "Vytvořit".
4. Klepnutím na tlačítko "Nová akce" znovu vytvoří druhou akci.
5. Pole v "bodu robotů také odpovědi" typ "Pokud plánujete cestovní?".
6. V "Vyřazení entity" typ pole, "odeslání" a také typ "return".
    - Tyto soubory určují naše Bot není provádělo tuto akci, pokud některý z těchto entit obsahovat hodnotu.
7. Klikněte na tlačítko "Vytvořit".


### <a name="training"></a>Školení

1. Podívejte "školení: [Status]" v horní levé části stránky a počkejte na její "Dokončit".
    - Pokud to trvá příliš dlouho, můžete kliknout na odkaz "Obnovit".
    - Školení stav "Dokončeno" je nutné, aby naše Entity překladače fungovat, když jsme trénování modelu.
2. Na levém panelu klikněte na tlačítko "Dialogů Train" a pak klikněte na tlačítko "Dialogové okno Nový Train".
3. Typ v první utterance uživatele "rezervovat mě let". 
4. Klikněte na tlačítko "Skóre akce".
5. Vyberte odpověď na "Pokud plánujete cestovní?".
6. Jako uživatel odpoví, "byste museli opustit zítra a vrácení neděle příští týden".
    - Všimněte si, jak konverzace Learner zjistil že zapnout dvě "Předem školení datum" entity v daného uživatele.
7. V panelu "Entity detekce" Vyberte text "zítra" a označovat jako "odeslání"
8. Označte taky text "Neděle příští týden" jako "return"
9. Klikněte na tlačítko "Skóre akce".
    - Všimněte si, jak podokno "Paměti" obsahuje kalendářní data odeslání a vraťte se.
    - Najeďte myší každé z nich a sledujte, jak entity, které jsou objekty datum, které jasně zachycovat skutečné calandar datum na rozdíl od "Neděle" nebo "zítřejší den".
10. Vyberte, "opouštíte na..." Odpovědi robotů.
11. Klikněte na tlačítko "Save".

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Alternativní vstupy](./10-alternative-inputs.md)

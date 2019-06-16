---
title: Překladače entit v modelu Learner konverzace – Azure Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Další informace o použití překladačů Entity v Learner konverzace.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 51f74f504f0ad70c8c7f73be8ee6a05add685824
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475752"
---
# <a name="entity-resolvers"></a>Překladače entity

Tento kurz ukazuje použití překladačů Entity v Learner konverzace

## <a name="video"></a>Video

[![Entita překladače kurz ve verzi Preview](https://aka.ms/cl_Tutorial_v3_EntityResolvers_Preview)](https://aka.ms/cl_Tutorial_v3_EntityResolvers)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, zda je spuštěna obecné kurzu robota

    npm run tutorial-general

## <a name="details"></a>Podrobnosti

- Volitelná vlastnost z vlastní entity je typ překladače.
- Překladače entity využívat sílu rozpoznávání předem vytrénovaných entity v LUIS poskytnout další podrobnosti a přehlednost pro vaše vlastní entitu.

## <a name="steps"></a>Kroky

Začněte na domovskou stránku webového uživatelského rozhraní.

### <a name="create-the-model"></a>Vytvoření modelu

1. Vyberte **nový Model**.
2. Zadejte **Entity překladače** pro **název**.
3. Vyberte **Vytvořit**.

### <a name="create-a-pair-of-entities"></a>Vytvoření páru entit

1. Vyberte **entity** na levém panelu, pak **novou entitu**.
2. Zadejte **odeslání** pro **název Entity**.
3. Vyberte **datetimeV2** pro **typ překladače**.
4. Vyberte **Vytvořit**. Zavřít informační automaticky otevírané okno výběrem **OK**.
5. Opakujte kroky 1 až 4 vytvořte druhý entitu s názvem **vrátit** s **datetimeV2** typ překladače.

![](../media/T09_entities.png)

### <a name="create-a-pair-of-actions"></a>Vytvoření páru akce

1. Vyberte **akce** na levém panelu, pak **novou akci**.
2. Zadejte **jsou opuštění na $departure a vrácení na $return** pro **odezvy bodu robotů také...** .
    - DŮLEŽITÉ – při zadávání $[entityName] je potřeba přístupů zadejte nebo klikněte na entity v rozevíracím seznamu jinak Learner konverzace bude předpokládat, že to bude textu místo Entity.
    - Všimněte si, že **požadované entity** pole se zobrazí také tyto entity a nelze ji odebrat. To zabrání tato akce poté jsou dostupné, dokud se oba požadované že entity jsou k dispozici.
3. Vyberte **Vytvořit**.
4. Vyberte **novou akci** vytvořit druhou akci.
5. Zadejte **když plánujete cestovní?** pro **odezvy bodu robotů také...** .
6. Zadejte **odeslání** a **vrátit** pro **vyřazení entity**. Tyto soubory určují naše Bot není provádělo tuto akci, pokud některý z těchto entit obsahovat hodnotu.
7. Vyberte **Vytvořit**.

![](../media/T09_actions.png)

### <a name="training"></a>Školení

1. Podívejte **školení: [Status]** v levém horním rohu pro **dokončeno**.
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
    - Najeďte myší každé z nich a sledujte, jak entity, které jsou objekty datum, které jasně zachytit skutečný kalendářní datum na rozdíl od "Neděle" nebo "zítřejší den".
10. Vyberte, "opouštíte na..." Odpovědi robotů.
11. Klikněte na tlačítko "Save".

![](../media/T09_training.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Výčet entity](./tutorial-enum-set-entity.md)

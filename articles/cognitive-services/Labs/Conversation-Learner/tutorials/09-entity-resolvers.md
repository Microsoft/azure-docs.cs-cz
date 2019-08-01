---
title: Překladače entit v Conversation Learnerovém modelu – Azure Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Naučte se používat překladače entit v Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 60c4abf1590cb91fd460cc6a2a5ba75a225ebd80
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704047"
---
# <a name="entity-resolvers"></a>Překladače entit

V tomto kurzu se dozvíte, jak používat překladače entit v Conversation Learner

## <a name="video"></a>Video

[![Kurz překladačů entit ve verzi Preview](https://aka.ms/cl_Tutorial_v3_EntityResolvers_Preview)](https://aka.ms/cl_Tutorial_v3_EntityResolvers)

## <a name="requirements"></a>Požadavky
Tento kurz vyžaduje, aby byl spuštěný robot pro obecné kurzy.

    npm run tutorial-general

## <a name="details"></a>Podrobnosti

- Typ překladače je volitelná vlastnost vlastních entit.
- Překladače entit využívají sílu předem vyškolených rozpoznávání entit v LUIS a poskytují další podrobnosti a srozumitelnosti vlastní entity.

## <a name="steps"></a>Kroky

Začněte na domovské stránce ve webovém uživatelském rozhraní.

### <a name="create-the-model"></a>Vytvoření modelu

1. Vyberte **Nový model**.
2. Jako **název**zadejte **překladače entit** .
3. Vyberte **Vytvořit**.

### <a name="create-a-pair-of-entities"></a>Vytvoření páru entit

1. Na levém panelu vyberte **entity** a pak **novou entitu**.
2. Jako **název entity**zadejte **odchod** .
3. Jako **typ překladače**vyberte **datetimeV2** .
4. Vyberte **Vytvořit**. Kliknutím na **tlačítko OK**zavřete informační automaticky otevírané okno.
5. Opakujte kroky 1-4 a vytvořte druhou entitu s názvem **return** s typem překladače **datetimeV2** .

![](../media/T09_entities.png)

### <a name="create-a-pair-of-actions"></a>Vytvoření páru akcí

1. Na levém panelu vyberte **Akce** a pak **Nová akce**.
2. Zadejte **, že opouštíte $Departure a vrátíte se na $Return** **odpověď robota...**
    - DŮLEŽITÉ – při psaní do $ [EntityName] je potřeba, abyste zadali ENTER nebo v rozevírací nabídce kliknete na entitu v opačném případě Conversation Learner bude tento text místo entity považován za text.
    - Všimněte si, že pole **požadované entity** získá i tyto entity a nelze je odebrat. Tím zabráníte tomu, aby se tato akce stala dostupná, dokud nebudou přítomny obě požadované entity.
3. Vyberte **Vytvořit**.
4. Výběrem možnosti **Nová akce** vytvořte druhou akci.
5. Zadejte, kdy máte v **plánu cestovat?** pro **odpověď robota...** .
6. Zadejte odchody a **vraťte** se k neoprávněným **entitám**. Díky tomu by náš robot nepřijal tuto akci, pokud některá z těchto entit obsahuje hodnotu.
7. Vyberte **Vytvořit**.

![](../media/T09_actions.png)

### <a name="training"></a>Školení

1. Podívejte se na **školení: [stav]** v levém horním rohu pro **dokončeno**.
    - Pokud trvá příliš dlouho, můžete kliknout na odkaz aktualizovat.
    - Stav školení "dokončeno" je nutný, aby překladače entit fungoval při výuce modelu.

2. Na levém panelu klikněte na možnost výuka dialogových oken a pak klikněte na tlačítko nového výukového dialogového okna.
3. Zadejte první utterance uživatele, "Book mi a let". 
4. Klikněte na tlačítko "akce skóre".
5. Vyberte odpověď "kdy plánujete cestu?".
6. Jako uživatel odpoví, "opouští zítra a vrací neděli příští týden".
    - Všimněte si, jak Conversation Learner v tomto uživateli zjistila dvě entity "předem vyškolená data".
7. Na panelu zjišťování entit vyberte text "zítra" a označte ho jako "odchod".
8. Také označit text "neděle na příští týden" jako "Return"
9. Klikněte na tlačítko "akce skóre".
    - Všimněte si, jak podokno paměť obsahuje data odchodu a vrácení.
    - Najeďte myší na každé z nich a sledujte, jak jsou entity objekty data, které jasně zachycují skutečné datum kalendáře na rozdíl od "neděle" nebo "zítra".
10. Vyberte si, že jste opustili... Odpověď robota
11. Klikněte na tlačítko Uložit.

![](../media/T09_training.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vyčíslit entity](./tutorial-enum-set-entity.md)

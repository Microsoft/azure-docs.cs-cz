---
title: Vzory – přesnost přidávání – LUIS
titleSuffix: Azure Cognitive Services
description: Přidejte šablony vzorů, aby se zlepšila přesnost předpovědi v aplikacích Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: cbd8ad73ff4a03790dd6b22d5ce33acf09a2b125
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491365"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Postup přidání vzorů pro zlepšení přesnosti předpovědi
Jakmile aplikace LUIS přijme koncový bod projevy, použijte [vzorek](luis-concept-patterns.md) , který vylepšit přesnost předpovědi pro projevy, která odhalí vzor v textovém pořadí a v textovém výběru. Vzory používají specifickou [syntaxi](luis-concept-patterns.md#pattern-syntax) k označení umístění: [entity](luis-concept-entity-types.md), [role](luis-concept-roles.md)entit a volitelného textu.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-template-utterance-to-create-pattern"></a>Přidání šablony utterance k vytvoření vzoru
1. Otevřete svou aplikaci tak, že na stránce **Moje aplikace** vyberete její název a na levém panelu jste v části **zlepšení výkonu aplikace**vybrali **vzory** .

    ![Snímek obrazovky se seznamem vzorů](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Vyberte správný záměr pro daný model. 

    ![Vybrat záměr](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. Do textového pole šablony zadejte šablonu utterance a vyberte Enter. Pokud chcete zadat název entity, použijte správnou syntaxi entity Pattern. Zahajte syntaxi entity pomocí `{`. Zobrazí se seznam entit. Vyberte správnou entitu a pak vyberte zadat. 

    ![Snímek entity pro vzor](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Pokud vaše entita obsahuje [roli](luis-concept-roles.md), uveďte roli s jednou dvojtečkou, `:`za názvem entity, jako je například `{Location:Origin}`. Seznam rolí pro entity se zobrazí v seznamu. Vyberte roli a pak vyberte zadat. 

    ![Snímek obrazovky entity s rolí](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Po výběru správné entity Dokončete zadání vzoru a pak vyberte Enter. Jakmile dokončíte zadávání vzorů, proveďte [výuku](luis-how-to-train.md) své aplikace.

    ![Snímek obrazovky zadaného vzoru s oběma typy entit](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Školení aplikace po změně modelu pomocí vzorů
Po přidání, úpravě, odebrání nebo opětovném přiřazení vzoru, [školení](luis-how-to-train.md) a [publikování](luis-how-to-publish-app.md) aplikace pro změny, které mají vliv na dotazy koncového bodu. 

<a name="search-patterns"></a>
<a name="edit-a-pattern"></a>
<a name="reassign-individual-pattern-to-different-intent"></a>
<a name="reassign-several-patterns-to-different-intent"></a>
<a name="delete-a-single-pattern"></a>
<a name="delete-several-patterns"></a>
<a name="filter-pattern-list-by-entity"></a>
<a name="filter-pattern-list-by-intent"></a>
<a name="remove-entity-or-intent-filter"></a>
<a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>

## <a name="use-contextual-toolbar"></a>Použít kontextový panel nástrojů

Kontextový panel nad seznamem vzorů vám umožní:

* Vyhledat vzory
* Úprava vzoru
* Změna přiřazení jednotlivého vzoru jinému záměru
* Opětovné přiřazení několika vzorů jinému záměru
* Odstranění – jeden vzor
* Odstranění několika vzorů
* Filtrovat seznam vzorků podle entity
* Filtr – vzor – seznam po záměru
* Odebrat entitu nebo filtr záměru
* Přidat vzor ze stávající stránky utterance na záměr nebo entitu

## <a name="next-steps"></a>Další kroky

* Naučte se [vytvářet vzor](luis-tutorial-pattern.md) pomocí vzoru. jakékoli role a s kurzem.
* Naučte se, jak svou aplikaci [naučit](luis-how-to-train.md) .

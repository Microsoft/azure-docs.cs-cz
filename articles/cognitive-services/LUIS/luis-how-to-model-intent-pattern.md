---
title: Vzory – přesnost přidávání – LUIS
titleSuffix: Azure Cognitive Services
description: Přidejte šablony vzorů, aby se zlepšila přesnost předpovědi v aplikacích Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: 914ca77b18d0469c3ea926848be4c60aab04c9c6
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91539100"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Postup přidání vzorů pro zlepšení přesnosti předpovědi
Jakmile aplikace LUIS přijme koncový bod projevy, použijte [vzorek](luis-concept-patterns.md) , který vylepšit přesnost předpovědi pro projevy, která odhalí vzor v textovém pořadí a v textovém výběru. Vzory používají specifickou [syntaxi](luis-concept-patterns.md#pattern-syntax) k označení umístění: [entity](luis-concept-entity-types.md), [role](luis-concept-roles.md)entit a volitelného textu.

> [!CAUTION]
> Vzory zahrnují jenom nadřazené entity strojového učení, ne subentity.

## <a name="add-template-utterance-using-correct-syntax"></a>Přidat šablonu utterance pomocí správné syntaxe

1. Přihlaste se k [portálu Luis](https://www.luis.ai)a vyberte své **předplatné** a **prostředek pro vytváření obsahu** , abyste viděli aplikace přiřazené k tomuto zdrojovému prostředku.
1. Otevřete svou aplikaci tak, že na stránce **Moje aplikace** vyberete její název.
1. Na levém panelu vyberte **vzory** a v části **zlepšení výkonu aplikace**.

1. Vyberte správný záměr pro daný model.

1. Do textového pole šablony zadejte šablonu utterance a vyberte Enter. Pokud chcete zadat název entity, použijte správnou syntaxi entity Pattern. Začněte syntaxí entity pomocí `{` . Zobrazí se seznam entit. Vyberte správnou entitu.

    > [!div class="mx-imgBorder"]
    > ![Snímek entity pro vzor](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Pokud vaše entita obsahuje [roli](luis-concept-roles.md), uveďte roli s jednou dvojtečkou, `:` za názvem entity, jako je například `{Location:Origin}` . Seznam rolí pro entity se zobrazí v seznamu. Vyberte roli a pak vyberte zadat.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky entity s rolí](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Po výběru správné entity Dokončete zadání vzoru a pak vyberte Enter. Jakmile dokončíte zadávání vzorů, proveďte [výuku](luis-how-to-train.md) své aplikace.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky zadaného vzoru s oběma typy entit](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="create-a-patternany-entity"></a>Vytvoření vzoru. kterákoli entita

[Vzor. všechny](luis-concept-entity-types.md) entity jsou platné jenom v [vzorcích](luis-how-to-model-intent-pattern.md), ne záměrech, jako je například projevy. Tento typ entity pomáhá LUIS najít konec entit s různou délkou a volbou Wordu. Vzhledem k tomu, že tato entita se používá ve vzoru, LUIS ví, kde je konec entity v šabloně utterance.

1. Přihlaste se k [portálu Luis](https://www.luis.ai)a vyberte své **předplatné** a **prostředek pro vytváření obsahu** , abyste viděli aplikace přiřazené k tomuto zdrojovému prostředku.
1. Otevřete svou aplikaci tak, že na stránce **Moje aplikace** vyberete její název.
1. V části **sestavení** vyberte **entity** na levém panelu a potom vyberte **+ vytvořit**.

1. V dialogovém okně **Zvolte typ entity** zadejte název entity do pole **název** a jako **typ** vyberte **vzor. any** a pak vyberte **vytvořit**.

    Jakmile [vytvoříte vzor utterance](luis-how-to-model-intent-pattern.md) pomocí této entity, bude tato entita extrahována kombinovaným algoritmem pro strojové učení a porovnávání textů.

## <a name="adding-example-utterances-as-pattern"></a>Přidání příkladu projevy jako vzoru

Pokud chcete přidat vzor pro entitu, _nejjednodušší_ způsob je vytvořit vzor ze stránky s podrobnostmi záměru. Tím se zaručí, že syntaxe odpovídá příkladu utterance.

1. Přihlaste se k [portálu Luis](https://www.luis.ai)a vyberte své **předplatné** a **prostředek pro vytváření obsahu** , abyste viděli aplikace přiřazené k tomuto zdrojovému prostředku.
1. Otevřete svou aplikaci tak, že na stránce **Moje aplikace** vyberete její název.
1. Na stránce seznam **záměrů** vyberte název záměru ukázkového utteranceu, ze kterého chcete vytvořit šablonu utterance.
1. Na stránce Podrobnosti záměru vyberte řádek pro příklad utterance, který chcete použít jako šablonu utterance, a pak vyberte **+ Přidat jako vzorek** z panelu nástrojů kontextu.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s výběrem příkladu utterance jako vzoru šablony na stránce s podrobnostmi záměru.](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

    Utterance musí zahrnovat entitu, aby bylo možné vytvořit vzor z utterance.

1. V místním okně vyberte na stránce **Potvrdit vzorce** možnost **Hotovo** . Nemusíte definovat dílčí entity nebo funkce entit. Musíte uvést jenom entitu Machine Learning.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s potvrzením příkladu utterance jako vzoru šablony na stránce s podrobnostmi záměru.](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. Pokud potřebujete upravit šablonu, jako je například výběr textu jako volitelné, s `[]` hranatými závorkami (hranaté závorky), musíte tuto úpravu provést na stránce **vzory** .

1. V navigačním panelu vyberte možnost **výuka** pro výuku aplikace s novým vzorem.

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

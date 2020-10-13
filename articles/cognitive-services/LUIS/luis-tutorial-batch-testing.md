---
title: 'Kurz: dávkové testování pro vyhledání problémů – LUIS'
description: Tento kurz ukazuje, jak pomocí dávkového testování ověřit kvalitu vaší aplikace Language Understanding (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/07/2020
ms.openlocfilehash: 7dd181f8cd398dd683296b446028b398a9800b53
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91298306"
---
# <a name="tutorial-batch-test-data-sets"></a>Kurz: dávkové test sady dat

Tento kurz ukazuje, jak pomocí dávkového testování ověřit kvalitu vaší aplikace Language Understanding (LUIS).

Dávkové testování umožňuje ověřit stav aktivního a trained modelu se známou sadou označených projevy a entit. V dávkovém souboru ve formátu JSON přidejte projevy a nastavte popisky entit, které potřebujete, v rámci utterance.

Požadavky na dávkové testování:

* Maximálně 1000 projevy na test.
* Žádné duplicity.
* Povolené typy entit: jenom uživatelsky proučené entity.

Pokud používáte jinou aplikaci než tento kurz, *nepoužívejte ukázkový* projevy již přidaný do vaší aplikace.

**V tomto kurzu se naučíte:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importovat ukázkovou aplikaci
> * Vytvoření souboru dávkového testu
> * Spustit test dávky
> * Kontrola výsledků testu

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importovat ukázkovou aplikaci

Importujte aplikaci, která používá Pizza pořadí, například `1 pepperoni pizza on thin crust` .

1.  Stáhněte si [soubor JSON aplikace](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true) a uložte si ho.

1. Přihlaste se k [portálu Luis](https://www.luis.ai)a vyberte své **předplatné** a **prostředek pro vytváření obsahu** , abyste viděli aplikace přiřazené k tomuto zdrojovému prostředku.
1. Importujte JSON do nové aplikace a pojmenujte aplikaci `Pizza app` .


1. Vyberte **vlak** v pravém horním rohu navigace a prohlaste aplikaci.

## <a name="what-should-the-batch-file-utterances-include"></a>Jak by měl dávkový soubor projevy zahrnovat

Dávkový soubor by měl obsahovat projevy s entitami strojového učení nejvyšší úrovně, které jsou označené jako počáteční a koncová pozice. Projevy by neměl být součástí příkladů, které už jsou v aplikaci. Měly by být projevy, že chcete pozitivní předpověď záměru a entit.

Testy můžete oddělit podle záměru nebo entity nebo mít všechny testy (až 1000 projevy) ve stejném souboru.

## <a name="batch-file"></a>Dávkový soubor

Příklad JSON obsahuje jeden utterance s označenou entitou pro ilustraci toho, co soubor testu vypadá. Ve vašich vlastních testech byste měli mít spoustu projevy se správným záměrem a entitou strojového učení s názvem.

1. Vytvořte `pizza-with-machine-learned-entity-test.json` v textovém editoru nebo [si ho stáhněte](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true) .

2. V dávkovém souboru ve formátu JSON přidejte utterance s **záměrem** , který chcete odhadnout v testu.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>Spustit dávku

1. V horním navigačním panelu vyberte **test** .

2. Na panelu na pravé straně vyberte **panel dávkové testování** .

3. Vyberte **importovat datovou sadu**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky aplikace LUIS s zvýrazněnou importovanou datovou sadou](./media/luis-tutorial-batch-testing/import-dataset-button.png)

4. Vyberte umístění souboru `pizza-with-machine-learned-entity-test.json` .

5. Pojmenujte datovou sadu `pizza test` a vyberte **Hotovo**.

    > [!div class="mx-imgBorder"]
    > ![Vybrat soubor](./media/luis-tutorial-batch-testing/import-dataset-modal.png)

6. Vyberte tlačítko **Run** (Spustit).

7. Vyberte **Zobrazit výsledky**.

8. Zkontrolujte výsledky v grafu a v legendě.

## <a name="review-batch-results-for-intents"></a>Kontrola výsledků dávky pro záměry

Výsledky testu ukazují, jak byl projevy testů předpovězený proti aktivní verzi.

V dávkovém grafu se zobrazí čtyři kvadranty výsledků. Napravo od grafu je filtr. Filtr obsahuje záměry a entity. Když vyberete [část grafu](luis-concept-batch-test.md#batch-test-results) nebo bod v rámci grafu, zobrazí se v grafu odpovídající utterance (y).

Při najetí myší na graf může kolečko myši zvětšit nebo zmenšit displej v grafu. To je užitečné v případě, že je graf v grafu seskupen těsně dohromady.

Graf je ve čtyřech kvadrantech, přičemž dva z oddílů se zobrazí červeně.

1. V seznamu filtru vyberte záměr **ModifyOrder** .

    > [!div class="mx-imgBorder"]
    > ![Výběr záměru ModifyOrder ze seznamu filtrů](./media/luis-tutorial-batch-testing/select-intent-from-filter-list.png)

    Utterance je předpovězen jako **skutečný pozitivní** význam, který utterance úspěšně shodoval s jeho pozitivním předpověďm uvedeným v dávkovém souboru.

    > [!div class="mx-imgBorder"]
    > ![Utterance úspěšně odpovídal jeho kladné předpovědi.](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    Zelené zaškrtnutí v seznamu filtry také označují úspěšnost testu pro každý záměr. Všechny ostatní záměry jsou uvedeny s 1/1 kladným skóre, protože utterance byl testován proti každému záměru, jako negativní test pro všechny záměry, které nejsou uvedeny v dávkovém testu.

1. Vyberte **potvrzující** záměr. Tento záměr není uvedený v dávkovém testu, takže se jedná o negativní test utterance, který je uvedený v dávkovém testu.

    > [!div class="mx-imgBorder"]
    > ![Utterance byl úspěšně předpovězený negativ pro neuvedené záměry v dávkovém souboru.](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    Negativní test byl úspěšný, jak bylo uvedeno u zeleného textu ve filtru a v mřížce.

## <a name="review-batch-test-results-for-entities"></a>Kontrola výsledků dávkových testů pro entity

Entita ModifyOrder jako entita počítače s podentitami obsahuje informace o tom, jestli se entita nejvyšší úrovně shodovala s tím, jak jsou dílčí entity předpovězeny.

1. V seznamu filtrovat vyberte entitu **ModifyOrder** a pak vyberte kruh v mřížce.

1. Předpověď entity se zobrazí pod grafem. Zobrazení obsahuje plné čáry pro předpovědi, které odpovídají očekávanému a tečkovanému řádku pro předpovědi, které neodpovídají očekávání.

    > [!div class="mx-imgBorder"]
    > ![Nadřazená entita entity byla úspěšně předpovězena v dávkovém souboru.](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

## <a name="finding-errors-with-a-batch-test"></a>Hledání chyb pomocí dávkového testu

V tomto kurzu jste si ukázali, jak spustit test a interpretovat výsledky. Nedostalo se do testovacího filozofie nebo na reakci na neúspěšné testy.

* Ujistěte se, že v testu pokryjete pozitivní i negativní projevy, včetně projevy, které mohou být předpovězeny pro jiný, ale související záměr.
* V případě selhání projevy proveďte následující úlohy a spusťte testy znovu:
    * Projděte si aktuální příklady pro záměry a entity, ověřte, že je v příkladu projevy aktivní verze správná jak pro záměr, tak pro označování entit.
    * Přidat funkce, které pomůžou prediktivním záměrům a entitám vaší aplikace
    * Přidat další pozitivní příklad projevy
    * Kontrola bilance příkladu projevy napříč záměry

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](./includes/cleanup-resources-preview-portal.md)]

## <a name="next-step"></a>Další krok

Kurz pro ověření aktuálního modelu použil dávkový test.

> [!div class="nextstepaction"]
> [Přečtěte si o vzorcích](luis-tutorial-pattern.md)


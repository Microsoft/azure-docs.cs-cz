---
title: 'Kurz: Dávkové testování za účelem nalezení problémů – LUIS'
description: Tento kurz ukazuje, jak pomocí dávkového testování ověřit kvalitu aplikace Language Understanding (LUIS).
ms.topic: tutorial
ms.date: 03/02/2020
ms.openlocfilehash: c276f0b52f83937fbe3b6fd9e0b7c1a66f665095
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "78250449"
---
# <a name="tutorial-batch-test-data-sets"></a>Kurz: Dávkové testovací datové sady

Tento kurz ukazuje, jak pomocí dávkového testování ověřit kvalitu aplikace Language Understanding (LUIS).

Dávkové testování umožňuje ověřit stav aktivního, trénovaného modelu se známou sadou popisek promluv a entit. V dávkovém souboru ve formátu JSON přidejte projevy a nastavte popisky entit, které potřebujete předpovědět uvnitř utterance.

Požadavky na dávkové zkoušky:

* Maximálně 1000 projevy na test.
* Žádné duplikáty.
* Typy entit jsou povoleny: pouze obráběné entity.

Při použití jiné aplikace než v tomto kurzu *nepoužívejte* příklad projevy již přidané do aplikace.

**V tomto kurzu se dozvíte, jak:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importovat ukázkové aplikace
> * Vytvoření dávkového testovacího souboru
> * Spuštění dávkového testu
> * Zkontrolovat výsledky testů

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importovat ukázkové aplikace

Importaplikace, která přijme objednávku `1 pepperoni pizza on thin crust`pizzy, například .

1.  Stáhněte si [soubor JSON aplikace](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true) a uložte si ho.

1. Použijte [portál preview LUIS](https://preview.luis.ai/), importujte JSON do `Pizza app`nové aplikace, pojmenujte aplikaci .

1. V pravém horním rohu navigace vyberte Trénovat aplikaci vyberte **Vlak.**

## <a name="what-should-the-batch-file-utterances-include"></a>Co by měly listy dávkového souboru obsahovat

Dávkový soubor by měl obsahovat projevy s entitami naučené počítačem nejvyšší úrovně s popiskem včetně počáteční a koncové polohy. Projevy by neměly být součástí příkladů, které jsou již v aplikaci. Měly by být projevy, které chcete pozitivně předpovědět pro záměr a entity.

Můžete oddělit testy záměrem nebo entitou nebo mít všechny testy (až 1000 projevy) ve stejném souboru.

## <a name="batch-file"></a>Dávkový soubor

Příklad JSON obsahuje jednu utterance s označenou entitou pro ilustraci, jak vypadá testovací soubor. Ve vlastních testech byste měli mít mnoho projevy se správným záměrem a počítačem naučil entity označené.

1. Vytvořte `pizza-with-machine-learned-entity-test.json` v textovém editoru nebo [jej stáhněte.](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true)

2. V dávkovém souboru ve formátu JSON přidejte utterance s **záměr,** který chcete předpovědět v testu.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>Spuštění dávky

1. V horním navigačním panelu vyberte **Testovat.**

2. V pravém panelu vyberte **panel Dávkové testování.**

3. Vyberte **Importovat datovou sadu**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky aplikace LUIS se zvýrazněnou importem datové sady](./media/luis-tutorial-batch-testing/import-dataset-button.png)

4. Zvolte umístění souboru. `pizza-with-machine-learned-entity-test.json`

5. Pojmenujte `pizza test` datovou sadu a vyberte **Hotovo**.

    > [!div class="mx-imgBorder"]
    > ![Vybrat soubor](./media/luis-tutorial-batch-testing/import-dataset-modal.png)

6. Vyberte tlačítko **Spustit**.

7. Vyberte **Zobrazit výsledky**.

8. Prohlédněte si výsledky v grafu a legendě.

## <a name="review-batch-results-for-intents"></a>Kontrola výsledků dávky záměrů

Výsledky testu graficky zobrazit, jak byly předpovězeny promluvy test proti aktivní verzi.

Dávkový graf zobrazuje čtyři kvadranty výsledků. Napravo od grafu je filtr. Filtr obsahuje záměry a entity. Když vyberete [část grafu](luis-concept-batch-test.md#batch-test-results) nebo bod v grafu, přidružené projevy se zobrazí pod grafem.

Při najetí myší na graf může kolečko myši zvětšit nebo zmenšit zobrazení v grafu. To je užitečné, pokud je v grafu mnoho bodů seskupených těsně dohromady.

Graf je ve čtyřech kvadrantech, přičemž dvě části jsou zobrazeny červeně.

1. V seznamu filtrů vyberte záměr **Změnit příkaz.**

    > [!div class="mx-imgBorder"]
    > ![Vybrat ZměnitZáměr příkazu ze seznamu filtrů](./media/luis-tutorial-batch-testing/select-intent-from-filter-list.png)

    Utterance je předpovězen jako **True Pozitivní** význam utterance úspěšně uzavřeno jeho pozitivní předpověď uvedená v dávkovém souboru.

    > [!div class="mx-imgBorder"]
    > ![Utterance úspěšně odpovídala jeho pozitivní predikci](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    Zelené zaškrtnutí v seznamu filtrů také označují úspěch testu pro každý záměr. Všechny ostatní záměry jsou uvedeny s 1/1 kladné skóre, protože utterance byl testován proti každému záměru, jako negativní test pro všechny záměry, které nejsou uvedeny v dávkovém testu.

1. Vyberte záměr **potvrzení.** Tento záměr není uveden v dávkovém testu, takže se jedná o negativní test utterance, který je uveden v testu dávky.

    > [!div class="mx-imgBorder"]
    > ![Utterance úspěšně předpověděl negativní pro neuvedený záměr v dávkovém souboru](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    Negativní test byl úspěšný, jak je uvedeno se zeleným textem ve filtru a mřížkou.

## <a name="review-batch-test-results-for-entities"></a>Kontrola výsledků dávkových testů pro entity

Entita ModifyOrder jako entita počítače s dílčími entitami se zobrazí, pokud se entita nejvyšší úrovně shoduje, a zobrazuje způsob, jakým jsou podvíšené entity.

1. Vyberte entitu **ModifyOrder** v seznamu filtrů a pak vyberte kruh v mřížce.

1. Předpověď entity se zobrazí pod grafem. Zobrazení obsahuje plné čáry pro předpovědi, které odpovídají očekávání a tečkované čáry pro předpovědi, které neodpovídají očekávání.

    > [!div class="mx-imgBorder"]
    > ![Nadřazený entitu byl úspěšně předpovězen v dávkovém souboru](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

## <a name="finding-errors-with-a-batch-test"></a>Hledání chyb pomocí dávkového testu

Tento kurz vám ukázal, jak spustit test a interpretovat výsledky. Netýkala se testovací filozofie ani toho, jak reagovat na neúspěšné testy.

* Ujistěte se, že pokrytí pozitivní i negativní projevy v testu, včetně projevy, které mohou být předpovězeny pro jiný, ale související záměr.
* Pro selhání projevy, proveďte následující úlohy a pak spusťte testy znovu:
    * Zkontrolujte aktuální příklady záměrů a entit, ověřte ukázkové projevy aktivní verze jsou správné jak pro záměr, tak pro označení entit.
    * Přidání funkcí, které vaší aplikaci pomohou předvídat záměry a entity
    * Přidání dalších kladných ukázkových promluv
    * Kontrola rovnováhy příkladových projevů napříč záměry

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](./includes/cleanup-resources-preview-portal.md)]

## <a name="next-step"></a>Další krok

Kurz používá dávkový test k ověření aktuálního modelu.

> [!div class="nextstepaction"]
> [Další informace o vzorcích](luis-tutorial-pattern.md)


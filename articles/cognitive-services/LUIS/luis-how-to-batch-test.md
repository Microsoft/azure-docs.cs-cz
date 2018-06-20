---
title: Batch testování aplikace LEOŠ - Azure | Microsoft Docs
description: Testování batch znalosti jazyka (LEOŠ) slouží k vyhledání utterances s nesprávnou záměry a entity.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: v-geberr
ms.openlocfilehash: 822fb1e2d5b13941527d242e8501b423bd6b81cb
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265509"
---
# <a name="batch-testing"></a>Testování batch
 Testování batch je komplexní test na váš aktuální trained model k měření jeho výkon v LEOŠ. 

<a name="batch-testing"></a>
## <a name="import-a-dataset-file-for-batch-testing"></a>Importovat soubor datovou sadu pro testování batch

1. Vyberte **Test** v horním panelu a pak vyberte **dávky testování panely**.

    ![Testování odkaz batch](./media/luis-how-to-batch-test/batch-testing-link.png)

2. Vyberte **datovou sadu Import**. **Nová datová sada Import** zobrazí se dialogové okno. Vyberte **zvolit soubor** a najděte [JSON](luis-concept-batch-test.md#batch-file-format) soubor, který obsahuje *více než 1 000* utterances k testování.

    ![Soubor importu datové sady](./media/luis-how-to-batch-test/batchtest-importset.png)

    Chyby při importu oznámí red oznamovací pruh v horní části prohlížeče. Při importu obsahuje chyby, je vytvořena žádná datová sada. Další informace najdete v tématu [běžné chyby](luis-concept-batch-test.md#common-errors-importing-a-batch).

3. V **název datové sady** pole, zadejte název souboru datové sady. Soubor datová sada obsahuje **pole utterances** včetně *s názvem bez přípony záměr* a *entity*. Zkontrolujte [příklad dávkového souboru](luis-concept-batch-test.md#batch-file-format) syntaxe. 

4. Vyberte **provádí**. Soubor datové sady je přidán.

## <a name="run-rename-export-or-delete-dataset"></a>Spuštění, přejmenovat, exportu nebo odstranění datové sady
Pokud chcete spustit, přejmenovat, exportu nebo datovou sadu odstranit, použijte se třemi tečkami (**...** ) na konci řádku datovou sadu.

![Akce datové sady](./media/luis-how-to-batch-test/batch-testing-options.png)

## <a name="run-a-batch-test-on-your-trained-app"></a>Spuštění testu batch na vyškolení aplikace

Pokud chcete spustit test, vyberte název datové sady. Po dokončení testu tento řádek zobrazuje výsledek testu datové sady.

![Výsledek testu batch](./media/luis-how-to-batch-test/run-test.png)

Ke stažení datové sady je stejný soubor, který byl odeslán pro testování batch.

|Stav|Význam|
|--|--|
|![Ikona zeleného kruhu úspěšné testu](./media/luis-how-to-batch-test/batch-test-result-green.png)|Všechny utterances jsou úspěšné.|
|![Červené x ikonu chybě testu](./media/luis-how-to-batch-test/batch-test-result-red.png)|Alespoň jeden utterance záměr neodpovídá předpovědi.|
|![Připraveno k ikona testu](./media/luis-how-to-batch-test/batch-test-result-blue.png)|Test je připraven ke spuštění.|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>
## <a name="view-batch-test-results"></a>Zobrazení výsledků testu batch 
Chcete-li zobrazit výsledky testů batch, vyberte **zobrazte výsledky**.

![Výsledky testu batch](./media/luis-how-to-batch-test/run-test-results.png)

<!--
 Select the **See results** link that appears after you run the test. A scatter graph known as an error matrix displays. The data points represent the utterances in the dataset. 

Green points indicate correct prediction, and red ones indicate incorrect prediction.

The filtering panel on the right side of the screen displays a list of all intents and entities in the app, with a green point for intents/entities that were predicted correctly in all dataset utterances, and a red point for those items with errors. Also, for each intent/entity, you can see the number of correct predictions out of the total utterances.

-->


<a name="filter-chart-results-by-intent-or-entity"></a> ## Filtrování výsledků grafu

Pokud chcete filtrovat grafu konkrétní záměr nebo entity, vyberte v pravé straně panelu filtrování záměr nebo entity. Datové body a jejich aktualizace v tomto grafu podle vašeho výběru. 
 
![Výsledek testu vizualizovaných Batch](./media/luis-how-to-batch-test/filter-by-entity.png) 

<!--
## Investigate false sections
Data points on the **[False Positive][false-positive]** and **[False Negative][false-negative]** sections indicate errors, which should be investigated. If all data points are on the **[True Positive][true-positive]** and **[True Negative][true-negative]** sections, then your application's performance is perfect on this dataset.


The graph indicates [F-measure][f-measure], [recall][recall], and [precision][precision].  
-->
## <a name="view-single-point-utterance-data"></a>Jeden bod utterance data zobrazení
V grafu najeďte myší na datový bod zobrazíte jistoty skóre jeho předpovědi. Vyberte datový bod k načtení jeho odpovídající utterance v seznamu utterances v dolní části stránky. 

![Vybrané utterance](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>
## <a name="view-section-data"></a>Část dat zobrazení
V části čtyři grafu, vyberte název oddílu **False kladné** v pravé horní grafu. Pod grafem zobrazit všechny utterances v této části pod grafu v seznamu. 

![Vybrané utterances oddílu](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

Na předchozím obrázku utterance `switch on` s názvem bez přípony je záměrem použití TurnAllOn však obdržela předpovědi žádná záměrné. Toto je to znamenat, že záměr TurnAllOn potřebuje další příklad utterances aby očekávaný předpovědi. 

Dva oddíly grafu červeně znamenat utterances, které neodpovídá očekávané předpovědi. Ty naznačují utterances které LEOŠ potřebuje další školení. 

Dva oddíly grafu zeleně odpovídají očekávané předpovědi.

## <a name="next-steps"></a>Další postup

Pokud testování uvádí, že aplikace LEOŠ nerozpoznal správné záměry a entity, můžete pracovat ke zlepšení výkonu aplikace LEOŠ označování další utterances nebo přidáním funkcí. 

* [Označení navrhované utterances s LEOŠ](Label-Suggested-Utterances.md) 
* [Funkce lze použít ke zlepšení výkonu aplikace LEOŠ](luis-how-to-add-features.md) 
* [Pochopení batch testování v tomto kurzu](luis-tutorial-batch-testing.md)
* [Další dávky testování koncepce](luis-concept-batch-test.md).

[true-positive]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#true-positive
[true-negative]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#true-negative
[false-positive]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#false-positive
[false-negative]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#false-negative
[f-measure]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#f-measure
[recall]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#recall
[precision]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#precision


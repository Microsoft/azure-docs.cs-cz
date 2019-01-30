---
title: Testovací služby batch
titleSuffix: Language Understanding - Azure Cognitive Services
description: Pomocí služby batch Language Understanding (LUIS) testování sad vyhledejte projevy s nesprávnou záměry a entity.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: dcc5e463eab6c5de612df45963f160156315a34a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55213221"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Testování pomocí sady projevy příklad služby batch

 Testování služby batch je komplexní test na aktuální trénovaný model pro měření jejího výkonu v LUIS. Sady dat použít pro testování služby batch by neměl obsahovat příklad projevy záměry nebo projevy přijatých z koncového bodu modulu runtime předpovědi. 

<a name="batch-testing"></a>

## <a name="import-a-dataset-file-for-batch-testing"></a>Importovat datovou sadu pro testování služby batch

1. Vyberte **testovací** v horní části panelu a pak vyberte **Batch testování panel**.

    ![Testování propojení služby batch](./media/luis-how-to-batch-test/batch-testing-link.png)

2. Vyberte **datové sady importu**. **Nová datová sada Import** zobrazí se dialogové okno. Vyberte **zvolit soubor** a vyhledejte soubor JSON se správnými [formátu JSON](luis-concept-batch-test.md#batch-file-format) obsahující *více než 1 000* projevy k testování.

    Chyby importu jsou hlášeny v red oznamovací pruh v horní části stránky prohlížeče. Při importu obsahuje chyby, žádná datová sada se vytvoří. Další informace najdete v tématu [běžné chyby](luis-concept-batch-test.md#common-errors-importing-a-batch).

3. V **název datové sady** pole, zadejte název pro soubor datové sady. Zahrnuje soubor datové sady **pole projevy** včetně *označené záměr* a *entity*. Zkontrolujte [příklad dávkový soubor](luis-concept-batch-test.md#batch-file-format) syntaxi. 

4. Vyberte **Done** (Hotovo). Přidá soubor datové sady.

## <a name="run-rename-export-or-delete-dataset"></a>Spuštění, přejmenování, export nebo odstranění datové sady

Chcete-li spustit, přejmenovat, export nebo odstranění datové sady, použijte na tři tečky (***...*** ) tlačítko na konci řádku datové sady.

![Akce datové sady](./media/luis-how-to-batch-test/batch-testing-options.png)

## <a name="run-a-batch-test-on-your-trained-app"></a>Spuštění testu služby batch v aplikaci trénovaného

Pro spuštění testu, vyberte název datové sady. Po dokončení testu, tento řádek zobrazuje výsledek testu datové sady.

![Výsledek testu služby batch](./media/luis-how-to-batch-test/run-test.png)

Ke stažení datová sada je stejný soubor, který byl nahrán pro testování služby batch.

|Stav|Význam|
|--|--|
|![Ikona zeleném kroužku úspěšných testů](./media/luis-how-to-batch-test/batch-test-result-green.png)|Všechny projevy jsou úspěšné.|
|![Červené x ikonu selhání testu](./media/luis-how-to-batch-test/batch-test-result-red.png)|Alespoň jeden utterance záměr neodpovídá do predikce.|
|![Jste připravení ikona testu](./media/luis-how-to-batch-test/batch-test-result-blue.png)|Test je připraven ke spuštění.|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

## <a name="view-batch-test-results"></a>Zobrazení výsledků testu služby batch 

Chcete-li zkontrolovat výsledky testů služby batch, vyberte **zobrazit výsledky**.

![Výsledky testu služby batch](./media/luis-how-to-batch-test/run-test-results.png)

<a name="filter-chart-results-by-intent-or-entity"></a>  

## <a name="filter-chart-results"></a>Filtrování grafu výsledků

Filtrování grafu specifické cílem nebo entity, vyberte na pravé straně panelu filtrování záměr nebo entity. Datové body a jejich distribuci aktualizací v grafu podle vašeho výběru. 
 
![Výsledek testu vizualizovaný služby Batch](./media/luis-how-to-batch-test/filter-by-entity.png) 

## <a name="view-single-point-utterance-data"></a>Zobrazení jednoho bodu utterance dat

V diagramu najeďte myší na datový bod zobrazíte skóre jistoty jeho předpovědí. Vyberte datový bod k načtení jeho odpovídajícího utterance v seznamu projevy v dolní části stránky. 

![Vybrané utterance](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

## <a name="view-section-data"></a>Část dat zobrazení

V grafu čtyři části, vyberte název oddílu, jako je například **falešně pozitivní** v pravém horním rohu grafu. Pod grafem všechny projevy v této části zobrazí pod grafem v seznamu. 

![Vybrané projevy oddílu](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

Na předchozím obrázku utterance `switch on` označená s cílem TurnAllOn však obdržela předpovědi none záměru. Toto je jako ukazatel toho, že cílem TurnAllOn potřebuje další příklad projevy používat, aby očekávané předpověď. 

Dva oddíly grafu červeně označení projevy, které neodpovídá očekávanému předpovědi. Tyto zprávy značí projevy které LUIS potřebuje další školení. 

Dva oddíly grafu zeleně odpovídají očekávané předpovědi.

## <a name="next-steps"></a>Další postup

Pokud testování označuje, že aplikace LUIS nedokáže rozpoznat správný záměry a entity, můžete pracovat ke zlepšení výkonu vaší aplikace LUIS označování další projevy nebo přidáním funkcí. 

* [Popisek navrhované projevy služba luis](luis-how-to-review-endoint-utt.md) 
* [Použití funkcí pro zvýšení výkonu aplikace LUIS](luis-how-to-add-features.md) 
* [Vysvětlení služby batch testování v tomto kurzu](luis-tutorial-batch-testing.md)
* [Další dávky testování konceptů](luis-concept-batch-test.md).

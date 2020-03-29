---
title: Jak provést dávkový test - LUIS
titleSuffix: Azure Cognitive Services
description: Pomocí sad dávkového testování language understanding (LUIS) vyhledejte projevy s nesprávnými záměry a entitami.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: bfef7eae7158a05b09a3534e8fb44335333d8cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73904353"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Dávkové testování se sadou příkladových promluv

 Dávkové testování je komplexní test na aktuální trénovaný model pro měření jeho výkonu v LUIS. Datové sady používané pro dávkové testování by neměly obsahovat ukázkové projevy v záměrech nebo projevech přijatých z koncového bodu běhu předpověď. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

<a name="batch-testing"></a>

## <a name="import-a-dataset-file-for-batch-testing"></a>Import souboru datové sady pro dávkové testování

1. V horní liště vyberte **Testovat** a pak vyberte **Panel testování dávky**.

    ![Odkaz na dávkové testování](./media/luis-how-to-batch-test/batch-testing-link.png)

2. Vyberte **Importovat datovou sadu**. Zobrazí se dialogové okno **Importovat novou datovou sadu.** Vyberte **Zvolit soubor** a vyhledejte soubor JSON se správným [formátem JSON,](luis-concept-batch-test.md#batch-file-format) který neobsahuje *více než 1 000* promluv, které chcete otestovat.

    Chyby importu jsou hlášeny v červeném oznamovacím pruhu v horní části prohlížeče. Pokud import má chyby, je vytvořena žádná datová sada. Další informace naleznete v [tématu Běžné chyby](luis-concept-batch-test.md#common-errors-importing-a-batch).

3. Do pole **Název datové sady** zadejte název souboru datové sady. Soubor datové sady obsahuje **pole promluv,** včetně *označeného záměru* a *entit*. Zkontrolujte [ukázkový dávkový soubor](luis-concept-batch-test.md#batch-file-format) pro syntaxi. 

4. Vyberte **Done** (Hotovo). Soubor datové sady je přidán.

## <a name="run-rename-export-or-delete-dataset"></a>Spuštění, přejmenování, export nebo odstranění datové sady

Chcete-li datovou sadu spustit, přejmenovat, exportovat nebo odstranit, použijte tlačítko se třemi tečkami (***...***) na konci řádku datové sady.

![Akce datové sady](./media/luis-how-to-batch-test/batch-testing-options.png)

## <a name="run-a-batch-test-on-your-trained-app"></a>Spuštění dávkového testu v trénované aplikaci

Chcete-li spustit test, vyberte název datové sady. Po dokončení testu se tento řádek zobrazí výsledek testu datové sady.

![Výsledek dávkového testu](./media/luis-how-to-batch-test/run-test.png)

Datová sada ke stažení je stejný soubor, který byl odeslán pro dávkové testování.

|Stav|Význam|
|--|--|
|![Ikona zeleného kruhu úspěšného testu](./media/luis-how-to-batch-test/batch-test-result-green.png)|Všechny projevy jsou úspěšné.|
|![Ikona testu červená x se lhaly](./media/luis-how-to-batch-test/batch-test-result-red.png)|Alespoň jeden záměr utterance neodpovídá předpověď.|
|![Ikona připravenosti k testování](./media/luis-how-to-batch-test/batch-test-result-blue.png)|Test je připraven ke spuštění.|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

## <a name="view-batch-test-results"></a>Zobrazit výsledky dávkových testů 

Chcete-li zkontrolovat výsledky dávkových testů, vyberte **zobrazit výsledky**.

![Výsledky dávkových zkoušek](./media/luis-how-to-batch-test/run-test-results.png)

<a name="filter-chart-results-by-intent-or-entity"></a>  

## <a name="filter-chart-results"></a>Filtrování výsledků grafu

Chcete-li graf filtrovat podle konkrétního záměru nebo entity, vyberte záměr nebo entitu v panelu filtrování na pravé straně. Datové body a jejich distribuce se aktualizují v grafu podle vašeho výběru. 
 
![Vizualizovaný výsledek dávkového testu](./media/luis-how-to-batch-test/filter-by-entity.png) 

## <a name="view-single-point-utterance-data"></a>Zobrazení dat utterance s jedním bodem

V grafu najeďte nad datový bod a znázorte skóre jistoty jeho předpovědi. Vyberte datový bod pro načtení jeho odpovídající utterance v seznamu projevy v dolní části stránky. 

![Vybraná promluva](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

## <a name="view-section-data"></a>Zobrazit data řezu

V grafu se čtyřmi oddíly vyberte název oddílu, například **Falešně pozitivní,** v pravém horním rohu grafu. Pod grafem se všechny projevy v této části zobrazí pod grafem v seznamu. 

![Vybrané projevy podle oddílů](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

V tomto předchozím obrázku `switch on` utterance je označen turnallon záměru, ale obdržel předpověď Žádný záměr. Toto je označení, že TurnAllOn záměr potřebuje další příklad projevy, aby se očekávané předpověď. 

Dvě části grafu červeně označují projevy, které neodpovídají očekávané předpovědi. Tyto označují projevy, které LUIS potřebuje další školení. 

Dvě části grafu v zelené mj.

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Další kroky

Pokud testování znamená, že vaše aplikace LUIS nerozpozná správné záměry a entity, můžete pracovat na zlepšení výkonu aplikace LUIS označením další projevy nebo přidáním funkcí. 

* [Popisek navrhované projevy s LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Vylepšování výkonu aplikace LUIS pomocí funkcí](luis-how-to-add-features.md) 
* [Principy dávkového testování pomocí tohoto kurzu](luis-tutorial-batch-testing.md)
* [Seznamte se s koncepty dávkového testování](luis-concept-batch-test.md).

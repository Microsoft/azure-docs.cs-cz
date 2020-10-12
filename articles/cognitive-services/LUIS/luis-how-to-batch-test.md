---
title: Postup provedení dávkového testu – LUIS
titleSuffix: Azure Cognitive Services
description: Pomocí Language Understanding (LUIS) Batch test Sets Najděte projevy s nesprávnými záměry a entitami.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: 8b34005f2796403e32b41a93e4163c7da16d40bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540944"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Dávkové testování se sadou příkladů projevy

 Dávkovým testováním je komplexní test na vašem aktuálně vyškolený model pro měření výkonu v LUIS. Datové sady, které se používají pro dávkové testování, by neměly zahrnovat příklad projevy v záměrech nebo projevy přijaté z koncového bodu modulu runtime předpovědi.

<a name="batch-testing"></a>

## <a name="import-a-dataset-file-for-batch-testing"></a>Import souboru datové sady pro dávkové testování

1. V horním panelu vyberte **test** a pak vyberte **panel Batch Testing**.

    ![Odkaz na dávkové testování](./media/luis-how-to-batch-test/batch-testing-link.png)

2. Vyberte **importovat datovou sadu**. Zobrazí se dialogové okno **importovat novou datovou sadu** . Vyberte **zvolit soubor** a vyhledejte soubor JSON se správným [formátem JSON](luis-concept-batch-test.md#batch-file-format) , který neobsahuje *více než 1 000* projevy pro testování.

    Chyby importu jsou hlášeny v červeném oznamovacím panelu v horní části okna prohlížeče. Pokud import obsahuje chyby, není vytvořena žádná datová sada. Další informace najdete v tématu [běžné chyby](luis-concept-batch-test.md#common-errors-importing-a-batch).

3. Do pole **název datové sady** zadejte název souboru DataSet. Soubor DataSet obsahuje **pole projevy, které** zahrnuje *označení záměru* a *entit*. Projděte si [ukázkový dávkový soubor](luis-concept-batch-test.md#batch-file-format) pro syntaxi.

4. Vyberte **Hotovo**. Přidá se soubor DataSet.

## <a name="run-rename-export-or-delete-dataset"></a>Spustit, přejmenovat, exportovat nebo odstranit datovou sadu

Chcete-li datovou sadu spustit, přejmenovat, exportovat nebo odstranit, použijte tlačítko se třemi tečkami (***...***) na konci řádku DataSet.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky se seznamem dávkových testů s možnostmi](./media/luis-how-to-batch-test/batch-testing-options.png)

## <a name="run-a-batch-test-on-your-trained-app"></a>Spuštění testu dávky na školenou aplikaci

Chcete-li spustit test, vyberte název datové sady a pak vyberte **Spustit** z kontextového panelu nástrojů. Po dokončení testu tento řádek zobrazí výsledek testu pro datovou sadu.

Datová sada ke stažení je stejný soubor, který byl nahrán pro dávkové testování.

|State|Význam|
|--|--|
|![Ikona úspěšného testu zeleného kruhu](./media/luis-how-to-batch-test/batch-test-result-green.png)|Všechny projevy jsou úspěšné.|
|![Neúspěšná ikona testu červená x](./media/luis-how-to-batch-test/batch-test-result-red.png)|Nejméně jeden utterance záměr neodpovídal předpovědi.|
|![Ikona připraveno k otestování](./media/luis-how-to-batch-test/batch-test-result-blue.png)|Test je připravený ke spuštění.|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

## <a name="view-batch-test-results"></a>Zobrazit výsledky dávkového testu

Chcete-li zkontrolovat výsledky dávkového testu, vyberte možnost **Zobrazit výsledky**.

<a name="filter-chart-results-by-intent-or-entity"></a>

## <a name="filter-chart-results"></a>Filtrovat výsledky grafu

Pokud chcete graf filtrovat podle konkrétního záměru nebo entity, vyberte záměr nebo entitu na panelu filtrování na pravé straně. Datové body a jejich distribuce se aktualizují v grafu podle vašeho výběru.

![Vizuální výsledek testu dávky](./media/luis-how-to-batch-test/filter-by-entity.png)

## <a name="view-single-point-utterance-data"></a>Zobrazit utterance data s jedním bodem

V grafu umístěte ukazatel myši na datový bod, abyste viděli hodnocení jistoty jeho předpovědi. V seznamu projevy v dolní části stránky vyberte datový bod, pro který chcete načíst odpovídající utterance.

![Vybrané utterance](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

## <a name="view-section-data"></a>Zobrazit data oddílu

V grafu se čtyřmi oddíly vyberte název oddílu, například **falešně pozitivní** v pravém horním rohu grafu. Pod grafem se všechny projevy v této části zobrazí pod grafem v seznamu.

![Vybrané projevy podle oddílu](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

V tomto předchozím obrázku `switch on` je utterance označený jako TurnAllOn záměr, ale dostal předpověď žádného záměru. Jedná se o indikaci, že záměr TurnAllOn potřebuje další příklad projevy, aby bylo možné provést očekávanou předpověď.

Dva části grafu červeně označují projevy, které neodpovídaly očekávané předpovědi. Označují projevy, které LUIS potřebují více školení.

Dva oddíly v grafu zeleně odpovídaly očekávané předpovědi.

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Další kroky

Pokud testování indikuje, že vaše aplikace LUIS nerozpozná správné záměry a entity, můžete pracovat na zlepšení výkonu vaší aplikace LUIS tak, že se přihlásíte další projevy nebo přidáte funkce.

* [Popisek navržený projevy pomocí LUIS](luis-how-to-review-endpoint-utterances.md)
* [Využijte funkce ke zlepšení výkonu aplikace v LUIS.](luis-how-to-add-features.md)
* [Vysvětlení dávkového testování pomocí tohoto kurzu](luis-tutorial-batch-testing.md)
* [Přečtěte si o konceptech dávkového testování](luis-concept-batch-test.md).

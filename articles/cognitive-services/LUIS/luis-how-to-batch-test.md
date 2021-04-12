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
ms.date: 12/29/2020
ms.openlocfilehash: b297330f3562babf9e83d36934827f7b92d5ea35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "98787008"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Dávkové testování se sadou příkladů projevy

Dávkové testování ověřuje vaši aktivní výukovou verzi, aby měřila její přesnost předpovědi. Batch test vám pomůže zobrazit přesnost každého záměru a entity v aktivní verzi. Prohlédněte si výsledky dávkových testů, abyste měli vhodná opatření ke zlepšení přesnosti, jako je například přidání dalších příkladů projevy k záměru, pokud se aplikace často nedaří identifikovat správné záměry nebo označení entit v rámci utterance.

## <a name="group-data-for-batch-test"></a>Seskupit data pro dávkový test

Je důležité, aby se projevy, které se používají pro dávkové testování, LUIS. Pokud máte datovou sadu projevy, rozdělte projevy na tři sady: příklad projevy přidané k záměru, projevy přijatý od publikovaného koncového bodu a projevy, který se používá k dávkovému testování LUIS po jeho školení.

Dávkový soubor JSON, který použijete, by měl obsahovat projevy s entitami strojového učení nejvyšší úrovně, které jsou označené jako počáteční a koncová pozice. Projevy by neměl být součástí příkladů, které už jsou v aplikaci. Měly by být projevy, že chcete pozitivní předpověď záměru a entit.

Testy můžete oddělit podle záměru nebo entity nebo mít všechny testy (až 1000 projevy) ve stejném souboru. 

### <a name="common-errors-importing-a-batch"></a>Běžné chyby při importu dávky

Pokud narazíte na chyby při nahrávání dávkového souboru do LUIS, podívejte se na následující běžné problémy:

* Více než 1 000 projevy v dávkovém souboru
* Objekt utterance JSON, který nemá vlastnost Entities. Vlastnost může být prázdné pole.
* Word (y) označený v několika entitách
* Popisky entit začínají nebo končí mezerou.

## <a name="fixing-batch-errors"></a>Oprava chyb dávky

Pokud dojde k chybám v dávkovém testování, můžete přidat více projevy k záměru a/nebo označit více projevy s entitou, která bude pomáhat LUIS učinit diskriminaci mezi záměry. Pokud jste přidali projevy a přiřadíte je a stále máte chyby předpovědi při dávkovém testování, zvažte přidání funkce [seznamu frází](luis-concept-feature.md) s slovníkem specifickým pro doménu, aby se Luis rychleji dozvěděly.


<a name="batch-testing"></a>

## <a name="batch-testing-using-the-luis-portal"></a>Dávkové testování pomocí portálu LUIS 

### <a name="import-and-train-an-example-app"></a>Import a výuka ukázkové aplikace

Importujte aplikaci, která používá Pizza pořadí, například `1 pepperoni pizza on thin crust` .

1.  Stáhněte si [soubor JSON aplikace](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true) a uložte si ho.

1. Přihlaste se k [portálu Luis](https://www.luis.ai)a vyberte své **předplatné** a **prostředek pro vytváření obsahu** , abyste viděli aplikace přiřazené k tomuto zdrojovému prostředku.
1. Vyberte šipku vedle **nové aplikace** a kliknutím na **importovat jako JSON** importujte JSON do nové aplikace. Pojmenujte aplikaci `Pizza app` .


1. Vyberte **vlak** v pravém horním rohu navigace a prohlaste aplikaci.


[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

### <a name="batch-test-file"></a>Soubor dávkového testu

Příklad JSON obsahuje jeden utterance s označenou entitou pro ilustraci toho, co soubor testu vypadá. Ve vašich vlastních testech byste měli mít spoustu projevy se správným záměrem a entitou strojového učení s názvem.

1. Vytvořte `pizza-with-machine-learned-entity-test.json` v textovém editoru nebo [si ho stáhněte](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true) .

2. V dávkovém souboru ve formátu JSON přidejte utterance s **záměrem** , který chcete odhadnout v testu.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>Spustit dávku

1. V horním navigačním panelu vyberte **test** .

2. Na panelu na pravé straně vyberte **panel dávkové testování** .

    ![Odkaz na dávkové testování](./media/luis-how-to-batch-test/batch-testing-link.png)

3. Vyberte **Importovat**. V dialogovém okně, které se zobrazí, vyberte **zvolit soubor** a vyhledejte soubor JSON se správným formátem JSON, který neobsahuje *více než 1 000* projevy pro testování.

    Chyby importu jsou hlášeny v červeném oznamovacím panelu v horní části okna prohlížeče. Pokud import obsahuje chyby, není vytvořena žádná datová sada. Další informace najdete v tématu [běžné chyby](#common-errors-importing-a-batch).

4. Vyberte umístění souboru `pizza-with-machine-learned-entity-test.json` .

5. Pojmenujte datovou sadu `pizza test` a vyberte **Hotovo**.

6. Vyberte tlačítko **Run** (Spustit). Po spuštění testu dávky vyberte **Zobrazit výsledky**. 

    > [!TIP]
    > * Když vyberete **Stáhnout** , stáhne se stejný soubor, který jste nahráli.
    > * Pokud se vám test dávky nepovedl, minimálně jeden záměr utterance se neshoduje s předpovědí.

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

### <a name="review-batch-results-for-intents"></a>Kontrola výsledků dávky pro záměry

Chcete-li zkontrolovat výsledky dávkového testu, vyberte možnost **Zobrazit výsledky**. Výsledky testu ukazují, jak byl projevy testů předpovězený proti aktivní verzi.

V dávkovém grafu se zobrazí čtyři kvadranty výsledků. Napravo od grafu je filtr. Filtr obsahuje záměry a entity. Když vyberete [část grafu](#review-batch-results-for-intents) nebo bod v rámci grafu, zobrazí se v grafu odpovídající utterance (y).

Při najetí myší na graf může kolečko myši zvětšit nebo zmenšit displej v grafu. To je užitečné v případě, že je graf v grafu seskupen těsně dohromady.

Graf je ve čtyřech kvadrantech, přičemž dva z oddílů se zobrazí červeně.

1. V seznamu filtru vyberte záměr **ModifyOrder** . Utterance je předpovězen jako **skutečný pozitivní** význam, který utterance úspěšně shodoval s jeho pozitivním předpověďm uvedeným v dávkovém souboru.

    > [!div class="mx-imgBorder"]
    > ![Utterance úspěšně odpovídal jeho kladné předpovědi.](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    Zelené zaškrtnutí v seznamu filtry také označují úspěšnost testu pro každý záměr. Všechny ostatní záměry jsou uvedeny s 1/1 kladným skóre, protože utterance byl testován proti každému záměru, jako negativní test pro všechny záměry, které nejsou uvedeny v dávkovém testu.

1. Vyberte **potvrzující** záměr. Tento záměr není uvedený v dávkovém testu, takže se jedná o negativní test utterance, který je uvedený v dávkovém testu.

    > [!div class="mx-imgBorder"]
    > ![Utterance byl úspěšně předpovězený negativ pro neuvedené záměry v dávkovém souboru.](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    Negativní test byl úspěšný, jak bylo uvedeno u zeleného textu ve filtru a v mřížce.

### <a name="review-batch-test-results-for-entities"></a>Kontrola výsledků dávkových testů pro entity

Entita ModifyOrder jako entita počítače s podentitami obsahuje informace o tom, jestli se entita nejvyšší úrovně shodovala s tím, jak jsou dílčí entity předpovězeny.

1. V seznamu filtrovat vyberte entitu **ModifyOrder** a pak vyberte kruh v mřížce.

1. Předpověď entity se zobrazí pod grafem. Zobrazení obsahuje plné čáry pro předpovědi, které odpovídají očekávanému a tečkovanému řádku pro předpovědi, které neodpovídají očekávání.

    > [!div class="mx-imgBorder"]
    > ![Nadřazená entita entity byla úspěšně předpovězena v dávkovém souboru.](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

<a name="filter-chart-results-by-intent-or-entity"></a>

#### <a name="filter-chart-results"></a>Filtrovat výsledky grafu

Pokud chcete graf filtrovat podle konkrétního záměru nebo entity, vyberte záměr nebo entitu na panelu filtrování na pravé straně. Datové body a jejich distribuce se aktualizují v grafu podle vašeho výběru.

![Vizuální výsledek testu dávky](./media/luis-how-to-batch-test/filter-by-entity.png)

### <a name="chart-result-examples"></a>Příklady výsledků grafu

Graf na portálu LUIS můžete provádět následující akce:
 
#### <a name="view-single-point-utterance-data"></a>Zobrazit utterance data s jedním bodem

V grafu umístěte ukazatel myši na datový bod, abyste viděli hodnocení jistoty jeho předpovědi. V seznamu projevy v dolní části stránky vyberte datový bod, pro který chcete načíst odpovídající utterance.

![Vybrané utterance](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

#### <a name="view-section-data"></a>Zobrazit data oddílu

V grafu se čtyřmi oddíly vyberte název oddílu, například **falešně pozitivní** v pravém horním rohu grafu. Pod grafem se všechny projevy v této části zobrazí pod grafem v seznamu.

![Vybrané projevy podle oddílu](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

V tomto předchozím obrázku `switch on` je utterance označený jako TurnAllOn záměr, ale dostal předpověď žádného záměru. Jedná se o indikaci, že záměr TurnAllOn potřebuje další příklad projevy, aby bylo možné provést očekávanou předpověď.

Dva části grafu červeně označují projevy, které neodpovídaly očekávané předpovědi. Označují projevy, které LUIS potřebují více školení.

Dva oddíly v grafu zeleně odpovídaly očekávané předpovědi.

## <a name="batch-testing-using-the-rest-api"></a>Dávkové testování pomocí REST API 

LUIS umožňuje dávkové testování pomocí portálu LUIS a REST API. Koncové body pro REST API jsou uvedeny níže. Informace o dávkovém testování pomocí portálu LUIS najdete v tématu [kurz: dávkové test sady dat](). Použijte níže uvedené úplné adresy URL a nahraďte zástupné hodnoty vlastním klíčem předpovědi LUIS a koncovým bodem. 

Nezapomeňte do hlavičky přidat svůj klíč LUIS `Ocp-Apim-Subscription-Key` a nastavit `Content-Type` na `application/json` .

### <a name="start-a-batch-test"></a>Spuštění dávkového testu

Spusťte test dávky pomocí ID verze aplikace nebo slotu publikování. Odešle požadavek **post** do jednoho z následujících formátů koncového bodu. Do těla žádosti zahrňte soubor Batch.

Slot pro publikování
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-NAME>/evaluations`

ID verze aplikace
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations`

Tyto koncové body vrátí ID operace, kterou použijete ke kontrole stavu a získání výsledků. 


### <a name="get-the-status-of-an-ongoing-batch-test"></a>Získání stavu průběžného testu dávky

Použijte ID operace z dávkového testu, které jste spustili, abyste získali svůj stav z následujících formátů koncových bodů: 

Slot pro publikování
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/status`

ID verze aplikace
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/status`

### <a name="get-the-results-from-a-batch-test"></a>Získání výsledků z dávkového testu

Pomocí ID operace z dávkového testu, který jste začali, získáte výsledky z následujících formátů koncových bodů: 

Slot pro publikování
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/result`

ID verze aplikace
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/result`


### <a name="batch-file-of-utterances"></a>Dávkový soubor pro projevy

Odeslání dávkového souboru projevy, který se označuje jako *datová sada*, pro dávkové testování. Datová sada je soubor ve formátu JSON, který obsahuje maximálně 1 000 označených projevy. V aplikaci můžete testovat až 10 datových sad. Pokud potřebujete vyzkoušet víc, odstraňte datovou sadu a pak přidejte novou. Všechny vlastní entity v modelu se zobrazí v rámci filtru entity testu dávky i v případě, že v datech dávkového souboru nejsou žádné odpovídající entity.

Dávkový soubor se skládá z projevy. Každý utterance musí mít očekávanou předpověď záměru spolu se všemi [entitami strojového učení](luis-concept-entity-types.md#types-of-entities) , které se mají zjistit.

### <a name="batch-syntax-template-for-intents-with-entities"></a>Šablona syntaxe služby Batch pro záměry s entitami

K spuštění dávkového souboru použijte následující šablonu:

```JSON
{
    "LabeledTestSetUtterances": [
        {
            "text": "play a song",
            "intent": "play_music",
            "entities": [
                {
                    "entity": "song_parent",
                    "startPos": 0,
                    "endPos": 15,
                    "children": [
                        {
                            "entity": "pre_song",
                            "startPos": 0,
                            "endPos": 3
                        },
                        {
                            "entity": "song_info",
                            "startPos": 5,
                            "endPos": 15
                        }
                    ]
                }
            ]
        }
    ]
}

```

Dávkový soubor používá vlastnosti **startPos** a **endpos** , které označují začátek a konec entity. Hodnoty jsou počítány od nuly a neměly by začínat ani končit mezerou. To se liší od protokolů dotazů, které používají vlastnosti startIndex a hodnota endIndex.

Pokud nechcete testovat entity, zahrňte `entities` vlastnost a nastavte hodnotu jako prázdné pole, `[]` .

### <a name="rest-api-batch-test-results"></a>Výsledky REST API dávky testu

Rozhraní API vrátilo několik objektů:

* Informace o záměrech a modelech entit, jako je přesnost, odvolání a F-skore.
* Informace o modelech entit, jako je například Precision, Recall a F-skore) pro každou entitu 
  * Pomocí `verbose` příznaku můžete získat další informace o entitě, například `entityTextFScore` a `entityTypeFScore` .
* Poskytnuté projevy s předpokládanými a označenými názvy záměrů
* Seznam falešně pozitivních entit a seznam falešně negativních entit.

## <a name="next-steps"></a>Další kroky

Pokud testování indikuje, že vaše aplikace LUIS nerozpozná správné záměry a entity, můžete pracovat na zlepšení výkonu vaší aplikace LUIS tak, že se přihlásíte další projevy nebo přidáte funkce.

* [Popisek navržený projevy pomocí LUIS](luis-how-to-review-endpoint-utterances.md)
* [Využijte funkce ke zlepšení výkonu aplikace v LUIS.](luis-how-to-add-features.md)

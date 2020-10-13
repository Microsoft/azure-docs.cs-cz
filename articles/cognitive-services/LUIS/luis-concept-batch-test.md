---
title: Dávkové testování – LUIS
titleSuffix: Azure Cognitive Services
description: Pomocí dávkového testování můžete u své aplikace průběžně pracovat a zdokonalit jejich porozumění jazyku.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: f3a8f5ef8119d9895f67e07ea1b68c660be59f9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541862"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>Dávkové testování s 1000 projevy na portálu LUIS

Dávkové testování ověřuje vaši aktivní výukovou verzi, aby měřila její přesnost předpovědi. Batch test vám pomůže zobrazit přesnost každého záměru a entity v aktivní verzi a zobrazovat výsledky pomocí grafu. Prohlédněte si výsledky dávkových testů, abyste měli vhodná opatření ke zlepšení přesnosti, jako je například přidání dalších příkladů projevy k záměru, pokud se aplikace často nedaří identifikovat správné záměry nebo označení entit v rámci utterance.

## <a name="group-data-for-batch-test"></a>Seskupit data pro dávkový test

Je důležité, aby se projevy, které se používají pro dávkové testování, LUIS. Pokud máte datovou sadu projevy, rozdělte projevy na tři sady: příklad projevy přidané k záměru, projevy přijatý od publikovaného koncového bodu a projevy, který se používá k dávkovému testování LUIS po jeho školení.

## <a name="a-data-set-of-utterances"></a>Sada dat projevy

Odeslání dávkového souboru projevy, který se označuje jako *datová sada*, pro dávkové testování. Datová sada je soubor ve formátu JSON, který obsahuje maximálně 1 000 s označením, že **není duplicitní** projevy. V aplikaci můžete testovat až 10 datových sad. Pokud potřebujete vyzkoušet víc, odstraňte datovou sadu a pak přidejte novou.

|**Pravidla**|
|--|
|* Žádné duplicitní projevy|
|1000 projevy nebo méně|

* Duplicity se považují za přesné shody řetězců, nikoli shody, které jsou nejprve zařazeny do tokenu.

## <a name="entities-allowed-in-batch-tests"></a>Entity povolené v dávkových testech

Všechny vlastní entity v modelu se zobrazí v rámci filtru entity testu dávky i v případě, že v datech dávkového souboru nejsou žádné odpovídající entity.

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>

## <a name="batch-file-format"></a>Formát dávkového souboru

Dávkový soubor se skládá z projevy. Každý utterance musí mít očekávanou předpověď záměru spolu se všemi [entitami strojového učení](luis-concept-entity-types.md#types-of-entities) , které se mají zjistit.

## <a name="batch-syntax-template-for-intents-with-entities"></a>Šablona syntaxe služby Batch pro záměry s entitami

K spuštění dávkového souboru použijte následující šablonu:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities":
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

Dávkový soubor používá vlastnosti **startPos** a **endpos** , které označují začátek a konec entity. Hodnoty jsou počítány od nuly a neměly by začínat ani končit mezerou. To se liší od protokolů dotazů, které používají vlastnosti startIndex a hodnota endIndex.

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="batch-syntax-template-for-intents-without-entities"></a>Šablona syntaxe služby Batch pro záměry bez entit

K spuštění dávkového souboru bez entit použijte následující šablonu:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": []
  }
]
```

Pokud nechcete testovat entity, zahrňte `entities` vlastnost a nastavte hodnotu jako prázdné pole, `[]` .


## <a name="common-errors-importing-a-batch"></a>Běžné chyby při importu dávky

Mezi běžné chyby patří:

> * Více než 1 000 projevy
> * Objekt utterance JSON, který nemá vlastnost Entities. Vlastnost může být prázdné pole.
> * Word (y) označený v několika entitách
> * Popisek entity začínající nebo končící mezerou

## <a name="batch-test-state"></a>Stav testu dávky

LUIS sleduje stav posledního testu sady dat. To zahrnuje velikost (počet projevy v dávce), datum posledního spuštění a poslední výsledek (počet úspěšně předpokládaných projevy).

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>Výsledky dávkového testu

Výsledkem dávkového testu je bodový graf, který se označuje jako chybová matice. Tento graf je obousměrné porovnání projevy v dávkovém souboru a předpovězeného záměru a entit aktuálního modelu.

Datové body na **falešně pozitivních** a **falešně negativních** částech označují chyby, které by se měly prozkoumat. Pokud jsou všechny datové body na **true pozitivních** částech a **pravdivé záporné** části, je přesnost vaší aplikace ideální pro tuto datovou sadu.

![Čtyři části grafu](./media/luis-concept-batch-test/chart-sections.png)

Tento graf vám pomůže najít projevy, že LUIS předpověď nesprávně na základě jeho aktuálního školení. Výsledky se zobrazí v jednotlivých oblastech v grafu. Výběrem jednotlivých bodů v grafu si můžete prohlédnout utterance informace nebo vybrat název oblasti a zkontrolovat výsledky utterance v této oblasti.

![Dávkové testování](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Chyby ve výsledcích

Chyby v dávkovém testu označují záměry, které nejsou předvídatelné, jak je uvedeno v dávkovém souboru. Chyby jsou uvedeny v dvou červených částech grafu.

Falešně pozitivní část označuje, že se utterance shodoval s záměrem nebo entitou, pokud by neměla mít. Hodnota false Negative znamená, že utterance neodpovídal záměru nebo entitě, pokud by měla být.

## <a name="fixing-batch-errors"></a>Oprava chyb dávky

Pokud dojde k chybám v dávkovém testování, můžete přidat více projevy k záměru a/nebo označit více projevy s entitou, která bude pomáhat LUIS učinit diskriminaci mezi záměry. Pokud jste přidali projevy a přiřadíte je a stále máte chyby předpovědi při dávkovém testování, zvažte přidání funkce [seznamu frází](luis-concept-feature.md) s slovníkem specifickým pro doménu, aby se Luis rychleji dozvěděly.

## <a name="next-steps"></a>Další kroky

* Postup [testování dávky](luis-how-to-batch-test.md)

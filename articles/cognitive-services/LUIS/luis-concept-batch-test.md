---
title: Dávkové testování – LUIS
titleSuffix: Azure Cognitive Services
description: Pomocí služby batch testování neustále pracovat na aplikaci zpřesnit jej a zvýšit jeho umožňující porozumět jazyku.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: b962fc32cdcde0509cfa60d105022bb208633ae3
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639299"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>Dávkové testování s 1000 projevy na portálu LUIS

Testování služby batch ověří vaše [aktivní](luis-concept-version.md#active-version) trénovaný model pro měření jeho přesnost předpovědi. Batch test vám pomůže zobrazit přesnost každého záměru a entity v aktuálním vyškolený model a zobrazovat výsledky pomocí grafu. Posoudit výsledky testu služby batch a přijmout vhodná opatření zvyšte přesnost, jako je například přidávání další příklad projevy k záměru, pokud vaše aplikace často nepodaří identifikovat správné záměr.

## <a name="group-data-for-batch-test"></a>Data skupiny pro testovací služby batch

Je důležité, že teprve začínáte LUIS projevy, používá se pro testování služby batch. Pokud máte datovou sadu projevy, rozdělte projevy na tři sady: příklad projevy přidané k záměru, projevy přijatý od publikovaného koncového bodu a projevy, který se používá k dávkovému testování LUIS po jeho školení. 

## <a name="a-data-set-of-utterances"></a>Sada dat projevy

Odeslání dávkového souboru projevy, který se označuje jako *datová sada*, pro dávkové testování. Datová sada je soubor ve formátu JSON, který obsahuje maximálně 1 000 s označením, že **není duplicitní** projevy. V aplikaci můžete testovat až 10 datových sad. Pokud potřebujete vyzkoušet víc, odstraňte datovou sadu a pak přidejte novou.

|**pravidla**|
|--|
|\* Žádné duplicitní projevy|
|projevy 1 000 nebo méně|

\* Duplicity, jsou považovány za přesná shody, ne shody, které jsou nejprve tokenizovaného. 

## <a name="entities-allowed-in-batch-tests"></a>Entity, které jsou povolené v testech služby batch

Všechny vlastní entity v modelu se zobrazí ve filtru entity batch test i v případě, že neexistují žádné odpovídající entity v souboru dat dávek.

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>

## <a name="batch-file-format"></a>Formát souboru služby batch

Dávkový soubor se skládá z projevy. Každý utterance musí mít očekávané záměru předpovědi spolu s [zjištěné počítače entity](luis-concept-entity-types.md#types-of-entities) očekáváte, že aby se rozpoznal. 

## <a name="batch-syntax-template-for-intents-with-entities"></a>Šablona syntaxe služby Batch pro záměry s entitami

Pomocí následující šablony můžete spustit dávkový soubor:

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

Dávkový soubor používá **startPos** a **endPos** vlastnosti poznamenat začátek a konec entity. Hodnoty jsou počítány od nuly a nesmí začínat ani končit mezerou. Tím se liší z protokolů dotazu, které používají startIndex a hodnota endIndex vlastnosti. 

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

Pokud nechcete testovat entity, zahrňte `entities` vlastnost a nastavte hodnotu jako prázdné pole,. `[]`


## <a name="common-errors-importing-a-batch"></a>Běžné chyby při importu služby batch

Běžné chyby patří: 

> * Víc než 1 000 projevy
> * Objekt utterance JSON, který nemá na vlastnost entity. Vlastnost může být prázdné pole.
> * Slova popisem více entit
> * Popisek entity od nebo končí mezerou.

## <a name="batch-test-state"></a>Stav testu služby batch

LUIS sleduje stav posledního testu sady dat. To zahrnuje velikost (počet v dávce projevy), poslední spuštění data a výsledek posledního (počet úspěšně předpokládané projevy).

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>Výsledky testu služby batch

Výsledek testu batch je bodový graf, označované jako matici chyby. Tento graf se 4 způsob porovnání projevy dávkový soubor a předpokládané záměr aktuální model a entity. 

Datových bodů na **falešně pozitivní** a **falešně negativní** části označují chyby, které by mělo být vypátráno. Pokud jsou všechny datové body na **true pozitivních** částech a **pravdivé záporné** části, je přesnost vaší aplikace ideální pro tuto datovou sadu.

![Čtyři části grafu](./media/luis-concept-batch-test/chart-sections.png)

Tento graf vám pomůže najít projevy, které předpoví LUIS nesprávně podle jeho aktuální školení. Výsledky se zobrazí v jedné oblasti grafu. Vyberte jednotlivé body v grafu zkontrolujte informace utterance nebo vyberte název oblasti a zkontrolovat výsledky utterance v dané oblasti.

![Dávkové testování](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Ve výsledcích chyby

Chyby v testu batch označují záměrů, které nejsou předpovědět, jak je uvedeno v dávkovém souboru. Chyby jsou uvedené v dvě red části grafu. 

Falešně pozitivní části označuje, že utterance shoda s cílem nebo entity při by neměl mít. Záporné hodnoty false označuje, že že utterance se neshodoval s cílem nebo entity při by měl mít. 

## <a name="fixing-batch-errors"></a>Opravy chyb služby batch

Pokud nejsou chyby při testování služby batch, můžete přidat další projevy záměru, nebo popisek další projevy s entitou umožňující LUIS, ujistěte se, k ní mezi záměry. Pokud jste přidali projevy a s popiskem je a stále get předpovědi chyb při testování služby batch, zvažte přidání [seznam frází](luis-concept-feature.md) funkce, která nabízí slovník jazyka specifického pro doménu umožňující LUIS při učení. 

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [testování dávky](luis-how-to-batch-test.md)

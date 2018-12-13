---
title: Dávkové testování
titleSuffix: Language Understanding - Azure Cognitive Services
description: Pomocí služby batch testování neustále pracovat na aplikaci zpřesnit jej a zvýšit jeho umožňující porozumět jazyku.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: c050c8ed338a019b34302e87a53d4a40306f9a22
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53133142"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>Testování s 1 000 projevy portálu LUIS služby batch

Testování služby batch ověří vaše [aktivní](luis-concept-version.md#active-version) trénovaný model pro měření jeho přesnost předpovědi. Testovací služby batch umožňuje zobrazit aktuální trained model v grafu přesnosti jednotlivých záměr a entity. Posoudit výsledky testu služby batch a přijmout vhodná opatření zvyšte přesnost, jako je například přidávání další příklad projevy k záměru, pokud vaše aplikace často nepodaří identifikovat správné záměr.

## <a name="group-data-for-batch-test"></a>Data skupiny pro testovací služby batch

Je důležité, že teprve začínáte LUIS projevy, používá se pro testování služby batch. Pokud už máte datovou sadu projevy, rozdělit projevy do tří sad: projevy přidá k záměru, projevy přijatých z publikovaných koncového bodu a projevy po jsou trénovaná používat batch testu LUIS. 

## <a name="a-dataset-of-utterances"></a>Datovou sadu projevy

Odeslat soubor batch projevy, označované jako *datovou sadu*, pro účely testování služby batch. Tato datová sada je soubor ve formátu JSON, který obsahuje maximálně 1 000 označené **neduplicitní** projevy. V aplikaci můžete otestovat až 10 datové sady. Pokud potřebujete další testování, odstranění datové sady a pak přidat nový.

|**pravidla**|
|--|
|* Žádné duplicitní projevy|
|projevy 1 000 nebo méně|

* Duplicity, jsou považovány za přesná shody, ne shody, které jsou nejprve tokenizovaného. 

## <a name="entities-allowed-in-batch-tests"></a>Entity, které jsou povolené v testech služby batch

Všechny vlastní entity v modelu se zobrazí ve filtru entity batch test i v případě, že neexistují žádné odpovídající entity v souboru dat dávek.

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>

## <a name="batch-file-format"></a>Formát souboru služby batch

Dávkový soubor se skládá z projevy. Každý utterance musí mít očekávané záměru předpovědi spolu s [zjištěné počítače entity](luis-concept-entity-types.md#types-of-entities) očekáváte, že aby se rozpoznal. 

## <a name="batch-syntax-template"></a>Syntaxe šablony služby batch

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


## <a name="common-errors-importing-a-batch"></a>Běžné chyby při importu služby batch

Běžné chyby patří: 

> * Víc než 1 000 projevy
> * Objekt utterance JSON, který nemá na vlastnost entity. Vlastnost může být prázdné pole.
> * Slova popisem více entit
> * Popisek entity od nebo končí mezerou.

## <a name="batch-test-state"></a>Stav testu služby batch

Služba LUIS sleduje stav poslední sady testů každou datovou sadu. To zahrnuje velikost (počet v dávce projevy), poslední spuštění data a výsledek posledního (počet úspěšně předpokládané projevy).

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>Výsledky testu služby batch

Výsledek testu batch je bodový graf, označované jako matici chyby. Tento graf se 4 způsob porovnání projevy dávkový soubor a předpokládané záměr aktuální model a entity. 

Datových bodů na **falešně pozitivní** a **falešně negativní** části označují chyby, které by mělo být vypátráno. Pokud jsou všechny datové body na **True kladné** a **True negativní** části, je ideální pro tuto datovou sadu přesnost vaší aplikace.

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

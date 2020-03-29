---
title: Testování dávek – LUIS
titleSuffix: Azure Cognitive Services
description: Pomocí dávkového testování můžete průběžně pracovat na aplikaci a vylepšovat ji a zlepšovat její jazykové porozumění.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: e9ad7c52af20762633c710b39a64fbebf0cf6213
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220047"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>Dávkové testování s 1000 projevy na portálu LUIS

Dávkové testování ověřuje vaši aktivní trénované verze pro měření přesnosti predikce. Dávkový test vám pomůže zobrazit přesnost každého záměru a entity v aktivní verzi a zobrazí výsledky pomocí grafu. Zkontrolujte výsledky dávkových testů a provádějte příslušnou akci ke zlepšení přesnosti, jako je například přidání dalších ukázkových promluv k záměru, pokud vaše aplikace často nedokáže identifikovat správné záměr nebo popisné entity v rámci utterance.

## <a name="group-data-for-batch-test"></a>Skupinová data pro dávkový test

Je důležité, aby projevy používané pro dávkové testování jsou nové luis. Pokud máte sadu dat projevy, rozdělte projevy do tří sad: příklad projevy přidány do záměru, projevy přijaté z publikovaného koncového bodu a projevy používané k dávkové testování LUIS po trénování. 

## <a name="a-data-set-of-utterances"></a>Datová sada promluv

Odešlete dávkový soubor promluv, označovaný jako *sada dat*, pro dávkové testování. Sada dat je soubor ve formátu JSON obsahující maximálně 1 000 **popisovaných neduplicitních** projevů. V aplikaci můžete otestovat až 10 sad dat. Pokud potřebujete otestovat více, odstraňte sadu dat a přidejte novou.

|**Pravidla**|
|--|
|*Žádné duplicitní projevy|
|1000 promluv nebo méně|

*Duplikáty jsou považovány za přesné shody řetězců, nikoli za shody, které jsou tokenizovány jako první. 

## <a name="entities-allowed-in-batch-tests"></a>Entity povolené v dávkových testech

Všechny vlastní entity v modelu se zobrazí ve filtru dávkových testovacích entit, i když v datech dávkového souboru nejsou žádné odpovídající entity.

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>

## <a name="batch-file-format"></a>Formát dávkového souboru

Dávkový soubor se skládá z promluv. Každý utterance musí mít očekávané záměr předpověď spolu s všechny počítače naučil entity, které očekáváte, že budou [zjištěny.](luis-concept-entity-types.md#types-of-entities) 

## <a name="batch-syntax-template-for-intents-with-entities"></a>Šablona syntaxe dávky pro záměry s entitami

Ke spuštění dávkového souboru použijte následující šablonu:

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

Dávkový soubor používá **vlastnosti startPos** a **endPos** k poznámce začátku a konce entity. Hodnoty jsou založeny na nule a neměly by začínat ani končit v prostoru. To se liší od protokolů dotazů, které používají vlastnosti startIndex a endIndex. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="batch-syntax-template-for-intents-without-entities"></a>Šablona syntaxe dávky pro záměry bez entit

Ke spuštění dávkového souboru bez entit použijte následující šablonu:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": []
  }
]
```

Pokud nechcete testovat entity, zahrňte `entities` vlastnost a nastavte hodnotu jako prázdné pole. `[]`


## <a name="common-errors-importing-a-batch"></a>Běžné chyby při importu dávky

Běžné chyby zahrnují: 

> * Více než 1 000 projevů
> * Utterance JSON objekt, který nemá vlastnost entity. Vlastnost může být prázdné pole.
> * Word (y) označené ve více entitách
> * Popisek entity začíná nebo končí v prostoru.

## <a name="batch-test-state"></a>Stav dávkového testu

Služba LUIS sleduje stav posledního testu každé sady dat. To zahrnuje velikost (počet projevy v dávce), datum posledního spuštění a poslední výsledek (počet úspěšně předpovídaných promluv).

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>Výsledky dávkových zkoušek

Výsledek dávkového testu je bodový graf, známý jako matice chyb. Tento graf je 4-way porovnání projevy v dávkovém souboru a aktuální model je předpokládaný záměr a entity. 

Datové body v oddílech **Falešně pozitivní** a **Falešně negativní** označují chyby, které by měly být zkoumány. Pokud jsou všechny datové body v oddílech **True Positive** a **True Negative,** přesnost aplikace je v této sadě dat dokonalá.

![Čtyři části grafu](./media/luis-concept-batch-test/chart-sections.png)

Tento graf vám pomůže najít projevy, které LUIS předpovídá nesprávně na základě jeho aktuální školení. Výsledky jsou zobrazeny podle oblasti grafu. Vyberte jednotlivé body v grafu, chcete-li zkontrolovat informace o utterance nebo vyberte název oblasti pro kontrolu výsledků utterance v této oblasti.

![Dávkové testování](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Chyby ve výsledcích

Chyby v dávkovém testu označují záměry, které nejsou předpovězeny tak, jak jsou uvedeny v dávkovém souboru. Chyby jsou uvedeny ve dvou červených částech grafu. 

Falešně pozitivní část označuje, že utterance uzavřeno záměr nebo entitu, když by neměl mít. Falešně negativní označuje utterance neodpovídá záměru nebo entity, když by měl mít. 

## <a name="fixing-batch-errors"></a>Oprava chyb dávky

Pokud jsou chyby v testování dávky, můžete buď přidat další projevy záměru a/nebo označit další projevy s entitou pomoci LUIS provádět diskriminaci mezi záměry. Pokud jste přidali projevy a označili je a stále získat chyby předpovědi v dávkové testování, zvažte přidání funkce [seznamu frází](luis-concept-feature.md) s doménou specifické slovní zásoby pomoci LUIS rychleji učit. 

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [otestovat dávku](luis-how-to-batch-test.md)

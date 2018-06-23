---
title: Batch testování aplikace LEOŠ - Azure | Microsoft Docs
description: Dávky k testování používejte nepřetržitě fungovat ve vaší aplikaci ji upravit a zlepšovat její znalosti jazyka.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: v-geberr
ms.openlocfilehash: 3803df32d6431b8413e8df0837ed62b2e4344cdc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343020"
---
# <a name="batch-testing-in-luis"></a>Testování v LEOŠ batch

Testování batch ověří vaše [active](luis-concept-version.md#active-version) trained model k měření jeho přesnost předpovědi. Batch test umožňuje zobrazit přesnost každý záměr a entity ve vaší aktuální trained model v grafu. Zkontrolujte výsledky testu batch a proveďte příslušné akce a zvyšte tak přesnost, jako je například přidávání další příklad utterances k záměrem aplikace často selže-li identifikovat správný záměr.

## <a name="group-data-for-batch-test"></a>Data skupiny pro batch test
Je důležité, že jsou pro nové LEOŠ utterances používat pro testování batch. Pokud máte datové sady utterances, rozdělte utterances do tří nastaví: utterances přidány do záměrem utterances přijaté z publikovaných koncového bodu a utterances použít do dávky testu LEOŠ po je vycvičena. 

## <a name="a-dataset-of-utterances"></a>Datové sady utterances
Odeslat soubor batch utterances, označuje jako *datovou sadu*, pro testování batch. Datová sada je soubor ve formátu JSON obsahující maximálně 1000 s názvem bez přípony **jedinečných** utterances. V aplikaci můžete otestovat až 10 datové sady. Pokud je potřeba provést testování více, odstranit datovou sadu a poté přidejte novou.

|**Pravidla**|
|--|
|* Žádné duplicitní utterances|
|Žádné podřízené objekty hierarchické entity|
|utterances 1 000 nebo méně|

* Duplikáty jsou považovány za odpovídá přesným řetězcem, není shody, které jsou nejprve tokenizovat. 

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>
## <a name="batch-file-format"></a>Formát souboru batch
Dávkový soubor se skládá z utterances. Každý utterance musí mít očekávané záměrné předpovědi společně s žádné [naučili počítač entity](luis-concept-entity-types.md#types-of-entities) byste měli zjistit. 

Následuje příklad dávkového souboru:

   [!code-json[Valid batch test](~/samples-luis/documentation-samples/batch-testing/travel-agent-1.json)]


## <a name="common-errors-importing-a-batch"></a>Běžné chyby import dávky
Běžné chyby patří: 

> * Více než 1 000 utterances
> * Utterance objekt JSON, který nemá ve vlastnosti entity

## <a name="batch-test-state"></a>Stav testu batch
LEOŠ sleduje stav poslední test každé datové sady. To zahrnuje datum posledního spuštění velikost (počet utterances v dávce) a poslední výsledek (počet úspěšně předpokládaných utterances).

<a name="sections-of-the-results-chart"></a>
## <a name="batch-test-results"></a>Výsledky testu batch
Výsledek testu batch je bodový graf, označuje jako matice k chybě. Tento graf je 4 způsob porovnání utterances v souboru a předpokládaných záměr na aktuální model a entity. 

Datových bodů na **False kladné** a **False záporné** části označení chyb, které by se měly prozkoumat. Pokud jsou všechny datové body na **True kladné** a **True záporné** částech, pak je ideální pro tuto datovou sadu přesnost vaší aplikace.

![Čtyři části grafu](./media/luis-concept-batch-test/chart-sections.png)

Tento graf vám pomůže najít utterances, které předpovídá LEOŠ nesprávně podle jeho aktuální školení. Výsledky se zobrazí na oblast grafu. Vyberte jednotlivé body na graf tak, aby informace utterance nebo vyberte název oblasti a zkontrolovat výsledky utterance v této oblasti.

![Testování batch](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Chyby ve výsledcích
Chyby v testu batch naznačují záměry, které nejsou předpovědět, jak jsme uvedli v dávkovém souboru. Chyby jsou vypsány dva oddíly red grafu. 

Části false kladné označuje, že utterance shodná záměr nebo entity při by neměl mít. Falešně negativní označuje, že že utterance se neshoduje záměr nebo entity při by měl mít. 

## <a name="fixing-batch-errors"></a>Opravy chyb batch
Pokud nejsou chyby v batch testování, můžete přidat další utterances do záměrem, nebo označení další utterances s entitou, abyste LEOŠ, ujistěte se, k ní mezi tříd Intent. Pokud máte přidat utterances a s názvem bez přípony je a stále get předpovědi chyby při testování batch, je vhodné přidat [frázi seznamu](luis-concept-feature.md) funkce s specifické pro doménu termínů pomohou LEOŠ další rychlejší. 

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [testování dávky](luis-how-to-batch-test.md)
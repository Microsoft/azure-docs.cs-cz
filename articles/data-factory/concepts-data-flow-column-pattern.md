---
title: Vzory sloupců v toku dat mapování Azure Data Factory
description: Vytváření zobecněných vzorů transformace dat pomocí vzorů sloupců v tocích dat mapování Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: bfb4eeef482c4944e75b7805642bc93c23195208
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065520"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>Použití vzorů sloupců v toku dat mapování

Několik mapování transformace toku dat umožňují odkazovat na sloupce šablony na základě vzorů namísto pevně zakódovaných názvů sloupců. Tento porovnávání se označuje jako *vzorky sloupců*. Vzorky můžete definovat tak, aby odpovídaly sloupcům na základě názvu, datového typu, datového proudu nebo umístění namísto vyžadování přesných názvů polí. Existují dva scénáře, kde jsou užitečné vzory sloupců:

* Pokud se příchozí zdrojová pole často mění, například případ změny sloupců v textových souborech nebo databázích NoSQL. Tento scénář se označuje jako [posun schématu](concepts-data-flow-schema-drift.md).
* Pokud chcete provést společnou operaci na velké skupině sloupců. Například chtějí přetypovat každý sloupec, který má 'total' v názvu sloupce do double.

Vzorky sloupců jsou aktuálně k dispozici v odvozeném sloupci, agregaci, výběru a transformaci jímky.

## <a name="column-patterns-in-derived-column-and-aggregate"></a>Vzorky sloupců v odvozeném sloupci a agregaci

Chcete-li přidat vzorek sloupce v odvozeném sloupci nebo na kartě Agregace agregační transformace, klepněte na ikonu plus vpravo od existujícího sloupce. Vyberte **Přidat vzorek sloupce**. 

![vzory sloupců](media/data-flow/columnpattern.png "Vzory sloupců")

Pomocí [tvůrce výrazů](concepts-data-flow-expression-builder.md) zadejte podmínku shody. Vytvořte logický výraz, který odpovídá `name` `type`sloupcům podle , , `stream`a `position` sloupce. Vzorek ovlivní všechny sloupce, driftované nebo definované, kde podmínka vrátí hodnotu true.

Dvě pole výrazu pod podmínkou shody určují nové názvy a hodnoty ovlivněných sloupců. Slouží `$$` k odkazování na existující hodnotu odpovídajícího pole. Pole levého výrazu definuje název a pravé pole výrazu definuje hodnotu.

![vzory sloupců](media/data-flow/columnpattern2.png "Vzory sloupců")

Výše uvedený vzorek sloupce odpovídá každému sloupci typu double a vytvoří jeden agregační sloupec na shodu. Název nového sloupce je název odpovídajícího sloupce spojený s "_total". Hodnota nového sloupce je zaokrouhlený a souhrnný součet existující dvojité hodnoty.

Chcete-li ověřit, zda je odpovídající podmínka správná, můžete ověřit výstupní schéma definovaných sloupců na kartě **Zkontrolovat** nebo získat snímek dat na kartě **Náhled dat.** 

![vzory sloupců](media/data-flow/columnpattern3.png "Vzory sloupců")

## <a name="rule-based-mapping-in-select-and-sink"></a>Mapování založené na pravidlech ve výběru a jímce

Při mapování sloupců ve zdrojových a vybraných transformacích můžete přidat buď pevné mapování, nebo mapování založené na pravidlech. Shoda na `name`, `type` `stream`, `position` a sloupce. Můžete mít libovolnou kombinaci pevných a na základě pravidel mapování. Ve výchozím nastavení budou všechny projekce s více než 50 sloupci ve výchozím nastavení nastaveny na mapování založené na pravidlech, které odpovídá každému sloupci a vyvede zadávaný název. 

Chcete-li přidat mapování založené na pravidlech, klepněte na **tlačítko Přidat mapování** a vyberte mapování založené na **pravidlech**.

![mapování založené na pravidlech](media/data-flow/rule2.png "Mapování založené na pravidlech")

Každé mapování založené na pravidlech vyžaduje dva vstupy: podmínku, podle které se má shodovat a co pojmenovat každý mapovaný sloupec. Obě hodnoty jsou zadávány prostřednictvím [tvůrce výrazů](concepts-data-flow-expression-builder.md). Do levého pole výrazu zadejte logickou podmínku shody. V pravém poli výrazu určete, na co bude odpovídající sloupec mapován.

![mapování založené na pravidlech](media/data-flow/rule-based-mapping.png "Mapování založené na pravidlech")

Syntaxe slouží `$$` k odkazování na vstupní název odpovídajícího sloupce. Pomocí výše uvedeného obrázku jako příklad, řekněme, že uživatel chce, aby odpovídaly na všechny sloupce řetězce, jejichž názvy jsou kratší než šest znaků. Pokud byl pojmenován `test`jeden příchozí `$$ + '_short'` sloupec , výraz `test_short`jej přejmenuje . Pokud je to jediné mapování, které existuje, všechny sloupce, které nesplňují podmínku, budou vynechány z výstupních dat.

Vzorky odpovídají unášeným i definovaným sloupcům. Chcete-li zjistit, které definované sloupce jsou mapovány pravidlem, klikněte na ikonu brýlí vedle pravidla. Ověřte výstup pomocí náhledu dat.

### <a name="regex-mapping"></a>Mapování regexu

Klepnete-li na ikonu šipky dolů, můžete určit podmínku mapování regulárního výrazu. Podmínka mapování regulárních výrazů odpovídá všem názvům sloupců, které odpovídají zadané podmínce regulárního výrazu. To lze použít v kombinaci se standardním mapováním založeným na pravidlech.

![mapování založené na pravidlech](media/data-flow/regex-matching.png "Mapování založené na pravidlech")

Výše uvedený příklad odpovídá `(r)` vzoru regulárního výrazu nebo libovolnému názvu sloupce, který obsahuje malé písmeno r. Podobně jako standardní mapování založené na pravidlech jsou všechny odpovídající sloupce `$$` změněny podmínkou vpravo pomocí syntaxe.

### <a name="rule-based-hierarchies"></a>Hierarchie založené na pravidlech

Pokud má definovaná projekce hierarchii, můžete pomocí mapování založeného na pravidlech mapovat podsloupce hierarchií. Zadejte odpovídající podmínku a složitý sloupec, jehož dílčí sloupce chcete mapovat. Každý odpovídající podsloupec bude výstupem pomocí pravidla "Název jako", které je zadáno vpravo.

![mapování založené na pravidlech](media/data-flow/rule-based-hierarchy.png "Mapování založené na pravidlech")

Výše uvedený příklad odpovídá na všech `a`dílčích sloupcích složitého sloupce . `a`obsahuje dva `b` dílčí `c`sloupce a . Výstupní schéma bude obsahovat dva `b` `c` sloupce a jako podmínka `$$`"Název jako" je .

## <a name="pattern-matching-expression-values"></a>Hodnoty výrazu odpovídající vzorek.

* `$$`překládá na název nebo hodnotu každé shody za běhu
* `name`představuje název každého příchozího sloupce
* `type`představuje datový typ každého příchozího sloupce
* `stream`představuje název přidružený ke každému datovému proudu nebo transformaci v toku
* `position`je ordinální poloha sloupců v toku dat

## <a name="next-steps"></a>Další kroky
* Další informace o [jazyku výrazu](data-flow-expression-functions.md) toku dat pro transformace dat
* Použití vzorů sloupců v [transformaci jímky](data-flow-sink.md) a [výběr transformace](data-flow-select.md) pomocí mapování založeného na pravidlech

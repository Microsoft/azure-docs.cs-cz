---
title: Výběr transformace v toku dat mapování
description: Azure Data Factory mapování toku dat vybrat transformaci
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/18/2020
ms.openlocfilehash: a90a2def874c7f081f83a34aea956083eb72879a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686502"
---
# <a name="select-transformation-in-mapping-data-flow"></a>Výběr transformace v toku dat mapování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Pomocí vybrané transformace můžete sloupce přejmenovat, přetáhnout nebo usadit. Tato transformace nemění data řádků, ale zvolí, které sloupce jsou šířeny po proudu. 

Při vybrané transformaci mohou uživatelé určit pevná mapování, použít vzory k mapování založenému na pravidlech nebo povolit automatické mapování. Pevná a založená mapování na základě pravidel lze použít v rámci stejné transformace výběru. Pokud sloupec neodpovídá jednomu z definovaných mapování, bude vynechán.

## <a name="fixed-mapping"></a>Opraveno mapování

Pokud je v projekci definováno méně než 50 sloupců, budou mít všechny definované sloupce ve výchozím nastavení pevné mapování. Pevné mapování přebírá definovaný, příchozí sloupec a mapuje přesný název.

![Opraveno mapování](media/data-flow/fixedmapping.png "Opraveno mapování")

> [!NOTE]
> Posunutý sloupec nelze mapovat ani přejmenovat pomocí pevného mapování.

### <a name="mapping-hierarchical-columns"></a>Mapování hierarchických sloupců

Pevná mapování lze použít k mapování podsloupce hierarchického sloupce na sloupec nejvyšší úrovně. Pokud máte definovanou hierarchii, vyberte podsloupec pomocí rozevíracího souboru sloupců. Vybraná transformace vytvoří nový sloupec s hodnotou a datovým typem podsloupce.

![hierarchické mapování](media/data-flow/select-hierarchy.png "hierarchické mapování")

## <a name="rule-based-mapping"></a>Mapování založené na pravidlech

Pokud chcete mapovat mnoho sloupců najednou nebo předat posunuté sloupce pod proudem, použijte mapování založené na pravidlech k definování mapování pomocí vzorů sloupců. Shoda na `name`, `type` `stream`, `position` a sloupce. Můžete mít libovolnou kombinaci pevných a na základě pravidel mapování. Ve výchozím nastavení budou všechny projekce s více než 50 sloupci ve výchozím nastavení nastaveny na mapování založené na pravidlech, které odpovídá každému sloupci a vyvede zadávaný název. 

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

Pokud máte v názvu sloupce více shod regulárních výrazů, můžete odkazovat na konkrétní shody pomocí místa, `$n` kde "n" odkazuje na který shodu. Například '$2' odkazuje na druhou shodu v rámci názvu sloupce.

### <a name="rule-based-hierarchies"></a>Hierarchie založené na pravidlech

Pokud má definovaná projekce hierarchii, můžete pomocí mapování založeného na pravidlech mapovat podsloupce hierarchií. Zadejte odpovídající podmínku a složitý sloupec, jehož dílčí sloupce chcete mapovat. Každý odpovídající podsloupec bude výstupem pomocí pravidla "Název jako", které je zadáno vpravo.

![mapování založené na pravidlech](media/data-flow/rule-based-hierarchy.png "Mapování založené na pravidlech")

Výše uvedený příklad odpovídá na všech `a`dílčích sloupcích složitého sloupce . `a`obsahuje dva `b` dílčí `c`sloupce a . Výstupní schéma bude obsahovat dva `b` `c` sloupce a jako podmínka `$$`"Název jako" je .

### <a name="parameterization"></a>Parametrizace

Pomocí mapování založeného na pravidlech můžete parametrizovat názvy sloupců. Pomocí klíčového slova ```name``` přiřadíte názvy příchozích sloupců k parametru. Pokud máte například parametr ```mycolumn```toku dat , můžete vytvořit pravidlo, které ```mycolumn```odpovídá libovolnému názvu sloupce, který se rovná . Odpovídající sloupec můžete přejmenovat na pevně zakódovaný řetězec, například "obchodní klíč", a explicitně na něj odkazovat. V tomto příkladu je ```name == $mycolumn``` podmínka odpovídající a podmínka názvu je "obchodní klíč". 

## <a name="auto-mapping"></a>Automatické mapování

Při přidávání vybrané transformace lze **automatické mapování** povolit přepnutím jezdce Automatické mapování. Při automatickém mapování vybraná transformace mapuje všechny příchozí sloupce, s výjimkou duplikátů, se stejným názvem jako jejich vstup. To bude zahrnovat posunuté sloupce, což znamená, že výstupní data mohou obsahovat sloupce, které nejsou definovány ve schématu. Další informace o driftovaných sloupcích naleznete v [tématu posun schématu](concepts-data-flow-schema-drift.md).

![Automatické mapování](media/data-flow/automap.png "Automatické mapování")

Při zapnutém automatickém mapování bude vybraná transformace respektovat nastavení přeskočit duplicity a poskytne nový alias pro existující sloupce. Aliasing je užitečné při provádění více spojení nebo vyhledávání na stejném datovém proudu a ve scénářích vlastního připojení. 

## <a name="duplicate-columns"></a>Duplicitní sloupce

Ve výchozím nastavení vybere transformaci klesne duplicitní sloupce ve vstupní i výstupní projekce. Duplicitní vstupní sloupce často pocházejí z transformace spojení a vyhledávání, kde jsou názvy sloupců duplikovány na každé straně spojení. Duplicitní výstupní sloupce může dojít, pokud mapujete dva různé vstupní sloupce na stejný název. Zvolte, zda chcete přetažení maže nebo předat duplicitní sloupce přepnutím zaškrtávacího políčka.

![Přeskočit duplikáty](media/data-flow/select-skip-dup.png "Přeskočit duplikáty")

## <a name="ordering-of-columns"></a>Řazení sloupců

Pořadí mapování určuje pořadí výstupních sloupců. Pokud je vstupní sloupec mapován vícekrát, bude dodrženo pouze první mapování. Pro všechny duplicitní sloupec přetažení, první shoda bude zachována.

## <a name="data-flow-script"></a>Skript toku dat

### <a name="syntax"></a>Syntaxe

```
<incomingStream>
    select(mapColumn(
        each(<hierarchicalColumn>, match(<matchCondition>), <nameCondition> = $$), ## hierarchical rule-based matching
        <fixedColumn>, ## fixed mapping, no rename
        <renamedFixedColumn> = <fixedColumn>, ## fixed mapping, rename
        each(match(<matchCondition>), <nameCondition> = $$), ## rule-based mapping
        each(patternMatch(<regexMatching>), <nameCondition> = $$) ## regex mapping
    ),
    skipDuplicateMapInputs: { true | false },
    skipDuplicateMapOutputs: { true | false }) ~> <selectTransformationName>
```

### <a name="example"></a>Příklad

Níže je uveden příklad vybraného mapování a jeho skriptu toku dat:

![Vybrat příklad skriptu](media/data-flow/select-script-example.png "Vybrat příklad skriptu")

```
DerivedColumn1 select(mapColumn(
        each(a, match(true())),
        movie,
        title1 = title,
        each(match(name == 'Rating')),
        each(patternMatch(`(y)`),
            $1 + 'regex' = $$)
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> Select1
```

## <a name="next-steps"></a>Další kroky
* Po použití Vyberte přejmenovat, změna pořadí a alias sloupce, použijte [transformace jímky](data-flow-sink.md) k zemi data do úložiště dat.

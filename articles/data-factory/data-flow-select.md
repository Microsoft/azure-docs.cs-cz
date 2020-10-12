---
title: V toku mapování dat vyberte transformaci.
description: Azure Data Factory mapování toku dat, vyberte transformaci
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/02/2020
ms.openlocfilehash: 70e0a95a85920562af8bf9d3fffa6633709dccc5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84322086"
---
# <a name="select-transformation-in-mapping-data-flow"></a>V toku mapování dat vyberte transformaci.

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Pomocí příkazu SELECT Transform můžete přejmenovat, odstranit nebo změnit pořadí sloupců. Tato transformace nemění data řádků, ale volí sloupce, které jsou šířeny za podřízených. 

V případě transformace výběru můžou uživatelé zadat pevná mapování, používat vzory pro mapování na základě pravidel nebo povolit automatické mapování. Pravidla a mapování založená na pravidlech lze použít ve stejné transformaci SELECT. Pokud sloupec neodpovídá jednomu z definovaných mapování, bude vyřazen.

## <a name="fixed-mapping"></a>Pevné mapování

Pokud je ve vaší projekci definováno méně než 50 sloupců, budou mít všechny definované sloupce ve výchozím nastavení pevné mapování. Pevné mapování přebírá definovaný, příchozí sloupec a mapuje na něj přesný název.

![Pevné mapování](media/data-flow/fixedmapping.png "Pevné mapování")

> [!NOTE]
> Nemůžete namapovat ani Přejmenovat sloupec s použitím pevného mapování.

### <a name="mapping-hierarchical-columns"></a>Mapování hierarchických sloupců

Pevné mapování lze použít k namapování podsloupce hierarchického sloupce na sloupec nejvyšší úrovně. Pokud máte definovanou hierarchii, pomocí rozevírací nabídky sloupec vyberte dílčí sloupec. Transformace Select vytvoří nový sloupec s hodnotou a datovým typem podsloupce.

![hierarchické mapování](media/data-flow/select-hierarchy.png "hierarchické mapování")

## <a name="rule-based-mapping"></a>Mapování na základě pravidel


> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xiXz]

Pokud chcete namapovat mnoho sloupců najednou nebo předat sloupce po směru, použijte mapování na základě pravidel k definování mapování pomocí vzorů sloupců. Odpovídá na základě `name` sloupců, `type` , a `stream` `position` . Můžete mít libovolnou kombinaci s pevným mapováním a mapování na základě pravidel. Ve výchozím nastavení budou všechny projekce s více než 50 sloupci standardně mapování na základě pravidel, které se shoduje s každým sloupcem a výstupem zadaného názvu. 

Chcete-li přidat mapování na základě pravidla, klikněte na tlačítko **Přidat mapování** a vyberte **mapování na základě pravidel**.

![mapování na základě pravidel](media/data-flow/rule2.png "Mapování na základě pravidel")

Každé mapování založené na pravidlech vyžaduje dva vstupy: podmínku, na kterou se má rozlišovat a co má pojmenovat jednotlivé mapované sloupce. Obě hodnoty jsou zadané prostřednictvím [Tvůrce výrazů](concepts-data-flow-expression-builder.md). Do pole levý výraz zadejte podmínku vaší logické shody. V poli pravého výrazu určete, k čemu se odpovídající sloupec namapuje.

![mapování na základě pravidel](media/data-flow/rule-based-mapping.png "Mapování na základě pravidel")

Použijte `$$` syntaxi pro odkaz na vstupní název spárovaného sloupce. Pomocí výše uvedeného obrázku jako příklad si řekněme, že uživatel chce vyhledat všechny řetězcové sloupce, jejichž názvy jsou kratší než šest znaků. Pokud byl jeden příchozí sloupec pojmenován `test` , výraz `$$ + '_short'` sloupec přejmenuje `test_short` . Pokud je toto jediné mapování, které existuje, všechny sloupce, které nesplňují podmínku, se z výstupních dat ztratí.

Vzory odpovídají současně předaným i definovaným sloupcům. Chcete-li zjistit, které definované sloupce jsou mapovány pravidlem, klikněte na ikonu brýlí vedle pravidla. Ověřte výstup pomocí data Preview.

### <a name="regex-mapping"></a>Mapování regulárního výrazu

Pokud kliknete na ikonu Dvojitá šipka dolů, můžete zadat podmínku mapování regulárního výrazu. Podmínka mapování regulárního výrazu odpovídá všem názvům sloupců, které odpovídají zadané podmínce regulárního výrazu. Tato možnost se dá použít v kombinaci se standardními mapováními založenými na pravidlech.

![mapování na základě pravidel](media/data-flow/regex-matching.png "Mapování na základě pravidel")

Výše uvedený příklad se shoduje se vzorem regulárního výrazu `(r)` nebo libovolným názvem sloupce, který obsahuje malý případ r. Podobně jako u mapování na základě pravidel jsou všechny odpovídající sloupce změněny podmínkou na pravé straně pomocí `$$` syntaxe.

Pokud je v názvu sloupce více shod regulárního výrazu, můžete odkazovat na konkrétní shody pomocí, `$n` kde ' n ' odkazuje na to, která shoda má. Například "$2" odkazuje na druhou shodu v rámci názvu sloupce.

### <a name="rule-based-hierarchies"></a>Hierarchie založené na pravidlech

Pokud vaše definovaná projekce má hierarchii, můžete k mapování podsloupců hierarchií použít mapování na základě pravidel. Zadejte podmínku pro porovnání a složitý sloupec, jehož podsloupce chcete namapovat. Každý odpovídající Podsloupec se nastaví na základě pravidla "název jako", které je uvedeno na pravé straně.

![mapování na základě pravidel](media/data-flow/rule-based-hierarchy.png "Mapování na základě pravidel")

Výše uvedený příklad se shoduje se všemi podsloupci komplexního sloupce `a` . `a` obsahuje dva podsloupce `b` a `c` . Výstupní schéma bude obsahovat dva sloupce `b` a `c` jako podmínku "název jako" `$$` .

### <a name="parameterization"></a>Parametrizace

Názvy sloupců můžete parametrizovat pomocí mapování založeného na pravidlech. Použijte klíčové slovo ```name``` pro porovnání názvů příchozích sloupců s parametrem. Například pokud máte parametr toku dat ```mycolumn``` , můžete vytvořit pravidlo, které odpovídá jakémukoli názvu sloupce, který je roven ```mycolumn``` . Odpovídající sloupec můžete přejmenovat na pevně zakódovaný řetězec, jako je například obchodní klíč, a explicitně na něj odkazovat. V tomto příkladu je podmínka porovnání ```name == $mycolumn``` a podmínka názvu je "obchodní klíč". 

## <a name="auto-mapping"></a>Automatické mapování

Při přidávání transformace Select lze povolit **automatické mapování** přepnutím posuvníku automatického mapování. Při automatickém mapování mapuje transformace Select všechny příchozí sloupce s vyloučením duplicit se stejným názvem jako jejich vstup. Tato akce bude zahrnovat sloupce s omezením, což znamená, že výstupní data mohou obsahovat sloupce, které nejsou ve schématu definovány. Další informace o vydaných sloupcích najdete v tématu o [posunu schématu](concepts-data-flow-schema-drift.md).

![Automatické mapování](media/data-flow/automap.png "Automatické mapování")

S automatickým mapováním na, transformaci Select bude akceptovat duplicitní nastavení přeskočit a zadat nový alias pro existující sloupce. Vytváření aliasů je užitečné při provádění více spojení nebo vyhledávání ve stejném datovém proudu a ve scénářích s automatickým spojením. 

## <a name="duplicate-columns"></a>Duplicitní sloupce

Ve výchozím nastavení transformace SELECT v vstupní i výstupní projekci vyřazuje duplicitní sloupce. Duplicitní vstupní sloupce často pocházejí z transformací JOIN a Lookup, kde jsou názvy sloupců duplikovány na každé straně spojení. Pokud namapujete dva různé vstupní sloupce na stejný název, mohou nastat duplicitní výstupní sloupce. Zaškrtnutím tohoto políčka můžete vybrat, zda chcete vyřadit nebo předávat duplicitní sloupce.

![Přeskočit duplicity](media/data-flow/select-skip-dup.png "Přeskočit duplicity")

## <a name="ordering-of-columns"></a>Řazení sloupců

Pořadí mapování určuje pořadí výstupních sloupců. Pokud je vstupní sloupec namapován vícekrát, bude dodrženo pouze první mapování. Při odstranění duplicitních sloupců se zachová první shoda.

## <a name="data-flow-script"></a>Skript toku dat

### <a name="syntax"></a>Syntax

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

Níže je příklad mapování výběru a skriptu toku dat:

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
* Po použití příkazu vybrat k přejmenování, změně pořadí a aliasu použijte [transformaci jímky](data-flow-sink.md) k vytvoření plochy dat do úložiště dat.

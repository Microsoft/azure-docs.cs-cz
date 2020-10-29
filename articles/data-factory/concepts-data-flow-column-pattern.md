---
title: Vzory sloupců v Azure Data Factory tok dat mapování
description: Vytváření zobecněných vzorů pro transformaci dat pomocí vzorů sloupců v Azure Data Factory mapování toků dat
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/16/2020
ms.openlocfilehash: dab065f4d2b025fa15966d81b66b41acb12c54b3
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027121"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>Použití vzorů sloupců v mapování toku dat

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Několik transformací toku dat mapování vám umožní odkazovat na sloupce šablon na základě vzorů namísto pevně kódovaných názvů sloupců. Tato shoda se označuje jako *vzory sloupců* . Můžete definovat vzory, které odpovídají sloupcům na základě názvu, datového typu, datového proudu, zdroje nebo pozice místo vyžadování přesných názvů polí. Existují dva scénáře, kdy jsou vhodné vzory sloupců:

* Pokud se pole příchozího zdroje často mění, například případ změny sloupců v textových souborech nebo NoSQL databázích. Tento scénář je známý jako [posun schématu](concepts-data-flow-schema-drift.md).
* Pokud chcete provést běžnou operaci pro velkou skupinu sloupců. Například, chcete-li přetypování každého sloupce, který má "Total" v názvu sloupce na hodnotu Double.

Vzory sloupců jsou aktuálně k dispozici v odvozeném sloupci, agregace, výběru a transformaci jímky.

## <a name="column-patterns-in-derived-column-and-aggregate"></a>Vzory sloupců v odvozeném sloupci a agregace

Chcete-li přidat vzorek sloupce do odvozeného sloupce, agregace nebo transformace okna, klikněte na tlačítko **Přidat** nad seznamem sloupců nebo ikonu se symbolem plus vedle existujícího odvozeného sloupce. Vyberte možnost **přidat model sloupce** .

![Snímek obrazovky zobrazuje ikonu Plus pro přidání vzoru sloupce.](media/data-flow/add-column-pattern.png "Vzory sloupců")

K zadání podmínky shody použijte [Tvůrce výrazů](concepts-data-flow-expression-builder.md) . Vytvořte logický výraz, který odpovídá sloupcům na základě `name` , `type` , `stream` , `origin` a `position` sloupce. Vzor bude mít vliv na libovolný sloupec, posunovaná nebo definovaná, kde podmínka vrátí hodnotu true.

Dvě pole výrazu pod podmínkou shody určují nové názvy a hodnoty ovlivněných sloupců. Slouží `$$` k odkazování na existující hodnotu spárovaného pole. Levý rámeček výrazu definuje název a pravé pole výrazu definuje hodnotu.

![Snímek obrazovky se zobrazí na kartě nastavení odvozeného sloupce.](media/data-flow/edit-column-pattern.png "Vzory sloupců")

Výše uvedený vzor sloupce odpovídá každému sloupci typu Double a vytvoří jeden odvozený sloupec na základě shody. Když se uvedete `$$` jako pole název sloupce, každý odpovídající sloupec se aktualizuje se stejným názvem. Hodnota každého sloupce je zaokrouhlena na dvě desetinná místa.

Pokud chcete ověřit, jestli je vaše shoda podmínky správná, můžete ověřit výstupní schéma definovaných sloupců na kartě **zkontrolovat** nebo získat snímek dat na kartě **Náhled dat** . 

![Snímek obrazovky se zobrazí na kartě výstupní schéma.](media/data-flow/columnpattern3.png "Vzory sloupců")

## <a name="rule-based-mapping-in-select-and-sink"></a>Mapování na základě pravidel pro výběr a jímku

Při mapování sloupců ve zdroji a výběru transformací můžete přidat buď pevné mapování, nebo mapování na základě pravidel. Odpovídá na základě `name` sloupců, `type` , `stream` , `origin` a `position` . Můžete mít libovolnou kombinaci s pevným mapováním a mapování na základě pravidel. Ve výchozím nastavení budou všechny projekce s více než 50 sloupci standardně mapování na základě pravidel, které se shoduje s každým sloupcem a výstupem zadaného názvu. 

Chcete-li přidat mapování na základě pravidla, klikněte na tlačítko **Přidat mapování** a vyberte **mapování na základě pravidel** .

![Snímek obrazovky zobrazuje mapování na základě pravidel vybrané z mapování přidat.](media/data-flow/rule2.png "Mapování na základě pravidel")

Každé mapování založené na pravidlech vyžaduje dva vstupy: podmínku, na kterou se má rozlišovat a co má pojmenovat jednotlivé mapované sloupce. Obě hodnoty jsou zadané prostřednictvím [Tvůrce výrazů](concepts-data-flow-expression-builder.md). Do pole levý výraz zadejte podmínku vaší logické shody. V poli pravého výrazu určete, k čemu se odpovídající sloupec namapuje.

![Snímek obrazovky ukazuje mapování.](media/data-flow/rule-based-mapping.png "Mapování na základě pravidel")

Použijte `$$` syntaxi pro odkaz na vstupní název spárovaného sloupce. Pomocí výše uvedeného obrázku jako příklad si řekněme, že uživatel chce vyhledat všechny řetězcové sloupce, jejichž názvy jsou kratší než šest znaků. Pokud byl jeden příchozí sloupec pojmenován `test` , výraz `$$ + '_short'` sloupec přejmenuje `test_short` . Pokud je toto jediné mapování, které existuje, všechny sloupce, které nesplňují podmínku, se z výstupních dat ztratí.

Vzory odpovídají současně předaným i definovaným sloupcům. Chcete-li zjistit, které definované sloupce jsou mapovány pravidlem, klikněte na ikonu brýlí vedle pravidla. Ověřte výstup pomocí data Preview.

### <a name="regex-mapping"></a>Mapování regulárního výrazu

Pokud kliknete na ikonu Dvojitá šipka dolů, můžete zadat podmínku mapování regulárního výrazu. Podmínka mapování regulárního výrazu odpovídá všem názvům sloupců, které odpovídají zadané podmínce regulárního výrazu. Tato možnost se dá použít v kombinaci se standardními mapováními založenými na pravidlech.

![Snímek obrazovky ukazuje podmínku mapování regulárního výrazu s úrovní hierarchie a odpovídajícím názvem.](media/data-flow/regex-matching.png "Mapování na základě pravidel")

Výše uvedený příklad se shoduje se vzorem regulárního výrazu `(r)` nebo libovolným názvem sloupce, který obsahuje malý případ r. Podobně jako u mapování na základě pravidel jsou všechny odpovídající sloupce změněny podmínkou na pravé straně pomocí `$$` syntaxe.

### <a name="rule-based-hierarchies"></a>Hierarchie založené na pravidlech

Pokud vaše definovaná projekce má hierarchii, můžete k mapování podsloupců hierarchií použít mapování na základě pravidel. Zadejte podmínku pro porovnání a složitý sloupec, jehož podsloupce chcete namapovat. Každý odpovídající Podsloupec se nastaví na základě pravidla "název jako", které je uvedeno na pravé straně.

![Snímek obrazovky ukazuje mapování na základě pravidel, které se používá pro hierarchii.](media/data-flow/rule-based-hierarchy.png "Mapování na základě pravidel")

Výše uvedený příklad se shoduje se všemi podsloupci komplexního sloupce `a` . `a` obsahuje dva podsloupce `b` a `c` . Výstupní schéma bude obsahovat dva sloupce `b` a `c` jako podmínku "název jako" `$$` .

## <a name="pattern-matching-expression-values"></a>Hodnoty výrazů pro porovnávání vzorů.

* `$$` překládá se za název nebo hodnotu každé shody v době běhu.
* `name` představuje název každého příchozího sloupce.
* `type` představuje datový typ každého příchozího sloupce.
* `stream` představuje název spojený s každým datovým proudem nebo transformaci v toku.
* `position` je pořadové místo sloupců v toku dat
* `origin` je transformace, ve které sloupec pochází nebo byl naposledy aktualizován.

## <a name="next-steps"></a>Další kroky
* Další informace o [jazyku výrazu](data-flow-expression-functions.md) mapování dat pro transformaci dat
* Použijte vzory sloupců v [transformaci jímky](data-flow-sink.md) a [Vyberte transformaci](data-flow-select.md) pomocí mapování založeného na pravidlech.

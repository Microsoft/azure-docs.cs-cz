---
title: Azure Data Factory mapování vzorců datových toků
description: Vytváření zobecněných vzorů pro transformaci dat pomocí Azure Data Factorych vzorů sloupců v mapování toků dat
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 4df831c1329ab13f19e6ecf979e404d4a90e5f72
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219764"
---
# <a name="mapping-data-flows-column-patterns"></a>Vzorce mapování datových toků

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Několik Azure Data Factory transformacích toků dat podporuje představu o vzorcích sloupců, takže můžete vytvářet šablony sloupců založené na vzorcích místo pevně zakódovaných názvů sloupců. Tuto funkci můžete v rámci Tvůrce výrazů použít k definování vzorů, které odpovídají sloupcům pro transformaci místo vyžadování přesně specifických názvů polí. Vzorce jsou užitečné v případě, že se příchozí zdrojová pole často mění, zejména v případě změny sloupců v textových souborech nebo NoSQL databázích. Tato podmínka se někdy označuje jako "posun schématu".

Toto "flexibilní schéma" zpracovává se aktuálně v odvozeném sloupci a agregované transformace a také transformace Select a jímka jako mapování na základě pravidel.

![vzory sloupců](media/data-flow/columnpattern2.png "Vzory sloupců")

## <a name="column-patterns"></a>Vzory sloupců
Vzory sloupců jsou užitečné pro zpracování obou scénářů a také v obecných scénářích. Je vhodný pro podmínky, kdy nemůžete plně znát název každého sloupce. Porovnávání vzorů můžete použít pro název sloupce a datový typ sloupce a vytvořit výraz pro transformaci, který provede tuto operaci s libovolným polem v datovém proudu, `name` který odpovídá vašim  &  `type` vzorům.

Při přidávání výrazu k transformaci, která přijímá vzory, vyberte možnost "přidat vzor sloupců". Vzory sloupců umožňují, aby sloupce s posunem schématu odpovídaly vzorům.

Při sestavování vzorů sloupců `$$` šablony použijte ve výrazu, který představuje odkaz na každé odpovídající pole ze vstupního datového proudu.

Pokud se rozhodnete použít některý z funkcí regulárního výrazu pro Tvůrce výrazů, můžete následně použít $1, $2, $3... Chcete-li odkazovat na dílčí vzory odpovídající vašemu výrazu Regex.

Příklad scénáře vzoru sloupce používá součet s řadou příchozích polí. Výpočty agregačního SOUČTu jsou v agregační transformaci. Pak můžete použít součet každé shody typů polí, které odpovídají "celému číslu", a pak použít $ $ pro odkazování na každou shodu ve výrazu.

## <a name="match-columns"></a>Shoda sloupců
![typy vzorků sloupců](media/data-flow/pattern2.png "Typy vzorku")

Chcete-li vytvořit vzory založené na sloupcích, můžete se shodovat s názvem sloupce, typem, datovým proudem nebo pozicí a použít libovolnou kombinaci těch s funkcemi výrazů a regulárními výrazy.

![pozice sloupce](media/data-flow/position.png "Pozice sloupce")

## <a name="rule-based-mapping"></a>Mapování na základě pravidel
Když namapujete sloupce ve zdroji a vyberete transformace, budete mít možnost zvolit "pevné mapování" nebo "mapování na základě pravidel". Pokud znáte schéma vašich dat a očekáváte konkrétní sloupce ze zdrojové datové sady, které vždy odpovídají specifickým statickým názvům, můžete použít pevné mapování. Ale při práci s flexibilními schématy použijte mapování na základě pravidel. Pomocí výše popsaných pravidel budete moct vytvořit porovnávání vzorů.

![mapování na základě pravidel](media/data-flow/rule2.png "Mapování na základě pravidel")

Sestavte pravidla pomocí Tvůrce výrazů. Vaše výrazy vrátí logickou hodnotu buď pro odpovídající sloupce (true), nebo pro vyloučení sloupců (false).

## <a name="pattern-matching-special-columns"></a>Speciální sloupce pro porovnávání vzorů

* `$$`se převede na název každé shody v době návrhu v režimu ladění a při spuštění v době běhu.
* `name`představuje název každého příchozího sloupce.
* `type`představuje datový typ každého příchozího sloupce.
* `stream`představuje název přidružený ke každému streamu nebo transformaci v toku.
* `position`je pořadové místo sloupců v toku dat

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o jazykovém [výrazu](https://aka.ms/dataflowexpressions) toku dat mapování ADF pro transformace dat.
* Použijte vzory sloupců v [transformaci jímky](data-flow-sink.md) a [Vyberte transformaci](data-flow-select.md) pomocí mapování založeného na pravidlech.

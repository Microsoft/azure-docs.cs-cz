---
title: Azure Data Factory mapování vzorců datových toků
description: Vytváření zobecněných vzorů pro transformaci dat pomocí Azure Data Factorych vzorů sloupců v mapování toků dat
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: d24988dfd5cbaf20e92c5afbbc39dc0c78e3ef6a
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314862"
---
# <a name="azure-data-factory-mapping-data-flows-column-patterns"></a>Vzorce dat mapování datových továrn Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Několik Azure Data Factory transformacích toků dat podporuje představu o vzorcích sloupců, takže můžete vytvářet šablony sloupců založené na vzorcích místo pevně zakódovaných názvů sloupců. Tuto funkci můžete v rámci Tvůrce výrazů použít k definování vzorů, které odpovídají sloupcům pro transformaci místo vyžadování přesně specifických názvů polí. Vzorce jsou užitečné v případě, že se příchozí zdrojová pole často mění, zejména v případě změny sloupců v textových souborech nebo NoSQL databázích. Tato podmínka se někdy označuje jako "posun schématu".

![vzory sloupců](media/data-flow/columnpattern2.png "Vzory sloupců")

Vzory sloupců jsou užitečné pro zpracování obou scénářů a také v obecných scénářích. Je vhodný pro podmínky, kdy nemůžete plně znát název každého sloupce. Porovnávání vzorů můžete použít pro název sloupce a datový typ sloupce a vytvořit výraz pro transformaci, který provede tuto operaci s libovolným polem v datovém proudu, `name` který odpovídá vašim  &  `type` vzorům.

Při přidávání výrazu k transformaci, která přijímá vzory, vyberte možnost "přidat vzor sloupců". Vzory sloupců umožňují, aby sloupce s posunem schématu odpovídaly vzorům.

Při sestavování vzorů sloupců `$$` šablony použijte ve výrazu, který představuje odkaz na každé odpovídající pole ze vstupního datového proudu.

Pokud se rozhodnete použít některý z funkcí regulárního výrazu pro Tvůrce výrazů, můžete následně použít $1, $2, $3... Chcete-li odkazovat na dílčí vzory odpovídající vašemu výrazu Regex.

Příklad scénáře vzoru sloupce používá součet s řadou příchozích polí. Výpočty agregačního SOUČTu jsou v agregační transformaci. Pak můžete použít součet každé shody typů polí, které odpovídají "celému číslu", a pak použít $ $ pro odkazování na každou shodu ve výrazu.

## <a name="match-columns"></a>Shoda sloupců
![typy vzorků sloupců](media/data-flow/pattern2.png "Typy vzorku")

Chcete-li vytvořit vzory založené na sloupcích, můžete se shodovat s názvem sloupce, typem, datovým proudem nebo pozicí a použít libovolnou kombinaci těch s funkcemi výrazů a regulárními výrazy.

![pozice sloupce](media/data-flow/position.png "Pozice sloupce")

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o jazykovém [výrazu](http://aka.ms/dataflowexpressions) toku dat mapování ADF pro transformace dat.

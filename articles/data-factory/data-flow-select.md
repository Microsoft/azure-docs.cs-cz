---
title: Azure Data Factory mapování toku dat, vyberte transformaci
description: Azure Data Factory mapování toku dat, vyberte transformaci
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 15c74637a2dc42ec44f582878b5505d94637cd7b
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314192"
---
# <a name="azure-data-factory-mapping-data-flow-select-transformation"></a>Azure Data Factory mapování toku dat, vyberte transformaci
[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Tuto transformaci použijte pro selektivitu sloupce (zmenšení počtu sloupců), sloupce aliasů a názvy datových proudů a změnu pořadí sloupců.

## <a name="how-to-use-select-transformation"></a>Jak použít transformaci Select
Možnost vybrat Transformer umožňuje aliasovat celý datový proud nebo sloupce v tomto datovém proudu, přiřazovat jiné názvy (aliasy) a následně na tyto nové názvy odkazovat později v toku dat. Tato transformace je užitečná pro scénáře automatického spojení. Způsob implementace automatického spojení v toku dat ADF je použít datový proud, vytvořit větvení s "novou větví" a hned následně přidat transformaci "SELECT". Tento datový proud teď bude mít nový název, který můžete použít pro připojení zpátky k původnímu streamu, a vytvořit tak nové připojení:

![Připojovat se k sobě](media/data-flow/selfjoin.png "Připojovat se k sobě")

Ve výše uvedeném diagramu je transformace SELECT v horní části. Tím se aliasuje původní datový proud na "OrigSourceBatting". Ve zvýrazněné transformaci spojení pod ním vidíte, že používáme tento datový proud aliasu jako připojení na pravé straně, což nám umožňuje odkazovat na stejný klíč v levém & pravé straně vnitřního spojení.

Možnost vybrat se dá použít taky jako způsob, jak vybrat sloupce z toku dat. Například pokud máte v jímky definované 6 sloupců, ale chcete pouze vybrat konkrétní 3 pro transformaci a následně tok do jímky, můžete vybrat pouze ty 3 pomocí transformace SELECT.

> [!NOTE]
> Pokud chcete vybrat jenom konkrétní sloupce, musíte přepnout na Vybrat vše.

![Vybrat transformaci](media/data-flow/select001.png "Vybrat alias")

## <a name="options"></a>Možnosti
* Výchozím nastavením pro možnost vybrat je zahrnout všechny příchozí sloupce a zachovat tyto původní názvy. Datový proud můžete aliasovat tak, že nastavíte název transformace SELECT.
* Pokud chcete jednotlivé sloupce aliasovat, zrušte výběr možnosti Vybrat vše a v dolní části použijte mapování sloupce.
* Zvolením přeskočit duplicity Eliminujte duplicitní sloupce ze vstupních nebo výstupních metadat.

![Přeskočit duplicity](media/data-flow/select-skip-dup.png "Přeskočit duplicity")

## <a name="next-steps"></a>Další postup
* Po použití příkazu vybrat k přejmenování, změně pořadí a aliasu použijte transformaci [jímky](data-flow-sink.md) k vytvoření plochy dat do úložiště dat.

---
title: Mapování transformace řazení toku dat
description: Transformace řazení dat mapování datových tok ových společností Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/08/2018
ms.openlocfilehash: c09439c5f54ae4b0884e9e25ae9a5a488f935bac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930229"
---
# <a name="azure-data-factory-data-flow-sort-transformations"></a>Transformace řazení dat datové továrny Azure



![Nastavení řazení](media/data-flow/sort.png "Seřadit")

Transformace řazení umožňuje seřadit příchozí řádky v aktuálním datovém proudu. Odchozí řádky z transformace řazení se následně budou řídit pravidly řazení, která jste nastavili. Můžete vybrat jednotlivé sloupce a seřadit je ASC nebo DEC pomocí indikátoru šipky vedle každého pole. Pokud potřebujete upravit sloupec před použitím řazení, klikněte na "Vypočítané sloupce" pro spuštění editoru výrazů. To poskytne příležitost k vytvoření výrazu pro operaci řazení namísto jednoduchého použití sloupce pro řazení.

## <a name="case-insensitive"></a>Malá a velká písmena se nerozlišují.
Pokud chcete při řazení řetězcových nebo textových polí ignorovat malá a velká písmena, můžete zapnout možnost Malá a velká písmena.

"Seřadit pouze v rámci oddílů" využívá rozdělení dat Spark. Řazením příchozích dat pouze v rámci každého oddílu mohou datové toky seřadit dělená data namísto řazení celého datového proudu.

Každý z podmínek řazení v transformaci řazení lze změnit uspořádány. Pokud tedy potřebujete přesunout sloupec výše v prioritě řazení, uchopte tento řádek pomocí myši a přesuňte jej v seznamu řazení výš nebo níže.

Efekty dělení na řazení

Tok dat ADF se provádí na velkých objemových datech Sparku s daty distribuovanými mezi více uzly a oddíly. Je důležité mít na paměti při navrhování toku dat, pokud jste v závislosti na transformace řazení zachovat data ve stejném pořadí. Pokud se rozhodnete znovu rozdělit data v následné transformaci, může dojít ke ztrátě řazení z důvodu, že přemíchání dat.

## <a name="next-steps"></a>Další kroky

Po řazení můžete použít [agregační transformace](data-flow-aggregate.md)

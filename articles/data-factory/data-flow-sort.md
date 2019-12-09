---
title: Mapování transformace řazení toku dat
description: Azure Data Factory transformace řazení dat mapování
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/08/2018
ms.openlocfilehash: c09439c5f54ae4b0884e9e25ae9a5a488f935bac
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930229"
---
# <a name="azure-data-factory-data-flow-sort-transformations"></a>Azure Data Factory transformace řazení toku dat



![Nastavení řazení](media/data-flow/sort.png "Seřadit")

Transformace řazení umožňuje řadit příchozí řádky v aktuálním datovém proudu. Odchozí řádky z transformace řazení budou následně následovat pravidla řazení, která jste nastavili. Můžete zvolit jednotlivé sloupce a seřadit je ASC nebo DEC pomocí indikátoru šipky vedle jednotlivých polí. Pokud před použitím řazení potřebujete změnit sloupec, klikněte na vypočítané sloupce a spusťte Editor výrazů. To vám poskytne příležitost vytvořit výraz pro operaci řazení místo pouhým použitím sloupce pro řazení.

## <a name="case-insensitive"></a>Malá a velká písmena se nerozlišují.
Pokud chcete ignorovat velikost písmen při řazení řetězce nebo textových polí, můžete zapnout rozlišování velkých a malých písmen.

"Seřadit pouze v rámci oddílů" využívá vytváření oddílů dat Spark. Pomocí řazení příchozích dat v jednotlivých oddílech můžou toky dat řadit dělená data namísto řazení celého datového proudu.

Každé z podmínek řazení v transformaci řazení lze změnit jejich uspořádání. Takže pokud potřebujete přesunout sloupec v prioritě řazení vyšší, přejeďte tento řádek myší a v seznamu řazení ho přesuňte nahoru nebo dolů.

Dělení efektů při řazení

Tok dat ADF se spouští na clusterech s velkým objemem dat s daty distribuovanými napříč více uzly a oddíly. Je důležité mít na paměti, že při navrhování toku dat, pokud v závislosti na transformaci řazení chcete zachovat data ve stejném pořadí, je důležité mít na paměti. Pokud se rozhodnete znovu rozdělit data v následné transformaci, může dojít ke ztrátě řazení z důvodu tohoto přerozdělování dat.

## <a name="next-steps"></a>Další kroky

Po řazení možná budete chtít použít [agregační transformaci](data-flow-aggregate.md) .

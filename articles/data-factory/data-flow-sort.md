---
title: Transformace řazení v toku dat mapování
description: Azure Data Factory transformace řazení dat mapování
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 4a6567f8576e2507704956233bc593b203b48239
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588730"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>Transformace řazení v toku dat mapování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Transformace řazení umožňuje řadit příchozí řádky v aktuálním datovém proudu. Můžete zvolit jednotlivé sloupce a seřadit je ve vzestupném nebo sestupném pořadí.

> [!NOTE]
> Mapování toků dat je prováděno v clusterech Spark, které distribuují data mezi více uzly a oddíly. Pokud se rozhodnete znovu rozdělit data v následné transformaci, může dojít ke ztrátě řazení z důvodu přerozdělování dat. Nejlepším způsobem, jak zachovat pořadí řazení v toku dat, je nastavit jeden oddíl na kartě optimalizace na transformaci a zachovat transformaci řazení co nejblíže k jímky.

## <a name="configuration"></a>Konfigurace

![Nastavení řazení](media/data-flow/sort.png "Seřadit")

**Nerozlišuje velká a malá písmena:** Bez ohledu na to, zda chcete ignorovat velikost písmen při řazení řetězce nebo textových polí

**Seřadit pouze v rámci oddílů:** Při spuštění datových toků ve Sparku jsou jednotlivé datové proudy rozdělené do oddílů. Toto nastavení seřadí data pouze v příchozích oddílech, nikoli pro řazení celého datového proudu. 

**Podmínky řazení:** Vyberte sloupce, podle kterých řadíte a v jakém pořadí se má řazení provést. Pořadí určuje prioritu řazení. Vyberte, zda se na začátku nebo na konci datového proudu budou zobrazovat hodnoty null.

### <a name="computed-columns"></a>Vypočítané sloupce

Chcete-li upravit nebo extrahovat hodnotu sloupce před použitím řazení, umístěte ukazatel myši na sloupec a vyberte "vypočítaný sloupec". Tím se otevře Tvůrce výrazů pro vytvoření výrazu pro operaci řazení namísto použití hodnoty sloupce.

## <a name="data-flow-script"></a>Skript toku dat

### <a name="syntax"></a>Syntax

```
<incomingStream>
    sort(
        desc(<sortColumn1>, { true | false }),
        asc(<sortColumn2>, { true | false }),
        ...
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>Příklad

![Nastavení řazení](media/data-flow/sort.png "Seřadit")

Skript toku dat pro výše uvedenou konfiguraci řazení je ve fragmentu kódu níže.

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>Další kroky

Po řazení možná budete chtít použít [agregační transformaci](data-flow-aggregate.md) .

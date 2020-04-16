---
title: Řazení transformace v toku dat mapování
description: Transformace řazení dat mapování datových tok ových společností Azure
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 381c6573dff1b3f1638af9090a535d9a1e59b2b5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413173"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>Řazení transformace v toku dat mapování

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Transformace řazení umožňuje seřadit příchozí řádky v aktuálním datovém proudu. Můžete vybrat jednotlivé sloupce a seřadit je vzestupně nebo sestupně.

> [!NOTE]
> Mapování datových toků se provádí na podhřcí clustery, které distribuují data mezi více uzlů a oddílů. Pokud se rozhodnete znovu rozdělit data v následné transformaci, může dojít ke ztrátě řazení z důvodu přemísťování dat.

## <a name="configuration"></a>Konfigurace

![Nastavení řazení](media/data-flow/sort.png "Seřadit")

**Malá a velká písmena:** Zda chcete ignorovat případ při řazení řetězcových nebo textových polí

**Seřadit pouze v rámci oddílů:** Jako toky dat jsou spuštěny na jiskru, každý datový proud je rozdělen do oddílů. Toto nastavení seřadí data pouze v rámci příchozích oddílů namísto řazení celého datového proudu. 

**Podmínky řazení:** Zvolte sloupce, podle kterých se řadíte a v jakém pořadí řazení dochází. Pořadí určuje prioritu řazení. Zvolte, zda se hodnoty null zobrazí na začátku nebo na konci datového proudu.

### <a name="computed-columns"></a>Vypočítané sloupce

Chcete-li upravit nebo extrahovat hodnotu sloupce před použitím řazení, najeďte nad sloupec a vyberte "vypočítaný sloupec". Tím otevřete tvůrce výrazů a vytvořte výraz pro operaci řazení namísto použití hodnoty sloupce.

## <a name="data-flow-script"></a>Skript toku dat

### <a name="syntax"></a>Syntaxe

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

Po řazení můžete použít [agregační transformace](data-flow-aggregate.md)

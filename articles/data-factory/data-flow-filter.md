---
title: Filtrování transformace v toku dat mapování
description: Odfiltrování řádků pomocí transformace filtru v toku dat mapování Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 3460c789909c6acd0537ef89a9ec8509bf6e135d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413735"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Filtrování transformace v toku dat mapování

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Filtr transformace umožňuje filtrování řádků na základě podmínky. Výstupní datový proud obsahuje všechny řádky, které odpovídají podmínce filtrování. Transformace filtru je podobná klauzuli WHERE v SQL.

## <a name="configuration"></a>Konfigurace

Pomocí tvůrce výrazů toku dat zadejte výraz pro podmínku filtru. Chcete-li otevřít tvůrce výrazů, klikněte na modré pole. Podmínka filtru musí být typu logická hodnota. Další informace o tom, jak vytvořit výraz, naleznete v dokumentaci [tvůrce výrazů.](concepts-data-flow-expression-builder.md)

![Transformace filtru](media/data-flow/filter1.png "Transformace filtru")

## <a name="data-flow-script"></a>Skript toku dat

### <a name="syntax"></a>Syntaxe

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>Příklad

Níže uvedený příklad je `FilterBefore1960` transformace filtru s `CleanData`názvem, která přijímá příchozí datový proud . Podmínka filtru je `year <= 1960`výraz .

V ux datové továrny tato transformace vypadá jako následující obrázek:

![Transformace filtru](media/data-flow/filter1.png "Transformace filtru")

Skript toku dat pro tuto transformaci je ve fragmentu níže:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Další kroky

Odfiltrování sloupců pomocí [vybrané transformace](data-flow-select.md)

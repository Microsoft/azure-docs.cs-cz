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
ms.openlocfilehash: 32a40f3d4da93318c6d2ec25295c89a7b53141e4
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606424"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Filtrování transformace v toku dat mapování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

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

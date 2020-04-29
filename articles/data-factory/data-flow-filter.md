---
title: Filtrovat transformaci v toku dat mapování
description: Odfiltrování řádků pomocí transformace filtru v Azure Data Factory toku dat mapování
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 32a40f3d4da93318c6d2ec25295c89a7b53141e4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606424"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Filtrovat transformaci v toku dat mapování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Transformace filtru umožňují filtrování řádků na základě podmínky. Výstupní datový proud obsahuje všechny řádky, které odpovídají podmínkám filtrování. Transformace filtru je podobná klauzuli WHERE v SQL.

## <a name="configuration"></a>Konfigurace

Pomocí Tvůrce výrazů toku dat zadejte výraz pro podmínku filtru. Chcete-li otevřít Tvůrce výrazů, klikněte na modrý rámeček. Podmínka filtru musí být typu Boolean. Další informace o tom, jak vytvořit výraz, najdete v dokumentaci k [tvůrci výrazů](concepts-data-flow-expression-builder.md) .

![Filtrovat transformaci](media/data-flow/filter1.png "Filtrovat transformaci")

## <a name="data-flow-script"></a>Skript toku dat

### <a name="syntax"></a>Syntaxe

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>Příklad

Níže uvedený příklad je transformace filtru s názvem `FilterBefore1960` , která přebírá příchozí `CleanData`datový proud. Podmínka filtru je výraz `year <= 1960`.

V uživatelském prostředí Data Factory Tato transformace vypadá jako na následujícím obrázku:

![Filtrovat transformaci](media/data-flow/filter1.png "Filtrovat transformaci")

Skript toku dat pro tuto transformaci je v následujícím fragmentu kódu:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Další kroky

Filtrování sloupců pomocí [transformace Select](data-flow-select.md)

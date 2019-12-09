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
ms.openlocfilehash: 84b935d1646ea4f13d5926fc68797fd8efd03dea
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930359"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Filtrovat transformaci v toku dat mapování

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

### <a name="example"></a>Příklad:

Níže uvedený příklad je transformace filtru s názvem `FilterBefore1960`, která přebírá příchozí datový proud `CleanData`. Podmínka filtru je `year <= 1960`výrazu.

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

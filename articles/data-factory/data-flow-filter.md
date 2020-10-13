---
title: Filtrovat transformaci v toku dat mapování
description: Odfiltrování řádků pomocí transformace filtru v Azure Data Factory toku dat mapování
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/26/2020
ms.openlocfilehash: 8189228d6707812fb943e9925dc2bbf1b6da4972
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84112815"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Filtrovat transformaci v toku dat mapování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Transformace filtru umožňují filtrování řádků na základě podmínky. Výstupní datový proud obsahuje všechny řádky, které odpovídají podmínkám filtrování. Transformace filtru je podobná klauzuli WHERE v SQL.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xnxN]

## <a name="configuration"></a>Konfigurace

Pomocí Tvůrce výrazů toku dat zadejte výraz pro podmínku filtru. Chcete-li otevřít Tvůrce výrazů, klikněte na modrý rámeček. Podmínka filtru musí být typu Boolean. Další informace o tom, jak vytvořit výraz, najdete v dokumentaci k [tvůrci výrazů](concepts-data-flow-expression-builder.md) .

![Filtrovat transformaci](media/data-flow/filter1.png "Filtrovat transformaci")

## <a name="data-flow-script"></a>Skript toku dat

### <a name="syntax"></a>Syntax

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>Příklad

Níže uvedený příklad je transformace filtru s názvem `FilterBefore1960` , která přebírá příchozí datový proud `CleanData` . Podmínka filtru je výraz `year <= 1960` .

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

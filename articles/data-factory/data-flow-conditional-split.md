---
title: Podmíněná rozdělení transformace v toku dat mapování
description: Rozdělení dat do různých datových proudů pomocí podmíněné rozdělení transformace v toku dat mapování Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 20def8ca51f21d914e7090999e8284244c5f3ec7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416498"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>Podmíněná rozdělení transformace v toku dat mapování

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Podmíněné rozdělení transformace trasy řádky dat do různých datových proudů na základě odpovídajícípodmínky. Podmíněné rozdělení transformace je podobná struktury rozhodnutí CASE v programovacím jazyce. Transformace vyhodnotí výrazy a na základě výsledků přesměruje řádek dat na zadaný datový proud.

## <a name="configuration"></a>Konfigurace

Nastavení **Rozdělit na** určuje, zda řádek dat toky na první odpovídající datový proud nebo každý datový proud, který odpovídá.

Pomocí tvůrce výrazů toku dat zadejte výraz pro podmínku rozdělení. Chcete-li přidat novou podmínku, klikněte na ikonu plus v existujícím řádku. Výchozí datový proud lze přidat také pro řádky, které neodpovídají žádné podmínce.

![podmíněné rozdělení](media/data-flow/conditionalsplit1.png "možnosti podmíněného rozdělení")

## <a name="data-flow-script"></a>Skript toku dat

### <a name="syntax"></a>Syntaxe

```
<incomingStream>
    split(
        <conditionalExpression1>
        <conditionalExpression2>
        ...
        disjoint: {true | false}
    ) ~> <splitTx>@(stream1, stream2, ..., <defaultStream>)
```

### <a name="example"></a>Příklad

Níže uvedený příklad je podmíněné rozdělení transformace s názvem, `SplitByYear` který trvá v příchozí datový proud `CleanData`. Tato transformace má `year < 1960` dvě `year > 1980`podmínky rozdělení a . `disjoint`je false, protože data přejde na první odpovídající podmínku. Každý řádek odpovídající první podmínce přejde do výstupního datového proudu `moviesBefore1960`. Všechny zbývající řádky odpovídající druhé podmínce přejdou na výstupní datový proud `moviesAFter1980`. Všechny ostatní řádky protékají výchozím datovým proudem `AllOtherMovies`.

V ux datové továrny tato transformace vypadá jako následující obrázek:

![podmíněné rozdělení](media/data-flow/conditionalsplit1.png "možnosti podmíněného rozdělení")

Skript toku dat pro tuto transformaci je ve fragmentu níže:

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>Další kroky

Běžné transformace toku dat používané s podmíněným rozdělením jsou [transformace spojení](data-flow-join.md), [transformace vyhledávání](data-flow-lookup.md)a [vybraná transformace](data-flow-select.md)

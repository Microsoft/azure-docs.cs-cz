---
title: Podmíněné transformace rozdělení v Azure Data Factory toku dat mapování
description: Rozdělení dat do různých datových proudů pomocí podmíněné transformace rozdělení v Azure Data Factory mapování toku dat
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 9ace415aa725a82d8feda5702d25d7e5ff9875d9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73676819"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>Podmíněné transformace rozdělení v toku dat mapování

Podmíněná transformace rozdělení směruje řádky dat do různých datových proudů na základě vyhovujících podmínek. Podmíněná transformace rozdělení je podobná struktuře PŘÍPADných rozhodnutí v programovacím jazyce. Transformace vyhodnocuje výrazy a na základě výsledků směruje datový řádek na zadaný datový proud.

## <a name="configuration"></a>Konfigurace

Nastavení **rozdělení** určuje, zda řádek dat pokračuje na první odpovídající datový proud nebo na každý datový proud, na který odpovídá.

Pomocí Tvůrce výrazů toku dat zadejte výraz pro podmínku rozdělení. Chcete-li přidat novou podmínku, klikněte na ikonu Plus v existujícím řádku. Je možné přidat výchozí datový proud i pro řádky, které neodpovídají žádné podmínce.

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

V následujícím příkladu je podmíněná transformace rozdělení s názvem `SplitByYear`, která přebírá příchozí Stream `CleanData`. Tato transformace obsahuje dvě podmínky rozdělení `year < 1960` a `year > 1980`. `disjoint` má hodnotu false, protože data přecházejí na první podmínku porovnání. Každý řádek, který se shoduje s první podmínkou, přejde do výstupního datového proudu `moviesBefore1960`. Všechny zbývající řádky, které odpovídají druhé podmínce, přešly do výstupního datového proudu `moviesAFter1980`. Všechny ostatní řádky přecházejí přes výchozí datový proud `AllOtherMovies`.

V uživatelském prostředí Data Factory Tato transformace vypadá jako na následujícím obrázku:

![podmíněné rozdělení](media/data-flow/conditionalsplit1.png "možnosti podmíněného rozdělení")

Skript toku dat pro tuto transformaci je v následujícím fragmentu kódu:

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>Další kroky

Běžné transformace toku dat, které se používají s podmíněným rozdělením, jsou [transformace spojení](data-flow-join.md), [transformace vyhledávání](data-flow-lookup.md)a [transformace výběru](data-flow-select.md) .

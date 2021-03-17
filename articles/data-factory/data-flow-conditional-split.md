---
title: Podmíněné transformace rozdělení v toku dat mapování
description: Rozdělení dat do různých datových proudů pomocí podmíněné transformace rozdělení v Azure Data Factory mapování toku dat
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/21/2020
ms.openlocfilehash: eece6f97e82f3800d4f59ac1849b34c2a1e4635b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83800089"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>Podmíněné transformace rozdělení v toku dat mapování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Podmíněná transformace rozdělení směruje řádky dat do různých datových proudů na základě vyhovujících podmínek. Podmíněná transformace rozdělení je podobná struktuře PŘÍPADných rozhodnutí v programovacím jazyce. Transformace vyhodnocuje výrazy a na základě výsledků směruje datový řádek na zadaný datový proud.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4wKCX]

## <a name="configuration"></a>Konfigurace

Nastavení **rozdělení** určuje, zda řádek dat pokračuje na první odpovídající datový proud nebo na každý datový proud, na který odpovídá.

Pomocí Tvůrce výrazů toku dat zadejte výraz pro podmínku rozdělení. Chcete-li přidat novou podmínku, klikněte na ikonu Plus v existujícím řádku. Je možné přidat výchozí datový proud i pro řádky, které neodpovídají žádné podmínce.

![podmíněné rozdělení](media/data-flow/conditionalsplit1.png "možnosti podmíněného rozdělení")

## <a name="data-flow-script"></a>Skript toku dat

### <a name="syntax"></a>Syntax

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

V následujícím příkladu je podmíněná transformace rozdělení s názvem `SplitByYear` , která přijímá příchozí datový proud `CleanData` . Tato transformace má dvě podmínky rozdělení `year < 1960` a `year > 1980` . `disjoint` hodnota je false, protože data přecházejí na první podmínku porovnání. Každý řádek, který odpovídá první podmínce, přejde do výstupního proudu `moviesBefore1960` . Všechny zbývající řádky, které odpovídají druhé podmínce, přešly do výstupního datového proudu `moviesAFter1980` . Všechny ostatní řádky přecházejí přes výchozí datový proud `AllOtherMovies` .

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

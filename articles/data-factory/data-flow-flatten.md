---
title: Mapování sloučené transformace toku dat
description: Transformovaná transformace toku dat mapování služby Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 435d2469be8eb572caa02e381d84ae4e9ac32f4b
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77674841"
---
# <a name="azure-data-factory-flatten-transformation"></a>Transformace Azure Data Factory sloučení

Transformaci sloučení lze použít k kontingenčním hodnotám pole v hierarchické struktuře do nových řádků, v podstatě denormalizují vaše data.

![Panel nástrojů transformace](media/data-flow/flatten5.png "Panel nástrojů transformace")

![Transformace sloučení 1](media/data-flow/flatten7.png "Transformace sloučení 1")

## <a name="unroll-by"></a>Odvádět od

Nejprve vyberte sloupec pole, který chcete vyhodit a pivotovat.

![Nastavení transformace sloučení](media/data-flow/flatten1.png "Nastavení transformace sloučení")

## <a name="unroll-root"></a>Odveďte kořen

Ve výchozím nastavení se ADF sloučí do struktury v poli, které jste si zvolili výše. Nebo můžete zvolit jinou část hierarchie, která se má vrátit.

## <a name="input-columns"></a>Vstupní sloupce

Nakonec vyberte projekci nové struktury na základě příchozích polí a normalizovaného sloupce, který jste vyvolali.

## <a name="data-flow-script"></a>Skript toku dat

### <a name="syntax"></a>Syntaxe

```
<incomingStream>
foldDown(unroll(<unroll cols>),
    mapColumn(
        name,
        each(<array>(type == '<arrayDataType>')),
        each(<array>, match(true())),
        location
    )) ~> <transformationName>
```

### <a name="example"></a>Příklad

```
source(output(
        name as string,
        location as string,
        satellites as string[],
        goods as (trade as boolean, customers as string[], orders as (orderId as string, orderTotal as double, shipped as (orderItems as (itemName as string, itemQty as string)[]))[])
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source2
source2 foldDown(unroll(goods.orders.shipped.orderItems),
    mapColumn(
        name,
        each(goods.orders, match(type == 'integer')),
        each(goods.orders.shipped.orderItems, match(true())),
        location
    )) ~> Flatten1
Flatten1 sink(allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> sink1
```    

## <a name="next-steps"></a>Další kroky

* K pivotování řádků na sloupce použijte [transformaci pivotu](data-flow-pivot.md) .
* Použijte [transformaci Unpivot](data-flow-unpivot.md) k vytvoření kontingenčních sloupců na řádcích.

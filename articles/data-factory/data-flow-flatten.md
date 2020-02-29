---
title: Mapování sloučené transformace toku dat
description: Azure Data Factory transformaci sloučení toku dat
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 415a093fd8a8fbe27e1d240b061548e18f2ca6b6
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164725"
---
# <a name="azure-data-factory-flatten-transformation"></a>Transformace Azure Data Factory sloučení

Transformaci sloučení lze použít k kontingenčním hodnotám pole v hierarchické struktuře do nových řádků, v podstatě denormalizují vaše data.

![Panel nástrojů transformace](media/data-flow/flatten5.png "Panel nástrojů transformace")

![Transformace sloučení 1](media/data-flow/flatten7.png "Transformace sloučení 1")

## <a name="unroll-by"></a>Odvádět od

Nejprve vyberte sloupec pole, který chcete vyhodit a pivotovat.

![Nastavení transformace sloučení](media/data-flow/flatten1.png "Nastavení transformace sloučení")

## <a name="unroll-root"></a>Odveďte kořen

Ve výchozím nastavení se ADF sloučí do struktury v poli, které jste si zvolili výše. Nebo můžete zvolit jinou část hierarchie, která se má vrátit. Možnost "nekumulativní kořen" je volitelné nastavení.

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

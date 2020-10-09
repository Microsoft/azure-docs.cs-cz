---
title: Transformace sloučení v toku dat mapování
description: Denormalizovat hierarchická data pomocí transformace sloučení
author: kromerm
ms.author: makromer
ms.review: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: a0e75957a0ab49394dab56f2b7fb847dee4b43cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81413677"
---
# <a name="flatten-transformation-in-mapping-data-flow"></a>Transformace sloučení v toku dat mapování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Použijte transformaci sloučení k převzetí hodnot polí v hierarchických strukturách, jako je JSON, a Odveďte je do jednotlivých řádků. Tento proces se označuje jako denormalizace.

## <a name="configuration"></a>Konfigurace

Transformace sloučení obsahuje následující nastavení konfigurace.

![Nastavení sloučení průhledností](media/data-flow/flatten1.png "Nastavení sloučení průhledností")

### <a name="unroll-by"></a>Odvádět od

Vyberte pole, které chcete odznačit. Výstupní data budou mít jeden řádek na každou položku v každém poli. Pokud je pole Nezahrnuto ve vstupním řádku null nebo prázdné, bude jeden výstupní řádek s nenasazenými hodnotami jako null.

### <a name="unroll-root"></a>Odveďte kořen

Ve výchozím nastavení sloučí transformaci pole na horní část hierarchie, ve které existuje. Volitelně můžete vybrat pole jako nekumulativní kořen. Kořenová složka pro odebrání musí být pole komplexních objektů, které je buď nebo obsahuje pole dewind. Pokud je vybráno "kumulativní kořen", budou výstupní data obsahovat alespoň jeden řádek na položky v kořenu kumulativního umístění. Pokud vstupní řádek neobsahuje žádné položky v kořenu, bude z výstupních dat vyřazen. Výběr neúplného kořenu bude vždy mít za následek menší nebo stejný počet řádků než výchozí chování.

### <a name="flatten-mapping"></a>Mapování sloučení

Podobně jako u transformace vyberte možnost projekce nové struktury ze příchozích polí a denormalizovaného pole. Pokud je namapováno denormalizované pole, výstupní sloupec bude stejný datový typ jako pole. Pokud je pole unwind v poli složitých objektů, které obsahují podpole, mapování položky tohoto subarry bude mít za následek výstup pole.

Pokud chcete ověřit výstup mapování, přečtěte si kartu Kontrola a náhled dat.

## <a name="examples"></a>Příklady

Viz následující objekt JSON pro níže uvedené příklady transformace sloučení.

``` json
{
  "name":"MSFT","location":"Redmond", "satellites": ["Bay Area", "Shanghai"],
  "goods": {
    "trade":true, "customers":["government", "distributer", "retail"],
    "orders":[
        {"orderId":1,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":2}]}},
        {"orderId":2,"orderTotal":323.34,"shipped":{"orderItems":[{"itemName":"Mice","itemQty":2},{"itemName":"Keyboard","itemQty":1}]}}
    ]}}
{"name":"Company1","location":"Seattle", "satellites": ["New York"],
  "goods":{"trade":false, "customers":["store1", "store2"],
  "orders":[
      {"orderId":4,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":3}]}},
      {"orderId":5,"orderTotal":343.24,"shipped":{"orderItems":[{"itemName":"Chair","itemQty":4},{"itemName":"Lamp","itemQty":2}]}}
    ]}}
{"name": "Company2", "location": "Bellevue",
  "goods": {"trade": true, "customers":["Bank"], "orders": [{"orderId": 4, "orderTotal": 123.34}]}}
{"name": "Company3", "location": "Kirkland"}
```

### <a name="no-unroll-root-with-string-array"></a>Nekumulativní kořen s polem řetězců

| Odvádět od | Odveďte kořen | Projekce |
| --------- | ----------- | ---------- |
| zboží. zákazníci | Žádné | name <br> Zákazník = zboží. Zákazník |

#### <a name="output"></a>Výstup

```
{ 'MSFT', 'government'}
{ 'MSFT', 'distributer'}
{ 'MSFT', 'retail'}
{ 'Company1', 'store'}
{ 'Company1', 'store2'}
{ 'Company2', 'Bank'}
{ 'Company3', null}
```

### <a name="no-unroll-root-with-complex-array"></a>Nekumulativní kořen se složitým polem

| Odvádět od | Odveďte kořen | Projekce |
| --------- | ----------- | ---------- |
| zboží. Orders. expedovaných. orderItems | Žádné | name <br> ČísloObjednávky = zboží. Orders. ČísloObjednávky <br> Item = zboží. Orders. expedováno. orderItems. Item <br> itemQty = zboží. Orders. dodána. orderItems. itemQty <br> umístění = umístění |

#### <a name="output"></a>Výstup

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
{ 'Company3', null, null, null, 'Kirkland'}
```

### <a name="same-root-as-unroll-array"></a>Stejný kořen jako nekumulativní pole

| Odvádět od | Odveďte kořen | Projekce |
| --------- | ----------- | ---------- |
| zboží. objednávky | zboží. objednávky | name <br> Items. Orders. expedovaných. orderItems. Item <br> zboží. zákazníci <br> location |

#### <a name="output"></a>Výstup

```
{ 'MSFT', ['Laptop','Charger'], ['government','distributer','retail'], 'Redmond'}
{ 'MSFT', ['Mice', 'Keyboard'], ['government','distributer','retail'], 'Redmond'}
{ 'Company1', ['Laptop','Charger'], ['store', 'store2'], 'Seattle'}
{ 'Company1', ['Chair', 'Lamp'], ['store', 'store2'], 'Seattle'}
{ 'Company2', null, ['Bank'], 'Bellevue'}
```

### <a name="unroll-root-with-complex-array"></a>Rozveďte kořen se složitým polem.

| Odvádět od | Odveďte kořen | Projekce |
| --------- | ----------- | ---------- |
| zboží. Orders. expedovaných. orderItem | zboží. objednávky |name <br> ČísloObjednávky = zboží. Orders. ČísloObjednávky <br> Item = zboží. Orders. expedováno. orderItems. Item <br> itemQty = zboží. Orders. dodána. orderItems. itemQty <br> umístění = umístění |

#### <a name="output"></a>Výstup

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
```

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
source foldDown(unroll(goods.orders.shipped.orderItems, goods.orders),
    mapColumn(
        name,
        orderId = goods.orders.orderId,
        itemName = goods.orders.shipped.orderItems.itemName,
        itemQty = goods.orders.shipped.orderItems.itemQty,
        location = location
    ),
    skipDuplicateMapInputs: false,
    skipDuplicateMapOutputs: false) 
```    

## <a name="next-steps"></a>Další kroky

* K pivotování řádků na sloupce použijte [transformaci pivotu](data-flow-pivot.md) .
* Použijte [transformaci Unpivot](data-flow-unpivot.md) k vytvoření kontingenčních sloupců na řádcích.

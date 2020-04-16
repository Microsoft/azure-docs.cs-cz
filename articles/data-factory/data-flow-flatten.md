---
title: Sloučení transformace v toku dat mapování
description: Denormalizovat hierarchická data pomocí sloučení transformace
author: kromerm
ms.author: makromer
ms.review: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: a0e75957a0ab49394dab56f2b7fb847dee4b43cb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413677"
---
# <a name="flatten-transformation-in-mapping-data-flow"></a>Sloučení transformace v toku dat mapování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Pomocí sloučení transformace můžete vzít hodnoty pole do hierarchických struktur, jako je například JSON, a rozbalit je do jednotlivých řádků. Tento proces se nazývá denormalizace.

## <a name="configuration"></a>Konfigurace

Sloučení transformace obsahuje následující nastavení konfigurace

![Sloučení nastavení](media/data-flow/flatten1.png "Sloučení nastavení")

### <a name="unroll-by"></a>Odvíjet podle

Vyberte pole, které chcete rozbalit. Výstupní data budou mít jeden řádek na položku v každém poli. Pokud rozbalit pole ve vstupním řádku je null nebo prázdné, bude jeden výstupní řádek s unrolled hodnoty jako null.

### <a name="unroll-root"></a>Zrušit kořenový adresář

Ve výchozím nastavení sloučení množí se zploštit matice na začátek hierarchie, ve které existuje. Volitelně můžete vybrat pole jako kořenový adresář. Rozbalit kořen musí být pole složitých objektů, které buď je nebo obsahuje rozbalit pole. Pokud je vybrán kořen rozbalovací, výstupní data budou obsahovat alespoň jeden řádek na položky v kořenovém adresáři rozbalit. Pokud vstupní řádek nemá žádné položky v kořenovém adresáři unroll, bude vynechán z výstupních dat. Výběr kořenového adresáře pro rozvrácení bude vždy výstup menší nebo stejný počet řádků než výchozí chování.

### <a name="flatten-mapping"></a>Sloučení mapování

Podobně jako u vybrané transformace zvolte projekci nové struktury z příchozích polí a nenormalizovaného pole. Pokud je namapováno nenormalizované pole, bude výstupní sloupec mít stejný datový typ jako pole. Pokud unroll podle pole je pole složitých objektů, které obsahuje subpole, mapování položky tohoto subarry bude výstup pole.

Ověřte výstup mapování na kartě Kontrola a náhledu dat.

## <a name="examples"></a>Příklady

Následující příklady zploštělé transformace naleznete na následujícím objektu JSON.

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

### <a name="no-unroll-root-with-string-array"></a>Žádný kořenový adresář s polem řetězců

| Odvíjet podle | Zrušit kořenový adresář | Projekce |
| --------- | ----------- | ---------- |
| zboží.zákazníci | Žádná | jméno <br> odběratel = goods.customer |

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

### <a name="no-unroll-root-with-complex-array"></a>Žádný kořenový adresář se složitým polem

| Odvíjet podle | Zrušit kořenový adresář | Projekce |
| --------- | ----------- | ---------- |
| goods.orders.shipped.orderItems | Žádná | jméno <br> orderId = goods.orders.orderId <br> itemName = goods.orders.shipped.orderItems.itemName <br> itemQty = goods.orders.shipped.orderItems.itemQty <br> umístění = umístění |

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

### <a name="same-root-as-unroll-array"></a>Stejný kořen jako rozbalit pole

| Odvíjet podle | Zrušit kořenový adresář | Projekce |
| --------- | ----------- | ---------- |
| zboží.objednávky | zboží.objednávky | jméno <br> goods.orders.shipped.orderItems.itemName <br> zboží.zákazníci <br> location |

#### <a name="output"></a>Výstup

```
{ 'MSFT', ['Laptop','Charger'], ['government','distributer','retail'], 'Redmond'}
{ 'MSFT', ['Mice', 'Keyboard'], ['government','distributer','retail'], 'Redmond'}
{ 'Company1', ['Laptop','Charger'], ['store', 'store2'], 'Seattle'}
{ 'Company1', ['Chair', 'Lamp'], ['store', 'store2'], 'Seattle'}
{ 'Company2', null, ['Bank'], 'Bellevue'}
```

### <a name="unroll-root-with-complex-array"></a>Odvíjet kořen se složitým polem

| Odvíjet podle | Zrušit kořenový adresář | Projekce |
| --------- | ----------- | ---------- |
| goods.orders.shipped.orderItem | zboží.objednávky |jméno <br> orderId = goods.orders.orderId <br> itemName = goods.orders.shipped.orderItems.itemName <br> itemQty = goods.orders.shipped.orderItems.itemQty <br> umístění = umístění |

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

* Transformace [pivotu](data-flow-pivot.md) slouží k přemění řádků ke sloupcům.
* Transformace [Unpivot](data-flow-unpivot.md) slouží k přeměně sloupců na řádky.

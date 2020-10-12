---
title: Filtrování událostí pro Azure Event Grid
description: Popisuje, jak filtrovat události při vytváření předplatného Azure Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 837209d4197c271598155776b8d171a705e1f454
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86120088"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Principy filtrování událostí pro předplatná Event Grid

Tento článek popisuje různé způsoby, jak filtrovat, které události se odešlou do vašeho koncového bodu. Při vytváření odběru událostí máte k dispozici tři možnosti pro filtrování:

* Typy událostí
* Předmět začíná na nebo končí
* Pokročilá pole a operátory

## <a name="event-type-filtering"></a>Filtrování typu události

Ve výchozím nastavení jsou všechny [typy událostí](event-schema.md) pro zdroj události odesílány do koncového bodu. Do koncového bodu se můžete rozhodnout odeslat jenom určité typy událostí. Můžete například dostávat oznámení o aktualizacích vašich prostředků, ale ne upozornění na jiné operace, jako je odstranění. V takovém případě filtr podle `Microsoft.Resources.ResourceWriteSuccess` typu události. Zadejte pole s typy událostí nebo určete, že `All` se mají načíst všechny typy událostí pro zdroj události.

Syntaxe JSON pro filtrování podle typu události je:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Filtrování subjektu

Pro jednoduché filtrování podle předmětu zadejte počáteční nebo koncovou hodnotu předmětu. Například můžete určit, že má předmět ukončen, `.txt` aby se zobrazily pouze události související s odesláním textového souboru do účtu úložiště. Nebo můžete filtrovat předmět `/blobServices/default/containers/testcontainer` na začátku a získat tak všechny události pro tento kontejner, ale ne jiné kontejnery v účtu úložiště.

Při publikování událostí pro vlastní témata vytvořte předměty pro události, které předplatitelům umožní snadno zjistit, jestli se o událost zajímá. Předplatitelé používají vlastnost Subject k filtrování a směrování událostí. Zvažte přidání cesty pro místo, kde došlo k události, takže předplatitelé mohou filtrovat segmenty této cesty. Cesta umožňuje předplatitelům zúžit nebo široce filtrovat události. Pokud zadáte cestu tři segmenty `/A/B/C` , například v předmětu, můžou předplatitelé filtrovat podle prvního segmentu `/A` a získat tak širokou škálu událostí. Tyto předplatitelé získají události s předměty, jako je `/A/B/C` nebo `/A/D/E` . Jiní předplatitelé mohou filtrovat podle `/A/B` a získat tak užší sadu událostí.

Syntaxe JSON pro filtrování podle předmětu je:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Rozšířené filtrování

Chcete-li filtrovat podle hodnot v datových polích a zadat operátor porovnání, použijte možnost pokročilého filtrování. V části Rozšířené filtrování zadáte:

* typ operátoru – typ porovnání.
* klíč – pole v datech události, které používáte pro filtrování. Může to být číslo, logická hodnota nebo řetězec.
* hodnoty – hodnota nebo hodnoty, které se mají porovnat s klíčem.

Pokud zadáte jeden filtr s více hodnotami, provede se operace **nebo** , takže hodnota pole klíč musí být jedna z těchto hodnot. Tady je příklad:

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/",
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

Pokud zadáte více různých filtrů, provede se operace **a** , takže je nutné splnit všechny podmínky filtru. Tady je příklad: 

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/"
        ]
    },
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

### <a name="operators"></a>Operátory

K dispozici jsou operátory pro **čísla** :

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals
* NumberIn
* NumberNotIn

Dostupný operátor pro **logické hodnoty** je: 
- BoolEquals

Dostupné operátory pro **řetězce** jsou:

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

Všechna porovnávání řetězců nerozlišují **velká a malá písmena.**

### <a name="key"></a>Klíč

Pro události ve schématu Event Grid použijte pro klíč následující hodnoty:

* ID
* Téma
* Předmět
* Typ události
* Dataverze
* Data události (jako data. klíč1)

Pro události v rámci schématu cloudových událostí použijte pro klíč tyto hodnoty:

* ID události
* Zdroj
* Typ události
* EventTypeVersion
* Data události (jako data. klíč1)

Pro vlastní vstupní schéma použijte pole dat události (jako data. klíč1).

### <a name="values"></a>Hodnoty

Hodnoty mohou být:

* číslo
* řetězec
* boolean
* array

### <a name="limitations"></a>Omezení

Rozšířené filtrování má následující omezení:

* 5 rozšířených filtrů a 25 hodnot filtru napříč všemi filtry na odběr Event gridu
* 512 znaků na hodnotu řetězce
* Pět hodnot pro operátor **in** a **Not in**
* Klávesy se znakem ** `.` (tečka)** . Například: `http://schemas.microsoft.com/claims/authnclassreference` nebo `john.doe@contoso.com` . V současné době není k dispozici podpora řídicích znaků v klíčích. 

Stejný klíč lze použít ve více než jednom filtru.

### <a name="examples"></a>Příklady

### <a name="stringcontains"></a>StringContains

```json
"advancedFilters": [{
    "operatorType": "StringContains",
    "key": "data.key1",
    "values": [
        "microsoft", 
        "azure"
    ]
}]
```

### <a name="stringbeginswith"></a>StringBeginsWith

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "grid"
    ]
}]
```

### <a name="stringendswith"></a>StringEndsWith

```json
"advancedFilters": [{
    "operatorType": "StringEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

### <a name="stringin"></a>StringIn

```json
"advancedFilters": [{
    "operatorType": "StringIn",
    "key": "data.key1",
    "values": [
        "exact", 
        "string", 
        "matches"
    ]
}]
```

### <a name="stringnotin"></a>StringNotIn

```json
"advancedFilters": [{
    "operatorType": "StringNotIn",
    "key": "data.key1",
    "values": [
        "aws", 
        "bridge"
    ]
}]
```

### <a name="numberin"></a>NumberIn

```json

"advancedFilters": [{
    "operatorType": "NumberIn",
    "key": "data.counter",
    "values": [
        5,
        1
    ]
}]

```

### <a name="numbernotin"></a>NumberNotIn

```json
"advancedFilters": [{
    "operatorType": "NumberNotIn",
    "key": "data.counter",
    "values": [
        41,
        0,
        0
    ]
}]
```

### <a name="numberlessthan"></a>NumberLessThan

```json
"advancedFilters": [{
    "operatorType": "NumberLessThan",
    "key": "data.counter",
    "value": 100
}]
```

### <a name="numbergreaterthan"></a>NumberGreaterThan

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThan",
    "key": "data.counter",
    "value": 20
}]
```

### <a name="numberlessthanorequals"></a>NumberLessThanOrEquals

```json
"advancedFilters": [{
    "operatorType": "NumberLessThanOrEquals",
    "key": "data.counter",
    "value": 100
}]
```

### <a name="numbergreaterthanorequals"></a>NumberGreaterThanOrEquals

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThanOrEquals",
    "key": "data.counter",
    "value": 30
}]
```

### <a name="boolequals"></a>BoolEquals

```json
"advancedFilters": [{
    "operatorType": "BoolEquals",
    "key": "data.isEnabled",
    "value": true
}]
```


## <a name="next-steps"></a>Další kroky

* Další informace o filtrování událostí pomocí PowerShellu a rozhraní příkazového řádku Azure najdete v tématu [filtrování událostí pro Event Grid](how-to-filter-events.md).
* Pokud chcete rychle začít používat Event Grid, přečtěte si téma [Vytvoření a směrování vlastních událostí pomocí Azure Event Grid](custom-event-quickstart.md).

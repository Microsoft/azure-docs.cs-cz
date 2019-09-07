---
title: Filtrování událostí pro Azure Event Grid
description: Popisuje, jak filtrovat události při vytváření předplatného Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: f9fca0a9fefb5959747a4492139ae422a118db02
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390177"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Principy filtrování událostí pro předplatná Event Grid

Tento článek popisuje různé způsoby, jak filtrovat, které události se odešlou do vašeho koncového bodu. Při vytváření odběru událostí máte k dispozici tři možnosti pro filtrování:

* Typy událostí
* Předmět začíná na nebo končí
* Pokročilá pole a operátory

## <a name="event-type-filtering"></a>Filtrování typu události

Ve výchozím nastavení jsou všechny [typy událostí](event-schema.md) pro zdroj události odesílány do koncového bodu. Do koncového bodu se můžete rozhodnout odeslat jenom určité typy událostí. Můžete například dostávat oznámení o aktualizacích vašich prostředků, ale ne upozornění na jiné operace, jako je odstranění. V takovém případě filtr podle `Microsoft.Resources.ResourceWriteSuccess` typu události. Zadejte pole s typy událostí nebo určete `All` , že se mají načíst všechny typy událostí pro zdroj události.

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

Pro jednoduché filtrování podle předmětu zadejte počáteční nebo koncovou hodnotu předmětu. Například můžete určit, že má předmět ukončen `.txt` , aby se zobrazily pouze události související s odesláním textového souboru do účtu úložiště. Nebo můžete filtrovat předmět `/blobServices/default/containers/testcontainer` na začátku a získat tak všechny události pro tento kontejner, ale ne jiné kontejnery v účtu úložiště.

Při publikování událostí pro vlastní témata vytvořte předměty pro události, které předplatitelům umožní snadno zjistit, jestli se o událost zajímá. Předplatitelé používají vlastnost Subject k filtrování a směrování událostí. Zvažte přidání cesty pro místo, kde došlo k události, takže předplatitelé mohou filtrovat segmenty této cesty. Cesta umožňuje předplatitelům zúžit nebo široce filtrovat události. Pokud zadáte cestu tři segmenty, `/A/B/C` například v předmětu, můžou předplatitelé filtrovat podle prvního segmentu `/A` a získat tak širokou škálu událostí. Tyto předplatitelé získají události s předměty `/A/B/C` , `/A/D/E`jako je nebo. Jiní předplatitelé mohou filtrovat `/A/B` podle a získat tak užší sadu událostí.

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
* hodnota nebo hodnoty – hodnota nebo hodnoty, které se mají porovnat s klíčem.

Pokud zadáte jeden filtr s více hodnotami, provede se operace **nebo** , takže hodnota pole klíč musí být jedna z těchto hodnot. Zde naleznete příklad:

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

Pokud zadáte více různých filtrů, provede se operace **a** , takže je nutné splnit všechny podmínky filtru. Zde naleznete příklad: 

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

### <a name="operator"></a>Operator

K dispozici jsou operátory pro čísla:

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals
* NumberIn
* NumberNotIn

Dostupný operátor pro logické hodnoty je: BoolEquals

Dostupné operátory pro řetězce jsou:

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

Všechna porovnávání řetězců jsou Case-insensitve.

### <a name="key"></a>Klíč

Pro události ve schématu Event Grid použijte pro klíč následující hodnoty:

* id
* Téma
* Subject
* Typ události
* DataVersion
* Data události (jako data. klíč1)

Pro události v rámci schématu cloudových událostí použijte pro klíč tyto hodnoty:

* ID události
* Source
* Typ události
* EventTypeVersion
* Data události (jako data. klíč1)

Pro vlastní vstupní schéma použijte pole dat události (jako data. klíč1).

### <a name="values"></a>Hodnoty

Hodnoty mohou být:

* number
* řetězec
* boolean
* array

### <a name="limitations"></a>Omezení

Rozšířené filtrování má následující omezení:

* Pět rozšířených filtrů na odběr Event gridu
* 512 znaků na hodnotu řetězce
* Pět hodnot pro operátor **in** a **Not in**

Stejný klíč lze použít ve více než jednom filtru.

## <a name="next-steps"></a>Další postup

* Další informace o filtrování událostí pomocí PowerShellu a rozhraní příkazového řádku Azure najdete v tématu [filtrování událostí pro Event Grid](how-to-filter-events.md).
* Pokud chcete rychle začít používat služby Event Grid, přečtěte si téma [vytvoření a směrování vlastních událostí pomocí služby Azure Event Grid](custom-event-quickstart.md).

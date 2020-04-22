---
title: Filtrování událostí pro Azure Event Grid
description: Popisuje, jak filtrovat události při vytváření předplatného Služby Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: ce1bb3760ae73a9eaeee3cde957cc94841ebdf29
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731945"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Principy filtrování událostí pro odběry služby Event Grid

Tento článek popisuje různé způsoby filtrování událostí, které jsou odesílány do koncového bodu. Při vytváření odběru událostí máte tři možnosti filtrování:

* Typy událostí
* Předmět začíná nebo končí
* Pokročilá pole a operátory

## <a name="event-type-filtering"></a>Filtrování typu události

Ve výchozím nastavení jsou všechny [typy událostí](event-schema.md) pro zdroj událostí odeslány do koncového bodu. Můžete se rozhodnout odeslat pouze určité typy událostí do koncového bodu. Můžete například získat upozornění na aktualizace prostředků, ale není oznámeno pro jiné operace, jako je odstranění. V takovém případě filtrujte podle typu `Microsoft.Resources.ResourceWriteSuccess` události. Poskytněte pole s typy `All` událostí nebo určete, chcete-li získat všechny typy událostí pro zdroj události.

Syntaxe JSON pro filtrování podle typu události je:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Filtrování předmětů

Pro jednoduché filtrování podle předmětu zadejte počáteční nebo koncovou hodnotu objektu. Můžete například zadat zakončení `.txt` předmětu, chcete-li získat pouze události související s nahráním textového souboru do účtu úložiště. Nebo můžete filtrovat předmět začíná `/blobServices/default/containers/testcontainer` získat všechny události pro tento kontejner, ale ne jiné kontejnery v účtu úložiště.

Při publikování událostí do vlastních témat vytvořte pro události předměty, které předplatitelům usnadní zjistit, zda mají o událost zájem. Předplatitelé používají vlastnost předmětu k filtrování a směrování událostí. Zvažte přidání cesty, kde k události došlo, aby odběratelé mohli filtrovat podle segmentů této cesty. Cesta umožňuje odběratelům úzce nebo široce filtrovat události. Pokud zadáte cestu se `/A/B/C` třemi segmenty, jako je v `/A` předmětu, odběratelé mohou filtrovat podle prvního segmentu a získat širokou sadu událostí. Tito odběratelé získat události `/A/B/C` `/A/D/E`s předměty, jako je nebo . Ostatní odběratelé mohou `/A/B` filtrovat podle získat užší sadu událostí.

Syntaxe JSON pro filtrování podle předmětu je:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Rozšířené filtrování

Chcete-li filtrovat podle hodnot v datových polích a zadat operátor porovnání, použijte možnost rozšířeného filtrování. V rozšířeném filtrování zadáte:

* typ operátoru - typ porovnání.
* klíč – Pole v datech události, které používáte pro filtrování. Může to být číslo, logická hodnota nebo řetězec.
* hodnoty - Hodnota nebo hodnoty, které mají být porovnány s klíčem.

Pokud zadáte jeden filtr s více hodnotami, provede se operace **OR,** takže hodnota klíčového pole musí být jednou z těchto hodnot. Zde naleznete příklad:

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

Pokud zadáte více různých filtrů, provede se operace **AND,** takže musí být splněna každá podmínka filtru. Zde naleznete příklad: 

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

### <a name="operator"></a>Operátor

Dostupné operátory pro čísla jsou:

* NumberGreaterThan
* NumberGreaterThanOrEquals
* Bezpočet než
* NumberLessThanOrEquals
* Číslov
* NumberNotin

Dostupný operátor pro logické hodnoty je: BoolEquals

Dostupné operátory pro řetězce jsou:

* Obsahuje řetězec
* StringBeginsWith
* Řetězec endswith
* StringIn
* StringNotIn

Všechny porovnání řetězců jsou případ-insensitve.

### <a name="key"></a>Klíč

Pro události ve schématu Mřížka událostí použijte pro klíč následující hodnoty:

* ID
* Téma
* Subjekt
* Typ události
* Datová verze
* Data událostí (například Data.key1)

Pro události ve schématu Události cloudu použijte pro klíč následující hodnoty:

* Eventid
* Zdroj
* Typ události
* EventTypeVersion
* Data událostí (například Data.key1)

Pro vlastní vstupní schéma použijte datová pole událostí (například Data.key1).

### <a name="values"></a>Hodnoty

Hodnoty mohou být:

* číslo
* řetězec
* Boolean
* pole

### <a name="limitations"></a>Omezení

Rozšířené filtrování má následující omezení:

* Pět pokročilých filtrů na odběr mřížky událostí
* 512 znaků na hodnotu řetězce
* Pět hodnot pro **in** a **not in operátory**

Stejný klíč lze použít ve více než jednom filtru.

## <a name="next-steps"></a>Další kroky

* Další informace o filtrování událostí pomocí PowerShellu a Azure CLI najdete v [tématu Filtrování událostí pro Event Grid](how-to-filter-events.md).
* Pokud chcete rychle začít používat Event Grid, přečtěte [si tématu Vytváření a směrování vlastních událostí pomocí Azure Event Grid](custom-event-quickstart.md).

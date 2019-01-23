---
title: Událost filtrování pro Azure Event Grid
description: Popisuje, jak filtrovat události při vytváření předplatného služby Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: 87599b05a3569bf6f28880352185a131f48a7f52
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54470616"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Principy událostí filtrování pro předplatná Event gridu

Tento článek popisuje různé způsoby, jak filtrovat události, které se odesílají do vašeho koncového bodu. Při vytváření odběru událostí, máte tři možnosti filtrování:

* Typy událostí
* Předmět začíná nebo končí
* Pokročilé pole a operátory

## <a name="event-type-filtering"></a>Filtrování podle typu události

Ve výchozím nastavení všechny [typy událostí](event-schema.md) pro zdroj události, které se odesílají do koncového bodu. Můžete se rozhodnout odesílat pouze určité typy událostí do vašeho koncového bodu. Například můžete získat oznámení aktualizací pro vaše prostředky, ale ne upozornění pro další operace, jako je odstranění. V takovém případě filtrovat podle `Microsoft.Resources.ResourceWriteSuccess` typ události. Zadejte pole s typy událostí, nebo zadat `All` zobrazíte všechny typy událostí pro zdroje událostí.

Syntaxe JSON pro filtrování podle typu události je následující:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Předmět filtrování

Pro jednoduché filtrování podle předmětu, zadejte počáteční nebo koncová hodnota pro předmět. Například můžete zadat předmět končí `.txt` pouze zobrazíte události související s textový soubor se nahrávají na účet úložiště. Nebo můžete použít filtr předmětu začíná `/blobServices/default/containers/testcontainer` zobrazíte všechny události pro tohoto kontejneru, ale ne další kontejnery v účtu úložiště.

Při publikování událostí do vlastní témata, vytvořte předměty pro události, které usnadňují předplatitelům vědět, jestli máte zájem o události. Předplatitelé, použijte vlastnost předmětu k filtrování a směrování událostí. Zvažte přidání cesty pro kde k události došlo, takže předplatitelé můžete filtrovat podle segmentů cesty. Cesta umožňuje předplatitelům úzce nebo široce filtrovat události. Pokud zadáte tři segment cesty, například `/A/B/C` v předmětu, můžete filtrovat předplatitele první segment `/A` zobrazíte široké škály události. Tyto předplatitelé získají události s popsány oblasti jako `/A/B/C` nebo `/A/D/E`. Ostatní účastníky můžete filtrovat podle `/A/B` zobrazíte užší sadu událostí.

Syntaxe JSON pro filtrování podle typu události je následující:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Rozšířené filtrování

Filtrovat podle hodnot v datových polích a zadejte operátor porovnání, použijte možnost rozšířeného filtrování. V rozšířené filtrování, můžete zadat:

* typ operátoru - typ porovnání.
* klíč – pole v dat události, které používáte pro filtrování. To může být číslo, logickou hodnotu nebo řetězec.
* hodnota nebo hodnoty – hodnota nebo hodnoty k porovnání s klíči.

Syntaxi JSON pro používání rozšířených filtrů, je:

```json
"filter": {
  "advancedFilters": [
    {
      "operatorType": "NumberGreaterThanOrEquals",
      "key": "Data.Key1",
      "value": 5
    },
    {
      "operatorType": "StringContains",
      "key": "Subject",
      "values": ["container1", "container2"]
    }
  ]
}
```

### <a name="operator"></a>Operátor

Jsou k dispozici operátory pro čísla:

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals
* NumberIn
* NumberNotIn

Je k dispozici operátor pro logické hodnoty: BoolEquals

Jsou k dispozici operátory pro řetězce:

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

Veškeré porovnávání řetězců jsou insensitve případ.

### <a name="key"></a>Klíč

Pro události ve schématu služby Event Grid použijte následující hodnoty pro klíč:

* ID
* Téma
* Subjekt
* Typ události
* DataVersion
* Data událostí (např. Data.key1)

Pro události v cloudu události schéma použijte následující hodnoty pro klíč:

* ID události
* Zdroj
* Typ události
* EventTypeVersion
* Data událostí (např. Data.key1)

Pro vlastní vstupní schéma použijte pole dat události (jako jsou Data.key1).

### <a name="values"></a>Hodnoty

Hodnoty mohou být:

* číslo
* řetězec
* Boolean
* pole

### <a name="limitations"></a>Omezení

Rozšířené filtrování má následující omezení:

* Pět rozšířené filtry na odběr služby event grid
* 512 znaků na jednu řetězcovou hodnotu
* Pět hodnoty **v** a **není v** operátory
* Klíč může mít pouze jednu úroveň vnoření (např. data.key1)
* Schémata vlastních událostí, dají se filtrovat pouze na pole nejvyšší úrovně

Stejný klíč můžete použít ve více než jeden filtr.

## <a name="next-steps"></a>Další postup

* Další informace o filtrování událostí pomocí Powershellu a Azure CLI najdete v tématu [pro Event Grid umožňuje filtrovat události](how-to-filter-events.md).
* Pokud chcete rychle začít používat služby Event Grid, přečtěte si téma [vytvoření a směrování vlastních událostí pomocí služby Azure Event Grid](custom-event-quickstart.md).

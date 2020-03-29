---
title: Vytvoření syntetického klíče oddílu v Azure Cosmos DB
description: Zjistěte, jak používat syntetické klíče oddílů v kontejnerech Azure Cosmos k rovnoměrné distribuci dat a úloh mezi klíče oddílů.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: e8786c2d6e93c18a5bf9856a5555d6b528f842c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441220"
---
# <a name="create-a-synthetic-partition-key"></a>Vytvoření syntetického klíče oddílu

Je vhodné mít klíč oddílu s mnoha odlišnými hodnotami, například stovky nebo tisíce. Cílem je rovnoměrně distribuovat data a úlohy mezi položky přidružené k těmto hodnotám klíče oddílu. Pokud taková vlastnost v datech neexistuje, můžete vytvořit *syntetický klíč oddílu*. Tento dokument popisuje několik základních technik pro generování syntetického klíče oddílu pro kontejner Cosmos.

## <a name="concatenate-multiple-properties-of-an-item"></a>Zřetězit více vlastností položky

Klíč oddílu můžete vytvořit zřetězením více hodnot vlastností do jedné umělé `partitionKey` vlastnosti. Tyto klíče jsou označovány jako syntetické klíče. Zvažte například následující ukázkový dokument:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Pro předchozí dokument je jednou z možností nastavení /deviceId nebo /date jako klíč oddílu. Tuto možnost použijte, pokud chcete oddíl kontejneru na základě ID zařízení nebo data. Další možností je zřetězit tyto dvě `partitionKey` hodnoty do syntetické vlastnosti, která se používá jako klíč oddílu.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

Ve scénářích v reálném čase můžete mít tisíce položek v databázi. Namísto ručního přidání syntetického klíče definujte logiku na straně klienta, abyste zřetězili hodnoty, a vložte syntetický klíč do položek v kontejnerech Cosmos.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Použití klíče oddílu s náhodnou příponou

Další možnou strategií pro rovnoměrnější distribuci úlohy je připojit náhodné číslo na konec hodnoty klíče oddílu. Při distribuci položek tímto způsobem můžete provádět paralelní operace zápisu napříč oddíly.

Příkladem je, pokud klíč oddílu představuje datum. Můžete zvolit náhodné číslo mezi 1 a 400 a zřetězit jej jako příponu k datu. Výsledkem této metody jsou `2018-08-09.1``2018-08-09.2`hodnoty klíče oddílu, jako je , a tak dále, prostřednictvím `2018-08-09.400`. Vzhledem k tomu, že randomizovat klíč oddílu, operace zápisu na kontejneru na každý den jsou rovnoměrně rozloženy mezi více oddílů. Tato metoda má za následek lepší paralelismus a celkově vyšší propustnost.

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>Použití klíče oddílu s předem vypočtenými příponami 

Strategie náhodné přípony může výrazně zlepšit propustnost zápisu, ale je obtížné číst konkrétní položku. Neznáte hodnotu přípony, která byla použita při zapisujepoložku. Chcete-li usnadnit čtení jednotlivých položek, použijte předem vypočtenou strategii příponek. Namísto použití náhodného čísla k distribuci položek mezi oddíly použijte číslo, které je vypočteno na základě něčeho, na co chcete dotazovat.

Vezměme si předchozí příklad, kde kontejner používá datum jako klíč oddílu. Nyní předpokládejme, že `Vehicle-Identification-Number` `VIN`každá položka má atribut ( ), ke kterému chceme získat přístup. Dále předpokládejme, že často spouštět `VIN`dotazy najít položky podle , kromě data. Předtím, než aplikace zapíše položku do kontejneru, může vypočítat příponu hash na základě VIN a připojit ji k datu klíče oddílu. Výpočet může generovat číslo mezi 1 a 400, které je rovnoměrně rozloženo. Tento výsledek je podobný výsledkům vytvořeným metodou strategie náhodné přípony. Hodnota klíče oddílu je pak datum spojené s vypočteným výsledkem.

S touto strategií zápisy jsou rovnoměrně rozloženy mezi hodnoty klíče oddílu a přes oddíly. Můžete snadno číst konkrétní položku a datum, protože můžete vypočítat hodnotu klíče oddílu pro konkrétní `Vehicle-Identification-Number`. Výhodou této metody je, že se můžete vyhnout vytvoření jednoho klíče aktivního oddílu, tj. 

## <a name="next-steps"></a>Další kroky

Další informace o konceptu dělení najdete v následujících článcích:

* Další informace o [logických oddílech](partition-data.md).
* Další informace o tom, jak [zřídit propustnost na kontejnery A databáze Azure Cosmos](set-throughput.md).
* Zjistěte, jak [zřídit propustnost v kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
* Zjistěte, jak [zřídit propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).

---
title: Vytvoření klíče syntetického oddílu v Azure Cosmos DB
description: Naučte se používat syntetické klíče oddílů v kontejnerech Azure Cosmos k distribuci dat a zatížení rovnoměrně napříč klíči oddílů.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: e8786c2d6e93c18a5bf9856a5555d6b528f842c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75441220"
---
# <a name="create-a-synthetic-partition-key"></a>Vytvoření syntetického klíče oddílu

Osvědčeným postupem je klíč oddílu s velkým množstvím různých hodnot, jako jsou stovky nebo tisíce. Cílem je distribuovat vaše data a zatížení napříč položkami přidruženými k těmto hodnotám klíče oddílu. Pokud taková vlastnost ve vašich datech neexistuje, můžete vytvořit *syntetický klíč oddílu*. Tento dokument popisuje několik základních postupů pro generování syntetického klíče oddílu pro váš Cosmos kontejner.

## <a name="concatenate-multiple-properties-of-an-item"></a>Zřetězení více vlastností položky

Klíč oddílu můžete vytvořit zřetězením více hodnot vlastností do jediné umělé `partitionKey` Vlastnosti. Tyto klíče se označují jako syntetické klíče. Zvažte například následující příklad dokumentu:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Pro předchozí dokument je jednou z možností nastavit/deviceId nebo/Date jako klíč oddílu. Tuto možnost použijte, pokud chcete rozdělit kontejner na základě ID nebo data zařízení. Další možností je zřetězit tyto dvě hodnoty do syntetické `partitionKey` vlastnosti, která se používá jako klíč oddílu.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

V případě scénářů v reálném čase můžete mít v databázi tisíce položek. Namísto ručního přidávání syntetického klíče definujte logiku na straně klienta pro zřetězení hodnot a vložte syntetický klíč do položek v kontejnerech Cosmos.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Použít klíč oddílu s náhodnou příponou

Další možnou strategií pro distribuci úloh je, že se na konci hodnoty klíče oddílu připojí náhodné číslo. Při distribuci položek tímto způsobem můžete provádět paralelní operace zápisu napříč oddíly.

Příkladem je, že klíč oddílu představuje datum. Můžete zvolit náhodné číslo mezi 1 a 400 a zřetězit ho jako příponu k datu. Tato metoda má za následek hodnoty klíče oddílu jako  `2018-08-09.1` , `2018-08-09.2` a tak dále  `2018-08-09.400` . Vzhledem k tomu, že je klíč oddílu náhodně, operace zápisu na kontejneru v každém dni jsou rovnoměrně rozloženy mezi několik oddílů. Výsledkem této metody je lepší paralelismus a celková vyšší propustnost.

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>Použít klíč oddílu s předem vypočítanými příponami 

Strategie náhodné přípony může významně zlepšit propustnost zápisu, ale je obtížné si přečíst konkrétní položku. Neznáte hodnotu přípony, která se použila při zapsání položky. Aby bylo snazší číst jednotlivé položky, použijte strategii předběžně vypočítaných přípon. Místo použití náhodného čísla k distribuci položek mezi oddíly použijte číslo, které se vypočítá na základě něčeho, co chcete dotazovat.

Vezměte v úvahu předchozí příklad, kde kontejner používá jako klíč oddílu datum. Nyní předpokládejme, že každá položka má  `Vehicle-Identification-Number` `VIN` atribut (), ke kterému chceme získat přístup. Dále Předpokládejme, že často spouštíte dotazy pro hledání položek podle `VIN` data. Předtím, než aplikace zapíše položku do kontejneru, může vypočítat příponu hash založenou na kódu VIN a připojit ji k datu klíče oddílu. Výpočet může vygenerovat číslo mezi 1 a 400, které je rovnoměrně distribuováno. Tento výsledek je podobný výsledkům, které vytvořila metoda strategie náhodné přípony. Hodnota klíče oddílu je pak datum zřetězení s vypočítaným výsledkem.

V této strategii jsou zápisy rovnoměrně rozloženy mezi hodnoty klíčů oddílu a mezi oddíly. Můžete snadno přečíst konkrétní položku a datum, protože můžete vypočítat hodnotu klíče oddílu pro konkrétní `Vehicle-Identification-Number` . Výhodou této metody je, že se můžete vyhnout vytváření jediného aktivního klíče oddílu, tj. klíč oddílu, který přebírá všechny úlohy. 

## <a name="next-steps"></a>Další kroky

Další informace o konceptu dělení na oddíly najdete v následujících článcích:

* Přečtěte si další informace o [logických oddílech](partition-data.md).
* Přečtěte si další informace o tom, jak [zřídit propustnost na Cosmosch kontejnerech a databázích Azure](set-throughput.md).
* Naučte se [zřídit propustnost v kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
* Naučte se [zřídit propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).

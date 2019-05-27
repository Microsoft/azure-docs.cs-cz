---
title: Vytvořte klíč syntetické oddíl ve službě Azure Cosmos DB rovnoměrně distribuovat data a úlohy.
description: Další informace o použití klíče syntetické oddílů v kontejnerech Azure Cosmos
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.openlocfilehash: 1fd436746dcd2e93a1699ac5c68965213c74580e
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978863"
---
# <a name="create-a-synthetic-partition-key"></a>Vytvoření syntetického klíče oddílu

Je osvědčeným postupem klíče oddílu s mnoha různých hodnot, například stovkách nebo tisících. Cílem je rovnoměrně distribuovat data a úlohy napříč položky související s těmito hodnotami klíče oddílu. Pokud tato vlastnost neexistuje ve vašich datech, musíte postavit *klíč oddílu syntetické*. Tento dokument popisuje několik základních technik pro generování klíče oddílu syntetické kontejneru Cosmos.

## <a name="concatenate-multiple-properties-of-an-item"></a>Zřetězit více vlastností položky

Při vytváření klíče oddílu zřetězením více hodnot vlastností do jednoho umělé `partitionKey` vlastnost. Tyto klíče se označují jako syntetická klíče. Zvažte například následující ukázkový dokument:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Pro předchozí dokument je jednou z možností nastavit /deviceId nebo /date jako klíč oddílu. Tuto možnost použijte, pokud chcete při vytváření oddílů kontejneru na základě ID zařízení nebo data. Další možností je ke zřetězení těchto dvou hodnot do syntetickém `partitionKey` vlastnost, která se používá jako klíč oddílu.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

V scénáře v reálném čase může mít tisíců položek v databázi. Místo přidání syntetické klíč ručně, definujte logiku na straně klienta a zřetězí hodnoty a vložit syntetické klíč do položek v kontejnerech Cosmos.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Klíč oddílu pomocí náhodného příponu

Další z možných postupů k distribuci zatížení rozkládalo rovnoměrněji je připojit náhodné číslo na konci hodnotu klíče oddílu. Při distribuci položky tímto způsobem můžete provádět paralelní zápisu operace napříč oddíly.

Příkladem je, pokud se klíče oddílu představuje datum. Může být zvolte náhodné číslo mezi 1 a 400 a zřetězit ho jako příponu k datu. Tato metoda má za následek hodnoty klíče oddílu jako `2018-08-09.1`,`2018-08-09.2`, a tak dále prostřednictvím `2018-08-09.400`. Protože je náhodně klíče oddílu, operací zápisu na kontejneru na každý den jsou rovnoměrně rozdělit mezi několik oddílů. Tato metoda je výsledkem lepší paralelismu a celkově vyšší propustnost.

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>Použití klíče oddílu s předem vypočítané přípony 

Náhodné přípona strategie může výrazně zlepšit propustnost zápisu, ale je obtížné číst specifické položky. Hodnota přípony, které jste použili při jste napsali položky neznáte. Aby bylo snazší přečíst jednotlivých položek, použijte strategii předvypočítaný přípony. Namísto použití náhodné číslo k distribuci položek mezi oddíly, použijte číslo, které se počítá podle něco, co chcete zadat dotaz.

Podívejte se na předchozí příklad, kdy kontejner používá datum jako klíč oddílu. Nyní předpokládejme, že každá položka má `Vehicle-Identification-Number` (`VIN`) atribut, který chceme, aby pro přístup k. Dále, Předpokládejme, že často spouštět dotazy k vyhledání položky podle `VIN`, kromě datum. Předtím, než je vaše aplikace zapíše položku do kontejneru, může vypočítat hodnotu hash přípona podle vozidla a připojte ho k datu klíče oddílu. Výpočet může vygenerovat číslo mezi 1 a 400, které jsou rovnoměrně rozloženy. Tento výsledek je podobný výsledky vytvořené metodou strategie náhodné příponu. Hodnota klíče oddílu je pak datum zřetězená s výsledek výpočtu.

Tato strategie jsou zápisy rovnoměrně rozděleny mezi hodnoty klíče oddílu a napříč oddíly. Snadno najdete konkrétní položku a datum, protože můžete vypočítat hodnotu klíče oddílu pro konkrétní `Vehicle-Identification-Number`. Výhodou této metody je, že se můžete vyhnout vytvořením jednoho výměně klíče oddílu, například klíč oddílu, který přebírá všechny úlohy. 

## <a name="next-steps"></a>Další postup

Podrobnější informace o dělení koncept v následujících článcích:

* Další informace o [logické oddíly](partition-data.md).
* Další informace o tom, jak [zřizování propustnosti na kontejnery Azure Cosmos a databází](set-throughput.md).
* Zjistěte, jak [zřizování propustnosti kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
* Zjistěte, jak [zřídit propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).

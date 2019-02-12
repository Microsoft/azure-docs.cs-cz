---
title: Vytvořte klíč syntetické oddíl ve službě Azure Cosmos DB rovnoměrně distribuovat data a úlohy.
description: Další informace o použití klíče syntetické oddílů v kontejnerech Azure Cosmos
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: mjbrown
ms.openlocfilehash: 8becfe375f2e887348729cf1d76820fc41156d2a
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997096"
---
# <a name="create-a-synthetic-partition-key"></a>Vytvoření syntetického klíče oddílu

Je vhodné mít klíč oddílu s mnoha různých hodnot, například stovkách nebo tisících. Cílem je rovnoměrně distribuovat data a úlohy napříč položky související s těmito hodnotami klíče oddílu. Pokud tato vlastnost neexistuje ve vašich datech, můžete vytvořit klíč syntetické oddílu. Následující části popisují několik základních technik pro generování klíče syntetické oddílu pro váš kontejner.

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

Scénáře v reálném čase máte tisíce dokumenty v databázi. Místo přidání syntetické klíč ručně, definujte logiku na straně klienta pro zřetězení hodnoty a vložit do dokumentů syntetické klíč.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Klíč oddílu pomocí náhodného příponu

Další z možných postupů k distribuci zatížení rozkládalo rovnoměrněji je připojit náhodné číslo na konci hodnotu klíče oddílu. Při distribuci položky tímto způsobem můžete provádět paralelní zápisu operace napříč oddíly.

Příkladem je, pokud se klíče oddílu představuje datum. Může být zvolte náhodné číslo mezi 1 a 400 a zřetězit ho jako příponu k datu. Tato metoda je výsledkem hodnoty klíče oddílu jako 2018-08-09.1 2018-08-09.2 a tak dále, prostřednictvím 2018-08-09.400. Protože je náhodně klíče oddílu, operací zápisu na kontejneru na každý den jsou rovnoměrně rozdělit mezi několik oddílů. Tato metoda je výsledkem lepší paralelismu a celkově vyšší propustnost.

## <a name="use-a-partition-key-with-precalculated-suffixes"></a>Použití klíče oddílu s předem vypočítaných přípony 

Randomizing strategie může výrazně zlepšit propustnost zápisu, ale je obtížné číst specifické položky. Hodnota přípony, který se používá při zápisu položky neznáte. Aby bylo snazší přečíst jednotlivých položek, použijte strategii předem vypočítaných přípony. Namísto použití náhodné číslo k distribuci položek mezi oddíly, použití číslo, které vypočítá založené na něco, co chcete zadat dotaz.

Podívejte se na předchozí příklad, kdy kontejner používá data v klíč oddílu. Nyní předpokládejme, že každá položka má atribut přístupné vozidla. identifikační číslo (VIN). Dál Předpokládejme, často spouštět dotazy k vyhledání položky podle VIN, kromě datum. Předtím, než je vaše aplikace zapíše položku do kontejneru, může vypočítat hodnotu hash přípona podle vozidla a připojte ho k datu klíče oddílu. Výpočet může vygenerovat číslo mezi 1 a 400, které jsou rovnoměrně rozloženy. Tento výsledek je podobný výsledky vytvořené metodou náhodné strategie. Hodnota klíče oddílu je pak datum zřetězená s výsledek výpočtu.

Tato strategie jsou zápisy rovnoměrně rozděleny mezi hodnoty klíče oddílu a napříč oddíly. Protože můžete vypočítat hodnotu klíče oddílu pro konkrétní vozidla – identifikace – číslo, může číst snadno konkrétní položku a datum. Výhodou této metody je, že se můžete vyhnout vytvořením jednoho výměně klíče oddílu. Horkými oddíly klíč je klíč oddílu, který přebírá všechny úlohy. 

## <a name="next-steps"></a>Další postup

Podrobnější informace o dělení koncept v následujících článcích:

* Další informace o [logické oddíly](partition-data.md).
* Další informace o tom, jak [zřizování propustnosti na kontejnery Azure Cosmos a databází](set-throughput.md).
* Zjistěte, jak [zřizování propustnosti kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
* Zjistěte, jak [zřídit propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).

---
title: Klíče oddílů syntetické ve službě Azure Cosmos DB
description: Další informace o použití klíče syntetické oddílů v kontejnerech služby Azure Cosmos DB
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: andrl
ms.openlocfilehash: f6323549c0ecf8f0196d327779f7f7cb67c6e03f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263509"
---
# <a name="create-a-synthetic-partition-key"></a>Vytvořit klíč syntetické oddílu

Je vhodné mít klíč oddílu s mnoha jedinečných hodnot, jako je například stovkách nebo tisících. Cílem je rovnoměrně distribuovat data a úlohy napříč položky související s těmito hodnotami klíče oddílu. Pokud tato vlastnost neexistuje ve vašich datech, lze zkonstruovat klíč syntetické oddílu. Následující části popisují několik základních technik pro generování klíče syntetické oddílu pro váš kontejner.

## <a name="concatenating-multiple-properties-of-an-item"></a>Zřetězení více vlastností položky

Při vytváření klíče oddílu zřetězením více hodnot vlastností do jednoho umělé `partitionKey` vlastnost. Tyto klíče se označují jako syntetická klíče. Zvažte například následující ukázkový dokument:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Pro předchozí dokument je jednou z možností nastavte /deviceId nebo /date jako klíč oddílu, pokud chcete při vytváření oddílů kontejneru na základě ID zařízení nebo data. Další možností je ke zřetězení těchto dvou hodnot do syntetickém `partitionKey` vlastnost, která se používá jako klíč oddílu.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

V scénáře v reálném čase, máte tisíce dokumenty v databázi, takže místo přidání syntetické klíč ručně, byste měli definovat logiku na straně klienta pro zřetězení hodnoty a vložit do dokumentů syntetické klíč.

## <a name="using-a-partition-key-with-random-suffix"></a>Použití klíče oddílu s příponou náhodné

Další z možných postupů k distribuci zatížení rozkládalo rovnoměrněji je připojit náhodné číslo na konci hodnotu klíče oddílu. Distribuce položky v tento způsob, jak umožňuje paralelní provádění operací zápisu napříč oddíly.

Například pokud klíče oddílu představuje datum, můžete zvolit náhodné číslo mezi 1 a 400 a zřetězit ho jako příponu k datu. Tato metoda je výsledkem hodnoty klíče oddílu jako 2018-08-09.1 2018-08-09.2 a tak dále, prostřednictvím 2018-08-09.400. Protože se klíč oddílu rámci procesu náhodného řazení, operací zápisu na kontejneru na každý den jsou rovnoměrně rozdělit mezi několik oddílů. Tato metoda je výsledkem lepší paralelismu a celkově vyšší propustnost.

## <a name="using-a-partition-key-with-pre-calculated-suffixes"></a>Použití klíče oddílu s předem vypočítané přípony 

I když rámci procesu náhodného řazení strategie může výrazně zlepšit propustnost zápisu, je obtížné číst určitou položku, protože zatím nevíte hodnota přípony, který je použit při zápisu položky. Aby bylo snazší přečíst jednotlivých položek, můžete použít předem vypočítané přípony strategie. Namísto použití náhodné číslo k distribuci položek mezi oddíly, použití číslo, které vypočítá založené na něco, co chcete zadat dotaz.

Podívejte se na předchozí příklad, kdy kontejner používá data v klíč oddílu. Nyní předpokládejme, že každá položka má atribut přístupné VIN (vozidla identifikační číslo) a pokud je často spustit dotazy k vyhledání položky podle VIN, navíc k datu. Předtím, než je vaše aplikace zapíše položku do kontejneru, může vypočítat hodnotu hash přípona podle vozidla a připojte ho k datu klíče oddílu. Výpočet může vygenerovat číslo mezi 1 a 400, která je distribuovaná rovnoměrně, podobné výsledky vytvořené metodou náhodné strategie. Hodnota klíče oddílu pak budou data zřetězená s výsledek výpočtu.

Tato strategie jsou zápisy rovnoměrně rozděleny mezi hodnoty klíče oddílu a napříč oddíly. Snadno můžete číst konkrétní položku a datum, protože lze vypočítat hodnotu klíče oddílu pro konkrétní vozidla – identifikace – číslo. Výhodou této metody je, že se můžete vyhnout vytvořením jednoho výměně klíče oddílu (klíč oddílu, který přebírá všechny úlohy). 

## <a name="next-steps"></a>Další postup

Podrobnější informace o dělení koncept v následujících článcích:

* Další informace o [logickými oddíly](partition-data.md)
* Další informace o [zřizování propustnosti na Cosmos kontejnerů a databáze](set-throughput.md)
* Přečtěte si [jak zřídit propustnosti kontejneru Cosmos](how-to-provision-container-throughput.md)
* Přečtěte si [jak zřídit propustnost v databázi Cosmos](how-to-provision-database-throughput.md)

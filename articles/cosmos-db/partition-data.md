---
title: Dělení a horizontální škálování ve službě Azure Cosmos DB
description: Zjistěte, jak funguje dělení v Azure Cosmos DB, jak nakonfigurovat klíče oddílů a oddílů a jak vybrat správný klíč oddílu pro vaši aplikaci.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: cbd171e10cc1a8b27de98d9d4d779f345ac5a3ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246614"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Dělení a horizontální škálování ve službě Azure Cosmos DB

Tento článek vysvětluje fyzické a logické oddíly ve službě Azure Cosmos DB. Popisuje také osvědčené postupy pro škálování a dělení. 

## <a name="logical-partitions"></a>Logické oddíly

Logický oddíl se skládá ze sady položek, které mají stejný klíč oddílu. Například v kontejneru, kde `City` všechny položky `City` obsahují vlastnost, můžete použít jako klíč oddílu pro kontejner. Skupiny položek, které `City`mají specifické `London` `Paris`hodnoty `NYC`pro , například , , a , tvoří odlišné logické oddíly. Při odstranění podkladových dat se nemusíte obávat odstranění oddílu.

V Azure Cosmos DB je kontejner základní jednotkou škálovatelnosti. Data, která se přidají do kontejneru a propustnost, kterou zřídíte v kontejneru, jsou automaticky (vodorovně) rozdělena napříč sadou logických oddílů. Data a propustnost jsou rozděleny na základě klíče oddílu, který zadáte pro kontejner Azure Cosmos. Další informace najdete [v tématu Vytvoření kontejneru Azure Cosmos](how-to-create-container.md).

Logický oddíl také definuje rozsah databázových transakcí. Položky v logickém oddílu můžete aktualizovat pomocí [transakce s izolací snímků](database-transactions-optimistic-concurrency.md). Při přidání nových položek do kontejneru jsou systémem transparentně vytvořeny nové logické oddíly.

## <a name="physical-partitions"></a>Fyzické oddíly

Kontejner Azure Cosmos se škáluje distribucí dat a propustnost íve velké množství logických oddílů. Interně je jeden nebo více logických oddílů mapováno na fyzický oddíl, který se skládá ze sady replik, označovaných také jako [*sada replik*](global-dist-under-the-hood.md). Každá sada replik hostuje instanci databázového stroje Azure Cosmos. Sada replik umožňuje data uložená v rámci fyzického oddílu trvanlivé, vysoce dostupné a konzistentní. Fyzický oddíl podporuje maximální velikost jednotek úložiště a požadavků (RU). Každá replika, která tvoří fyzický oddíl, zdědí kvótu úložiště oddílu. Všechny repliky fyzického oddílu společně podporují propustnost, která je přidělena fyzickému oddílu. 

Následující obrázek ukazuje, jak jsou logické oddíly mapovány na fyzické oddíly, které jsou distribuovány globálně:

![Bitová kopie, která demonstruje dělení azure cosmos DB](./media/partition-data/logical-partitions.png)

Propustnost zřízená pro kontejner je rovnoměrně rozdělena mezi fyzické oddíly. Návrh klíče oddílu, který nedistribuuje požadavky na propustnost rovnoměrně může vytvořit "horké" oddíly. Aktivní oddíly může mít za následek omezení rychlosti a v neefektivní použití zřízená propustnost a vyšší náklady.

Na rozdíl od logických oddílů jsou fyzické oddíly interní implementací systému. Nelze řídit velikost, umístění nebo počet fyzických oddílů a nelze řídit mapování mezi logickými oddíly a fyzické oddíly. Můžete však řídit počet logických oddílů a distribuci dat, zatížení a propustnost [výběrem správného klíče logického oddílu](partitioning-overview.md#choose-partitionkey).

## <a name="next-steps"></a>Další kroky

* Informace o [výběru klíče oddílu](partitioning-overview.md#choose-partitionkey).
* Informace o [zřízené propustnosti v Azure Cosmos DB](request-units.md).
* Další informace o [globální distribuci v Azure Cosmos DB](distribute-data-globally.md).
* Zjistěte, jak [zřídit propustnost v kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
* Zjistěte, jak [zřídit propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).

---
title: Dělení a horizontální škálování v Azure Cosmos DB
description: Přečtěte si o tom, jak dělení funguje v Azure Cosmos DB, jak nakonfigurovat dělení a klíče oddílů a jak zvolit správný klíč oddílu pro vaši aplikaci.
ms.author: rimman
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 8f83c40aeecdbf9ca30adc20286712850882ee41
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616796"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Dělení a horizontální škálování v Azure Cosmos DB

Tento článek vysvětluje fyzické a logické oddíly v Azure Cosmos DB. Popisuje také osvědčené postupy pro škálování a rozdělení do oddílů. 

## <a name="logical-partitions"></a>Logické oddíly

Logický oddíl obsahuje sadu položek, které mají stejný klíč oddílu. Například v kontejneru, kde všechny položky obsahují `City` vlastnost, lze použít `City` jako klíč oddílu pro kontejner. Skupiny položek, které mají specifické hodnoty pro `City`, `London`například `Paris`, a `NYC`, tvoří rozdílné logické oddíly. Nemusíte se starat o odstranění oddílu, když se odstraní podkladová data.

V Azure Cosmos DB je kontejner základní jednotkou škálovatelnosti. Data přidaná do kontejneru a propustnost, kterou zřizujete na kontejneru, jsou automaticky (horizontálně) rozdělená na oddíly v rámci sady logických oddílů. Data a propustnost jsou rozdělené na oddíly na základě klíče oddílu, který zadáte pro kontejner Azure Cosmos. Další informace najdete v tématu [vytvoření kontejneru Azure Cosmos](how-to-create-container.md).

Logický oddíl také definuje rozsah databázových transakcí. Položky v rámci logického oddílu lze aktualizovat pomocí [transakce s izolací snímku](database-transactions-optimistic-concurrency.md). Při přidání nových položek do kontejneru jsou nové logické oddíly transparentně vytvořeny systémem.

## <a name="physical-partitions"></a>Fyzické oddíly

Kontejner Azure Cosmos se škáluje distribucí dat a propustnosti napříč velkým počtem logických oddílů. Interně je jeden nebo více logických oddílů namapovaných na fyzický oddíl, který se skládá ze sady replik, označovaných také jako [*sada replik*](global-dist-under-the-hood.md). Každá sada replik je hostitelem instance databázového stroje Azure Cosmos. Sada replik vytvoří data uložená v rámci fyzického oddílu odolného, vysoce dostupného a konzistentního. Fyzický oddíl podporuje maximální velikost úložiště a jednotky žádostí (ru). Každá replika, která tvoří fyzický oddíl, zdědí kvótu úložiště oddílu. Všechny repliky fyzického oddílu společně podporují propustnost, která je přidělena fyzickému oddílu. 

Následující obrázek ukazuje, jak jsou logické oddíly namapovány na fyzické oddíly distribuované globálně:

![Obrázek, který ukazuje Azure Cosmos DB dělení](./media/partition-data/logical-partitions.png)

Propustnost zřízená pro kontejner je rozdělená rovnoměrně mezi fyzické oddíly. Návrh klíče oddílu, který nedistribuuje požadavky propustnosti rovnoměrně, může vytvořit oddíly "Hot". Aktivní oddíly můžou mít za následek omezení četnosti a neefektivní využívání zřízené propustnosti a vyšší náklady.

Na rozdíl od logických oddílů jsou fyzické oddíly interní implementací systému. Nemůžete ovládat velikost, umístění nebo počet fyzických oddílů a mapování mezi logickými oddíly a fyzickými oddíly nemůžete řídit. [Výběrem správného logického klíče oddílu](partitioning-overview.md#choose-partitionkey)ale můžete řídit počet logických oddílů a distribuci dat, zatížení a propustnost.

## <a name="next-steps"></a>Další postup

* Přečtěte si, jak [Vybrat klíč oddílu](partitioning-overview.md#choose-partitionkey).
* Přečtěte si o [zřízené propustnosti v Azure Cosmos DB](request-units.md).
* Přečtěte si o [globální distribuci v Azure Cosmos DB](distribute-data-globally.md).
* Naučte se [zřídit propustnost v kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
* Naučte se [zřídit propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).

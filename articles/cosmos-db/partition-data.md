---
title: Vytváření oddílů a horizontální škálování ve službě Azure Cosmos DB
description: Další informace o tom, jak dělení funguje v Azure Cosmos DB, jak nakonfigurovat, vytváření oddílů a klíče oddílu a jak vybrat správný oddíl klíč pro vaši aplikaci.
ms.author: mjbrown
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: dd62e0f4ff110ec8454031f1b66b56025328c33c
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54101475"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Vytváření oddílů a horizontální škálování ve službě Azure Cosmos DB

Tento článek vysvětluje, o fyzické a logické oddíly ve službě Azure Cosmos DB a osvědčené postupy pro škálování a rozdělení do oddílů. 

## <a name="logical-partitions"></a>Logické oddíly

Logický oddíl se skládá ze sady položek se stejným klíčem oddílu. Představte si třeba kontejner, ve kterém obsahovat všechny položky `City` vlastnosti a pak můžete použít `City` jako klíč oddílu pro kontejner. Skupiny položek s konkrétní hodnoty `City` , jako "Londýn", "Paříž", "NYC" atd. budou tvořit odlišné logického oddílu.

Kontejner ve službě Azure Cosmos DB, je základní jednotkou škálovatelnosti. Data přidaná do kontejneru a propustnost, kterou zřídíte v kontejneru se automaticky (vodorovně) rozdělené mezi sadu logické oddíly. Se dělí na základě klíče oddílu, který zadáte pro kontejneru Cosmos. Další informace najdete v tématu [určení klíče oddílu pro váš kontejner Cosmos](how-to-create-container.md) článku.

Logický oddíl definuje obor transakce databáze. Položky v rámci logického oddílu můžete aktualizovat pomocí transakce s izolací snímku. Při přidání nové položky do kontejneru, nové logické oddíly transparentně vytvořených systémem.

## <a name="physical-partitions"></a>Fyzické oddíly

Kontejner Azure Cosmos je škálování díky distribuci dat a propustnost velkým počtem logické oddíly. Interně, jeden nebo více logické oddíly se mapují na **fyzický oddíl** , který se skládá ze sady replik se označuje také jako sady replik. Každé sady replik je hostitelem instance databázového stroje Azure Cosmos. Sady replik díky tomu budou data uložená v rámci oddílu fyzického odolné, vysoce dostupné a konzistentní vzhledem k aplikacím. Fyzický oddíl podporuje pevné, maximální velikost úložiště a RU. Každá replika zahrnující fyzický oddíl dědí kvóty úložiště. A všechny repliky fyzický oddíl souhrnně podporují propustnost přidělené na fyzický oddíl. Následující obrázek znázorňuje, jak logické oddíly se mapují na fyzické oddíly, které jsou globálně distribuované:

![Dělení služby Azure Cosmos DB](./media/partition-data/logical-partitions.png)

Zajištěné propustnosti pro kontejner je rozděleno rovnoměrně mezi fyzickými oddíly. K návrhu klíče oddílu, který nebude rovnoměrně distribuovat požadavky na propustnost proto můžou vytvářet "horkými" oddílů. Aktivních oddílů může vést k omezování rychlosti a neefektivní využití zřízené propustnosti.

Na rozdíl od logické oddíly jsou fyzické oddíly vnitřní implementace systému. Nelze určit jejich velikost, umístění, počet nebo mapování mezi logické oddíly a fyzickými oddíly. Však můžete řídit počet logické oddíly a distribuci dat a propustnost pomocí klávesy správný oddíl.

## <a name="next-steps"></a>Další postup

V tomto článku poskytuje přehled o dělení dat a osvědčené postupy pro škálování a rozdělení do oddílů ve službě Azure Cosmos DB. 

* Další informace o [zřízenou propustnost v databázi Azure Cosmos DB](request-units.md)
* Další informace o [globální distribuce ve službě Azure Cosmos DB](distribute-data-globally.md)
* Další informace o [zvolit klíč oddílu](partitioning-overview.md#choose-partitionkey)
* Přečtěte si [jak zřídit propustnosti kontejneru Cosmos](how-to-provision-container-throughput.md)
* Přečtěte si [jak zřídit propustnost v databázi Cosmos](how-to-provision-database-throughput.md)

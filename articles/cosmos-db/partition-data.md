---
title: Vytváření oddílů a horizontální škálování ve službě Azure Cosmos DB
description: Další informace o tom, jak dělení funguje v Azure Cosmos DB, jak nakonfigurovat, vytváření oddílů a klíče oddílu a jak zvolit klíč oddílu pravý pro vaši aplikaci.
ms.author: mjbrown
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 4c6847d8f870c02228aa14ab9e11c85b091ec48b
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56959948"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Vytváření oddílů a horizontální škálování ve službě Azure Cosmos DB

Tento článek vysvětluje fyzické a logické oddíly ve službě Azure Cosmos DB. Také popisuje osvědčené postupy pro škálování a rozdělení do oddílů. 

## <a name="logical-partitions"></a>Logické oddíly

Logický oddíl obsahuje sadu položek, které mají stejný klíč oddílu. Například v kontejneru, ve kterém obsahovat všechny položky `City` vlastností, můžete použít `City` jako klíč oddílu pro kontejner. Skupiny položek, které mají konkrétní hodnoty pro `City`, jako například `London`, `Paris`, a `NYC`, tvoří odlišné logického oddílu. Nemusíte se starat o odstranění oddílu při odstranění podkladová data.

Kontejner ve službě Azure Cosmos DB, je základní jednotkou škálovatelnosti. Data, která se přidá do kontejneru a propustnost, kterou zřídíte v kontejneru se automaticky (vodorovně) rozdělené mezi sadu logické oddíly. Data a propustnost dělí na základě klíče oddílu, který zadáte pro kontejner Azure Cosmos DB. Další informace najdete v tématu [vytvoření kontejneru služby Azure Cosmos DB](how-to-create-container.md).

Logický oddíl definuje obor transakce databáze. Položky v rámci logického oddílu můžete aktualizovat pomocí transakce s izolací snímku. Při přidání nové položky do kontejneru, nové logické oddíly transparentně vytvořených systémem.

## <a name="physical-partitions"></a>Fyzické oddíly

Kontejner služby Azure Cosmos DB je škálování díky distribuci dat a propustnost velkým počtem logické oddíly. Interně, jeden nebo více logické oddíly se mapují na fyzický oddíl, který se skládá ze sady replik, také nazývané *sady replik*. Každou repliku nastavit hostitelů instanci databázového stroje Azure Cosmos DB. Sady replik díky tomu budou data uložená v rámci oddílu fyzického odolné, vysoce dostupné a konzistentní vzhledem k aplikacím. Fyzický oddíl podporuje maximální velikost úložiště a jednotkách požadavků (ru). Každou repliku, která tvoří fyzický oddíl dědí kvótu úložiště oddílu. Všechny repliky fyzický oddíl souhrnně podporují propustnost, která je přidělena na fyzický oddíl. 

Následující obrázek znázorňuje, jak logické oddíly se mapují na fyzické oddíly, které jsou globálně distribuované:

![Obrázek, který ukazuje rozdělení do oddílů služby Azure Cosmos DB](./media/partition-data/logical-partitions.png)

Zajištěné propustnosti pro kontejner je rozděleno rovnoměrně mezi fyzickými oddíly. K návrhu klíče oddílu, který nebude rovnoměrně distribuovat požadavky na propustnost může vytvářet "horkými" oddílů. Aktivních oddílů, může dojít v omezení četnosti a neefektivní používání šablon zřízenou propustnost.

Na rozdíl od logické oddíly jsou fyzické oddíly vnitřní implementace systému. Nemůžete ovládat, velikost, umístění nebo počet fyzických oddílů, a nemůžete určit mapování mezi oddíly fyzické a logické oddíly. Však můžete řídit počet logické oddíly a distribuci dat a propustnost pomocí klávesy správný oddíl.

## <a name="next-steps"></a>Další postup

* Další informace o [zvolit klíč oddílu](partitioning-overview.md#choose-partitionkey).
* Další informace o [zřízenou propustnost v databázi Azure Cosmos DB](request-units.md).
* Další informace o [globální distribuce ve službě Azure Cosmos DB](distribute-data-globally.md).
* Zjistěte, jak [zřídit propustnost v kontejneru Azure Cosmos DB](how-to-provision-container-throughput.md).
* Zjistěte, jak [zřídit propustnost v databázi Azure Cosmos DB](how-to-provision-database-throughput.md).

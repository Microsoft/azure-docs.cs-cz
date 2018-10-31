---
title: Škálování propustnosti ve službě Azure Cosmos DB
description: Tento článek popisuje, jak službu Azure Cosmos DB Elasticky škáluje propustnost
services: cosmos-db
author: dharmas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/15/2018
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: adbac964e6654e16f6c405b9a5b8669326583f3e
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50244154"
---
# <a name="scaling-throughput-in-azure-cosmos-db"></a>Škálování propustnosti ve službě Azure Cosmos DB

Ve službě Azure Cosmos DB, zřízená propustnost je vyjádřena jako požadavek jednotek za sekundu (RU/s, plural: ru). Měření náklady na čtení a zápisu operace kontejneru Cosmos.

![Požadované jednotky](./media/scale-throughput/figure1.png)

Můžete zřídit RU v kontejneru Cosmos nebo v databázi Cosmos. RU zřízených v kontejneru je dostupná výhradně pro operace prováděné v tomto kontejneru. RU zřízených pro databázi jsou sdílena mezi všechny kontejnery v rámci této databáze (s výjimkou všechny kontejnery s exkluzivně přiřazená ru).

Elasticky škálovat propustnost směrem nahoru nebo dolů, můžete zvýšit nebo snížit zřízené RU/s v každém okamžiku. Další informace najdete v tématu Postupy: poskytování propustnost a Elasticky škálovat Cosmos kontejnerů a databáze. Globální škálování propustnosti, můžete přidání nebo odebrání oblastí na vašem účtu Cosmos kdykoli. Další informace najdete v tématu Postup přidání nebo odebrání oblastí k vašemu účtu Cosmos. Přidružení účtu Cosmos více oblastí je důležité pro mnoho scénářů s nízkou latencí dosáhnout a [vysoké dostupnosti](high-availability.md) po celém světě.

## <a name="throughput-scaling-details"></a>Podrobnosti o škálování propustnosti

Pokud zřizujete ru R v kontejneru Cosmos (nebo databáze), služby Cosmos DB zajišťuje, že jsou k dispozici v R ru *každý* z oblastí, přidružených k účtu Cosmos. Pokaždé, když přidáte novou oblast ke svému účtu Cosmos Cosmos DB automaticky zřídí ru R v nově přidaném oblasti. Operací provedených v kontejneru Cosmos je zaručeno, chcete-li získat R ru ve všech oblastí, přidružených k účtu Cosmos. Nelze selektivně přiřadit ru určité oblasti – zřízené ru zřízených pro databázi Cosmos kontejneru (nebo) pro všemi oblastmi spojenými s vaším účtem Cosmos.

Za předpokladu, že se v kontejneru Cosmos nakonfigurují R ru a existují N oblasti přidružené k účtu Cosmos, pak:

- Pokud je nakonfigurovaný účet Cosmos s oblastí jeden zápis, celkový počet ru dostupná globálně v kontejneru = R x N.
- Pokud je nakonfigurovaný účet Cosmos s využitím více oblastí zápisu, celkový počet ru dostupná globálně v kontejneru R = x (N + 1). Další jednotky ru R budou automaticky přiřazeni k procesu aktualizace je v konfliktu a proti entropie provoz napříč regiony.

Podle vaší volby [model pro zajištění konzistence](consistency-levels.md) ovlivní také propustnosti. Pro relace, konzistentní Předpona a konečné konzistence ve srovnání s omezená neaktuálnost nebo silné konzistence můžete získat přibližně 2 × propustnost čtení.

## <a name="next-steps"></a>Další postup

Dále můžete zjistěte, jak nakonfigurovat propustnost díky pomoci v následujícím článku:

* [Získání a nastavení propustnosti pro kontejnery a databáze](set-throughput.md) 


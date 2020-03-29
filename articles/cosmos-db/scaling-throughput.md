---
title: Škálování propustnost v Db Azure Cosmos
description: Tento článek popisuje, jak Azure Cosmos DB škáluje propustnost v různých oblastech, kde je zřízen účet Azure Cosmos.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 440f23afcd08326261be30432ad1f0ecb16f55fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873501"
---
# <a name="globally-scale-provisioned-throughput"></a>Globální škálování zřízené propustnosti 

V Azure Cosmos DB zřízená propustnost je reprezentována jako jednotky požadavku za sekundu (RU/s nebo plurální formulář RU). Ru měří náklady na operace čtení i zápisu proti kontejneru Cosmos, jak je znázorněno na následujícím obrázku:

![Jednotky žádostí](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

Ru můžete zřídit na kontejner Cosmos nebo databáze Cosmos. Ru zřízené na kontejneru jsou k dispozici výhradně pro operace prováděné na tomto kontejneru. Ru zřízené v databázi jsou sdíleny mezi všechny kontejnery v rámci této databáze (s výjimkou všechny kontejnery s výhradně přiřazené ru).

Pro elasticky škálování zřízená propustnost, můžete zvýšit nebo snížit zřízená RU/s kdykoli. Další informace najdete [v tématu Jak zřídit propustnost](set-throughput.md) a elasticky škálovat Cosmos kontejnery a databáze. Pro globální škálování propustnost, můžete přidat nebo odebrat oblasti z vašeho účtu Cosmos kdykoliv. Další informace naleznete v tématu [Přidání nebo odebrání oblastí z databázového účtu](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Soupození více oblastí s účtem Cosmos je důležité v mnoha scénářích – pro dosažení nízké latence a [vysoké dostupnosti](high-availability.md) po celém světě.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>Jak je zřízená propustnost distribuována mezi oblastmi

Pokud zřídíte *ru "R"* na kontejnercosmos (nebo databáze), Cosmos DB zajišťuje, že *'R'* RU jsou k dispozici v *každé* oblasti přidružené k účtu Cosmos. Pokaždé, když přidáte novou oblast do svého účtu, Cosmos DB automaticky zřídí *'R'* RU v nově přidané oblasti. Operace prováděné proti kontejneru Cosmos je zaručeno, že získat *'R'* RU v každé oblasti. Ru nelze selektivně přiřadit ru k určité oblasti. Ru zřízené na kontejnercosmos (nebo databáze) jsou zřízeny ve všech oblastech přidružených k účtu Cosmos.

Za předpokladu, že kontejner Cosmos je nakonfigurován s *'R'* RU a existují *'N'* oblasti přidružené k účtu Cosmos, pak:

- Pokud je účet Cosmos nakonfigurován s jednou oblastí zápisu, celkové ru, které jsou globálně k dispozici v kontejneru = *R* x *N*.

- Pokud je účet Cosmos nakonfigurován s více oblastmi zápisu, celkový ru, které jsou globálně k dispozici v kontejneru = *R* x (*N*+1). Další *ru ru* jsou automaticky zřízeny pro zpracování konfliktů aktualizací a přenosy antientropie napříč oblastmi.

Vaše volba [modelu konzistence](consistency-levels.md) také ovlivňuje propustnost. Můžete získat přibližně 2x propustnost pro čtení pro uvolněnější úrovně konzistence (např. *relace*, *konzistentní předpona* a *konečná* konzistence) ve srovnání se silnějšími úrovněmi konzistence (např. *ohraničená neaktuálnost* nebo *silná* konzistence).

## <a name="next-steps"></a>Další kroky

Dále se můžete dozvědět, jak nakonfigurovat propustnost v kontejneru nebo databázi:

* [Získání a nastavení propustnosti pro kontejnery a databáze](set-throughput.md) 


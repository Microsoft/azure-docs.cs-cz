---
title: Škálování propustnosti ve službě Azure Cosmos DB
description: Tento článek popisuje, jak službu Azure Cosmos DB Elasticky škáluje propustnost
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: f930b5c478cc880952b4559be4c6647b260efcf2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66243493"
---
# <a name="globally-scale-provisioned-throughput"></a>Globální škálování zřízené propustnosti 

Ve službě Azure Cosmos DB, zřízená propustnost je vyjádřena jako požadavek jednotek za sekundu (RU/s nebo množný tvar ru). Měření náklady na čtení a zápisu operace kontejneru Cosmos, jak je znázorněno na následujícím obrázku:

![Jednotky žádostí](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

Můžete zřídit RU v kontejneru Cosmos nebo v databázi Cosmos. RU zřízených v kontejneru je dostupná výhradně pro operace prováděné v tomto kontejneru. RU zřízených pro databázi jsou sdílena mezi všechny kontejnery v rámci této databáze (s výjimkou všechny kontejnery s exkluzivně přiřazená ru).

Elasticky škálovat propustnost, můžete zvýšit nebo snížit RU/s zřízených v každém okamžiku. Další informace najdete v tématu [postupy: poskytování propustnost](set-throughput.md) a Elasticky škálovat Cosmos kontejnerů a databáze. Globální škálování propustnosti, můžete přidat nebo odebrat oblasti ze svého účtu Cosmos kdykoli. Další informace najdete v tématu [přidat nebo odebrat oblasti ze svého účtu databáze](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Přidružení účtu Cosmos více oblastí je důležité v mnoha scénářích – pro zajištění nízké latence a [vysoké dostupnosti](high-availability.md) po celém světě.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>jak zřízená propustnost je distribuovaná napříč oblastmi

Pokud zřizujete *"R"* ru na Cosmos kontejneru (nebo databáze), služby Cosmos DB zajišťuje, že *"R"* ru jsou k dispozici v *každý* oblasti přidružené k účtu Cosmos. Pokaždé, když přidáte novou oblast ke svému účtu služby Cosmos DB automaticky zřídí *"R"* RUs v nově přidaném oblasti. Je zaručeno, že operací provedených v kontejneru Cosmos získat *"R"* RUs v jednotlivých oblastech. Nelze přiřadit selektivně ru určité oblasti. Ve všech oblastech, které jsou spojené s vaším účtem Cosmos jsou zřízené ru zřízených v databázi Cosmos kontejneru (nebo).

Za předpokladu, že se v kontejneru Cosmos nakonfigurují *"R"* ru a existují *n* oblasti přidružené k účtu Cosmos, pak:

- Pokud je nakonfigurovaný účet Cosmos s oblastí jeden zápis, celkový počet ru dostupná globálně v kontejneru = *R* x *N*.

- Pokud je nakonfigurovaný účet Cosmos s využitím více oblastí zápisu, celkový počet ru dostupná globálně v kontejneru = *R* x (*N*+ 1). Další *R* ru budou automaticky přiřazeni k procesu aktualizace je v konfliktu a proti entropie provoz napříč regiony.

Podle vaší volby [model pro zajištění konzistence](consistency-levels.md) má vliv také propustnost. Pro více volný úrovně konzistence můžete získat přibližně 2 × propustnost čtení (například *relace*, *konzistentní předpona* a *konečné* konzistence) v porovnání s silnější úrovně konzistence (například *omezená neaktuálnost* nebo *silné* konzistence).

## <a name="next-steps"></a>Další postup

Dále můžete zjistěte, jak nakonfigurovat propustnosti na kontejner nebo v databázi:

* [Získání a nastavení propustnosti pro kontejnery a databáze](set-throughput.md) 


---
title: Škálování propustnosti v Azure Cosmos DB
description: Tento článek popisuje, jak Azure Cosmos DB škáluje propustnost napříč různými oblastmi, ve kterých je účet Azure Cosmos zřízený.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 94b5b3d2ab1f576f87ead23b389252ec96a20f11
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397347"
---
# <a name="how-does-azure-cosmos-db-globally-scale-the-provisioned-throughput"></a>Jak Azure Cosmos DB globálně škálovat zřízenou propustnost?

V Azure Cosmos DB je zajištěná propustnost reprezentovaná jako jednotka žádosti za sekundu (RU/s nebo ru ve formě plural). Ru změřte náklady na operace čtení i zápisu na kontejneru Cosmos, jak je znázorněno na následujícím obrázku:

:::image type="content" source="./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png" alt-text="Jednotky žádosti" border="false":::

Ru můžete zřídit v kontejneru Cosmos nebo v databázi Cosmos. Ru zřízené na kontejneru jsou k dispozici výhradně pro operace prováděné na daném kontejneru. Ru zřízené v databázi se sdílí mezi všemi kontejnery v této databázi (s výjimkou všech kontejnerů se exkluzivně přiřazeným ru).

Pro elastické škálování zajištěné propustnosti můžete kdykoli zvýšit nebo snížit výše zřízené RU/s. Další informace najdete v tématech [jak zřídit propustnost](set-throughput.md) a elasticky škálovat Cosmos kontejnery a databáze. V případě propustnosti globálně škály můžete kdykoli přidat nebo odebrat oblasti z účtu Cosmos. Další informace najdete v tématu [Přidání nebo odebrání oblastí z databázového účtu](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Přidružení více oblastí k účtu Cosmos je důležité v mnoha scénářích – pro zajištění nízké latence a [vysoké dostupnosti](high-availability.md) po celém světě.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>Jak se distribuuje zajištěná propustnost napříč oblastmi

Pokud zřizujete *' r '* ru na kontejneru Cosmos (nebo databázi), Cosmos DB zajistí, že je v *každé* oblasti přidružené k vašemu účtu Cosmos k dispozici ru *r* . Pokaždé, když do svého účtu přidáte novou oblast, Cosmos DB v nově přidané oblasti automaticky zřídit *R* ru. Operace prováděné na vašem kontejneru Cosmos jsou zaručené získat *"R"* ru v každé oblasti. Ru nelze selektivně přiřadit ke konkrétní oblasti. Ru zřízené na kontejneru Cosmos (nebo databázi) se zřídí ve všech oblastech přidružených k vašemu účtu Cosmos.

Za předpokladu, že je kontejner Cosmos nakonfigurovaný pomocí *R* ru a k účtu Cosmos jsou přidružené oblasti *N* , pak:

- Pokud je účet Cosmos nakonfigurovaný s jednou oblastí zápisu, celkový ru dostupný globálně na kontejneru = *R* x *N*.

- Pokud je účet Cosmos nakonfigurovaný s více oblastmi zápisu, celkový ru dostupný globálně na kontejneru = *R* x (*N*+ 1). Další *R* ru se automaticky zřídí pro zpracování konfliktů aktualizací a provozu ochrany proti entropii napříč oblastmi.

Vaše volba [modelu konzistence](consistency-levels.md) má vliv také na propustnost. V porovnání s silnějšími úrovněmi konzistence můžete získat přibližně dvojnásobnou propustnost čtení pro příznivější úrovně konzistence *(například* *relace*, *konzistentní předpony* *a* konečná *konzistence* ).

## <a name="next-steps"></a>Další kroky

V dalším kroku se dozvíte, jak nakonfigurovat propustnost na kontejneru nebo databázi:

* [Získání a nastavení propustnosti pro kontejnery a databáze](set-throughput.md) 


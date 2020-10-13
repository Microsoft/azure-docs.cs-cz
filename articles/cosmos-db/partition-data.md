---
title: Dělení a horizontální škálování ve službě Azure Cosmos DB
description: Přečtěte si o tom, jak dělení funguje v Azure Cosmos DB, jak nakonfigurovat dělení a klíče oddílů a jak zvolit správný klíč oddílu pro vaši aplikaci.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 98cd28e8b770ebfb7ab395fbe7fff16a078e3529
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826852"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Dělení a horizontální škálování ve službě Azure Cosmos DB

Tento článek vysvětluje vztah mezi logickými a fyzickými oddíly. Popisuje také osvědčené postupy pro dělení a poskytuje podrobné zobrazení, jak horizontální škálování funguje v Azure Cosmos DB. Není nutné porozumět těmto interním podrobnostem, abyste mohli [Vybrat klíč oddílu](partitioning-overview.md#choose-partitionkey) , ale my jsme je pokryli, abyste měli jasnou představu o tom, jak Azure Cosmos DB funguje.

## <a name="logical-partitions"></a>Logické oddíly

Logický oddíl obsahuje sadu položek, které mají stejný klíč oddílu. Například v kontejneru, který obsahuje data o výživě potravin, všechny položky obsahují `foodGroup` vlastnost. Můžete použít `foodGroup` jako klíč oddílu pro kontejner. Skupiny položek, které mají specifické hodnoty pro `foodGroup` , například, `Beef Products` `Baked Products` a `Sausages and Luncheon Meats` , tvoří rozdílné logické oddíly. Nemusíte si dělat starosti s odstraněním logického oddílu, pokud jsou podkladová data odstraněna.

Logický oddíl také definuje rozsah databázových transakcí. Položky v rámci logického oddílu lze aktualizovat pomocí [transakce s izolací snímku](database-transactions-optimistic-concurrency.md). Při přidání nových položek do kontejneru jsou nové logické oddíly transparentně vytvořeny systémem.

Počet logických oddílů ve vašem kontejneru není nijak omezený. Každý logický oddíl může ukládat až 20 GB dat. Dobrá volba klíče oddílu má široké spektrum možných hodnot. Například v kontejneru, kde všechny položky obsahují `foodGroup` vlastnost, může data v rámci `Beef Products` logického oddílu růst až 20 GB. [Výběr klíče oddílu](partitioning-overview.md#choose-partitionkey) s široké škálou možných hodnot zajistí, že kontejner bude schopný škálovat.

## <a name="physical-partitions"></a>Fyzické oddíly

Kontejner Azure Cosmos se škáluje distribucí dat a propustnosti mezi fyzickými oddíly. Interně je jeden nebo více logických oddílů namapovaných na jeden fyzický oddíl. Většina malých kontejnerů Cosmos má mnoho logických oddílů, ale vyžaduje jenom jeden fyzický oddíl. Na rozdíl od logických oddílů jsou fyzické oddíly interní implementací systému a jsou výhradně spravovány Azure Cosmos DB.

Počet fyzických oddílů v kontejneru Cosmos závisí na následujících:

- Množství zřízené propustnosti (každý jednotlivý fyzický oddíl může poskytovat propustnost až 10 000 jednotek žádostí za sekundu)
- Celkové úložiště dat (každý jednotlivý fyzický oddíl může ukládat až 50 GB)

Celkový počet fyzických oddílů ve vašem kontejneru není nijak omezený. Jak vaše zřízená propustnost nebo velikost dat roste, Azure Cosmos DB automaticky vytvoří nové fyzické oddíly tím, že se rozdělí stávající. Rozdělení fyzického oddílu nemá vliv na dostupnost vaší aplikace. Po rozdělení fyzického oddílu budou všechna data v jednom logickém oddílu stále uložena ve stejném fyzickém oddílu. Rozdělení fyzického oddílu jednoduše vytvoří nové mapování logických oddílů na fyzické oddíly.

Propustnost zřízená pro kontejner je rozdělená rovnoměrně mezi fyzické oddíly. Návrh klíče oddílu, který nedistribuuje požadavky rovnoměrně, může mít za následek příliš mnoho požadavků směrovaných na malou podmnožinu oddílů, které se stanou "horkou". Hot partitions vede k neefektivnímu využití zřízené propustnosti, což může vést k omezení rychlosti a vyššímu počtu nákladů.

Fyzické oddíly kontejneru můžete zobrazit v části **úložiště** v okně **metriky** Azure Portal:

:::image type="content" source="./media/partition-data/view-partitions-zoomed-out.png" alt-text="Zobrazení počtu fyzických oddílů" lightbox="./media/partition-data/view-partitions-zoomed-in.png" ::: 

V tomto příkladu kontejneru, který jsme vybrali `/foodGroup` jako náš klíč oddílu, každý ze tří obdélníků představuje fyzický oddíl. V imagi je **Rozsah klíčů oddílu** stejný jako fyzický oddíl. Vybraný fyzický oddíl obsahuje tři logické oddíly: `Beef Products` , a `Vegetable and Vegetable Products` `Soups, Sauces, and Gravies` .

Pokud zřizujeme propustnost 18 000 jednotek žádostí za sekundu (RU/s), pak každý ze tří fyzických oddílů může využít 1/3 celkové zřízené propustnosti. V rámci vybraného fyzického oddílu klíče logických oddílů `Beef Products` , `Vegetable and Vegetable Products` a `Soups, Sauces, and Gravies` mohou společně shromažďovat, využijte ru/s fyzického oddílu 6 000. Vzhledem k tomu, že zajištěná propustnost je rovnoměrně rozdělená napříč fyzickými oddíly kontejneru, je důležité zvolit klíč oddílu, který rovnoměrně distribuuje spotřebu propustnosti, a to [výběrem správného logického klíče oddílu](partitioning-overview.md#choose-partitionkey). Pokud zvolíte klíč oddílu, který rovnoměrně distribuuje spotřebu propustnosti napříč logickými oddíly, zajistíte rovnováhu propustnosti mezi fyzickými oddíly.

## <a name="replica-sets"></a>Sady replik

Každý fyzický oddíl se skládá ze sady replik, které se také označují jako [*sada replik*](global-dist-under-the-hood.md). Každá sada replik je hostitelem instance databázového stroje Azure Cosmos. Sada replik vytvoří data uložená v rámci fyzického oddílu odolného, vysoce dostupného a konzistentního. Každá replika, která tvoří fyzický oddíl, zdědí kvótu úložiště oddílu. Všechny repliky fyzického oddílu společně podporují propustnost, která je přidělena fyzickému oddílu. Azure Cosmos DB automaticky spravuje sady replik.

Většina malých Cosmos kontejnerů vyžaduje jenom jeden fyzický oddíl, ale pořád má aspoň 4 repliky.

Následující obrázek ukazuje, jak jsou logické oddíly namapovány na fyzické oddíly distribuované globálně:

:::image type="content" source="./media/partition-data/logical-partitions.png" alt-text="Zobrazení počtu fyzických oddílů" border="false":::

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [Vybrat klíč oddílu](partitioning-overview.md#choose-partitionkey).
* Přečtěte si o [zřízené propustnosti v Azure Cosmos DB](request-units.md).
* Přečtěte si o [globální distribuci v Azure Cosmos DB](distribute-data-globally.md).
* Naučte se [zřídit propustnost v kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
* Naučte se [zřídit propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).

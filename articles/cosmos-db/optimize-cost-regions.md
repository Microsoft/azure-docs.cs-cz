---
title: Optimalizujte náklady na nasazení ve více oblastech v Azure Cosmos DB
description: Tento článek vysvětluje, jak spravovat náklady na nasazení ve více oblastech v Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2020
ms.openlocfilehash: a559a51feafa310a4645282dc6368f520fc6b972
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96459614"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Optimalizace nákladů na více oblastí ve službě Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Můžete kdykoli přidat nebo odebrat oblasti pro účet Azure Cosmos. Propustnost, kterou nakonfigurujete pro různé databáze a kontejnery Azure Cosmos, je vyhrazena v každé oblasti přidružené k vašemu účtu. Pokud je propustnost zřízená za hodinu, která je součtem RU/s konfigurovaných napříč všemi databázemi a kontejnery pro váš účet Azure Cosmos, `T` a počtem oblastí Azure přidružených k vašemu databázovému účtu `N` , pak je celková zřízená propustnost pro váš účet Cosmos pro danou hodinu rovna `T x N RU/s` .

Náklady na zřízenou propustnost s jednou oblastí zápisu jsou 0,008 USD za hodinu a 100 RU/s a náklady na zřízenou propustnost s více oblastmi zápisu jsou 0,016 USD za hodinu a 100 RU/s. Další informace najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/cosmos-db/)Azure Cosmos DB.

## <a name="costs-for-multiple-write-regions"></a>Náklady na více oblastí zápisu

V systému zápisy ve více oblastech zvyšuje netto dostupná ru pro operace zápisu časy, `N` ve kterých `N` je počet oblastí pro zápis. Na rozdíl od zápisu jedné oblasti je teď každá oblast zapisovatelná a podporuje řešení konfliktů. V bodě plánování nákladů na pohled, že k provádění `M` ru/s stačí po celém světě, budete muset zřídit M `RUs` na úrovni kontejneru nebo databáze. Pak můžete přidat tolik oblastí, jak byste chtěli, a použít je pro zápisy k provedení `M` ru po celém světě zápisů.

### <a name="example"></a>Příklad

Vezměte v úvahu, že máte kontejner v Západní USA nakonfigurovaný pro zápisy v jedné oblasti, zřízený s propustností 10 000 RU/s a za tento měsíc ukládá 0,5 TB dat. Předpokládejme, že přidáte oblast, Východní USA se stejným úložištěm a propustností a chcete mít možnost zapisovat do kontejnerů v obou oblastech z vaší aplikace. Vaše nové celkové měsíční vyúčtování (za předpokladu 730 hodin za měsíc) bude následující:

|**Položka**|**Využití (měsíčně)**|**Rychlost**|**Měsíční náklady**|
|----|----|----|----|
|Faktura za propustnost pro kontejner v Západní USA (oblast pro jedno zápis) |10 000 RU/s × 730 hodin |$0,008 za 100 RU/s za hodinu |$584 |
|Faktura za propustnost pro kontejner v 2 oblastech – Západní USA & Východní USA (více oblastí zápisu) |2 * 10 000 RU/s × 730 hodin |$0,016 za 100 RU/s za hodinu |$2 336 |
|Faktura za úložiště pro kontejner v Západní USA |0,5 TB (nebo 512 GB) |0,25/GB |$128 |
|Faktura za úložiště pro kontejner v 2 oblastech – Západní USA & Východní USA |2 × 0,5 TB (nebo 1 024 GB) |0,25/GB |$256 |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Zvýšení využití propustnosti na základě jednotlivých oblastí

Pokud máte neefektivní využití, například jednu nebo více méně používaných oblastí čtení, můžete podniknout kroky k zajištění maximálního využití ru v oblastech čtení pomocí změny kanálu v oblasti pro čtení nebo jejich přesunutí na jiný sekundární, pokud je přetížený. Nejdřív budete muset v oblasti pro zápis zajistit, aby byla zajištěná zajištěná propustnost (ru). Zapisuje náklady více než čtení, pokud velmi velké dotazy, aby nemohly zachovávat i využití, mohou být náročné. Celkově Sledujte spotřebované propustnosti ve vašich oblastech a přidejte nebo odeberte oblasti na vyžádání ke škálování propustnosti čtení a zápisu. tím je zajištěno lepší dopad na latenci pro všechny aplikace, které jsou nasazené ve stejné oblasti.

## <a name="next-steps"></a>Další kroky

Další informace o optimalizaci nákladů v Azure Cosmos DB najdete v následujících článcích:

* Další informace o [optimalizaci pro vývoj a testování](optimize-dev-test.md)
* Další informace o [Azure Cosmos DB vyúčtování](understand-your-bill.md)
* Další informace o [optimalizaci nákladů na propustnost](optimize-cost-throughput.md)
* Další informace o [optimalizaci nákladů na úložiště](optimize-cost-storage.md)
* Další informace o [optimalizaci nákladů na čtení a zápisy](optimize-cost-reads-writes.md)
* Další informace o [optimalizaci nákladů na dotazy](./optimize-cost-reads-writes.md)

---
title: Optimalizujte náklady na nasazení ve více oblastech v Azure Cosmos DB
description: Tento článek vysvětluje, jak spravovat náklady na nasazení ve více oblastech v Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: rimman
ms.openlocfilehash: 233eab1fc49d7ce4cbb1e5b98b67eda9a64aa195
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667599"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Optimalizace nákladů na více oblastí v Azure Cosmos DB

Můžete kdykoli přidat nebo odebrat oblasti pro účet Azure Cosmos. Propustnost, kterou nakonfigurujete pro různé databáze a kontejnery Azure Cosmos, je vyhrazena v každé oblasti přidružené k vašemu účtu. Pokud je propustnost zřízená za hodinu, která je součtem ru/s konfigurovaných napříč všemi databázemi a kontejnery pro váš účet `T` Azure Cosmos, a počtem oblastí Azure přidružených k vašemu databázovému účtu je `N`, pak celková hodnota zřízená propustnost pro váš účet Cosmos je po určitou hodinu rovna:

1. `T x N RU/s`Pokud je váš účet Azure Cosmos nakonfigurovaný s jednou oblastí zápisu. 

1. `T x (N+1) RU/s`Pokud je váš účet Azure Cosmos nakonfigurovaný se všemi oblastmi, které jsou schopné zpracovávat zápisy. 

Zřízená propustnost s jednou oblastí zápisu za cenu $0.008/hod za 100 RU/s a zřízenou propustností s více náklady na zapisovatelné oblasti $0,016/za hodinu za 100 RU/s. Další informace najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/cosmos-db/)Azure Cosmos DB.

## <a name="costs-for-multiple-write-regions"></a>Náklady na více oblastí zápisu

V systému s více hlavními systémy zvyšuje `N` netto dostupný ru pro operace zápisu časy, kde `N` je počet oblastí pro zápis. Na rozdíl od zápisu jedné oblasti je teď každá oblast zapisovatelná a měla by podporovat řešení konfliktů. Objem úlohy pro zapisovače se zvýšil. V bodě plánování nákladů na pohled, že k provádění `M` ru/s stačí po celém světě, budete muset zřídit M `RUs` na úrovni kontejneru nebo databáze. Pak můžete přidat tolik oblastí, jak byste chtěli, a použít je pro zápisy k provedení `M` ru po celém světě zápisů. 

### <a name="example"></a>Příklad

Vezměte v úvahu, že máte kontejner ve Západní USA zřízený s propustností 10 000 RU/s a ukládá 1 TB dat v tomto měsíci. Předpokládejme, že přidáte tři oblasti – Východní USA, Severní Evropa a Východní Asie, každé se stejným úložištěm a propustností a chcete mít možnost zapisovat do kontejnerů ve všech čtyřech oblastech ze své globálně distribuované aplikace. Vaše celkové měsíční faktury (za předpokladu 31 dnů) v měsíci jsou následující:

|**Položka**|**Využití (měsíčně)**|**Kmitočt**|**Měsíční náklady**|
|----|----|----|----|
|Faktura za propustnost pro kontejner v Západní USA (více oblastí zápisu) |10 000 RU/s * 24 × 31 |$0,016 za 100 RU/s za hodinu |$1 190,40 |
|Faktura za propustnost pro 3 další oblasti – Východní USA, Severní Evropa a Východní Asie (více oblastí pro zápis) |(3 + 1) * 10 000 RU/s * 24 * 31 |$0,016 za 100 RU/s za hodinu |$4 761,60 |
|Faktura za úložiště za kontejner v oblasti Západní USA |1 TB (nebo 1 024 GB) |0,25/GB |$256 |
|Faktura za úložiště za 3 další oblasti: Východní USA, Severní Evropa a Východní Asie |3 × 1 TB (nebo 3 072 GB) |0,25/GB |$768 |
|**Celkem**|||**$6 976** |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Zvýšení využití propustnosti na základě jednotlivých oblastí

Pokud máte neefektivní využití, například jednu nebo více méně používaných nebo přetížených oblastí, můžete provést následující kroky, abyste vylepšili využití propustnosti:  

1. Ujistěte se, že jste nejdřív provedli optimalizaci zřízené propustnosti (ru) v oblasti pro zápis, a pak v oblastech pro čtení, které používají změnu kanálu v oblasti pro čtení, využívali maximální využití ru. 

2. Čtení a zápisy v různých oblastech zápisu je možné škálovat napříč všemi oblastmi, které jsou přidružené k účtu Azure Cosmos. 

3. Sledujte aktivitu ve svých oblastech a můžete přidat a odebrat oblasti na vyžádání pro škálování propustnosti čtení a zápisu.

## <a name="next-steps"></a>Další kroky

Další informace o optimalizaci nákladů v Azure Cosmos DB najdete v následujících článcích:

* Další informace o [optimalizaci pro vývoj a testování](optimize-dev-test.md)
* Další informace o [Azure Cosmos DB vyúčtování](understand-your-bill.md)
* Další informace o [optimalizaci nákladů na propustnost](optimize-cost-throughput.md)
* Další informace o [optimalizaci nákladů na úložiště](optimize-cost-storage.md)
* Další informace o [optimalizaci nákladů na čtení a zápisy](optimize-cost-reads-writes.md)
* Další informace o [optimalizaci nákladů na dotazy](optimize-cost-queries.md)


---
title: Optimalizujte náklady na nasazení ve více oblastech v Azure Cosmos DB
description: Tento článek vysvětluje, jak spravovat náklady na nasazení ve více oblastech v Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: 8d98c9a7e58f08d9ad63183805cd6cd0d2ab3b3d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91570171"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Optimalizace nákladů na více oblastí ve službě Azure Cosmos DB

Můžete kdykoli přidat nebo odebrat oblasti pro účet Azure Cosmos. Propustnost, kterou nakonfigurujete pro různé databáze a kontejnery Azure Cosmos, je vyhrazena v každé oblasti přidružené k vašemu účtu. Pokud je propustnost zřízená za hodinu, která je součtem RU/s konfigurovaných napříč všemi databázemi a kontejnery pro váš účet Azure Cosmos, `T` a počtem oblastí Azure přidružených k vašemu databázovému účtu `N` , pak je celková zajištěná propustnost pro váš účet Cosmos, která je pro danou hodinu rovna:

1. `T x N RU/s` Pokud je váš účet Azure Cosmos nakonfigurovaný s jednou oblastí zápisu. 

1. `T x (N+1) RU/s` Pokud je váš účet Azure Cosmos nakonfigurovaný se všemi oblastmi, které jsou schopné zpracovávat zápisy. 

Náklady na zřízenou propustnost s jednou oblastí zápisu jsou 0,008 USD za hodinu a 100 RU/s a náklady na zřízenou propustnost s více oblastmi zápisu jsou 0,016 USD za hodinu a 100 RU/s. Další informace najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/cosmos-db/)Azure Cosmos DB.

## <a name="costs-for-multiple-write-regions"></a>Náklady na více oblastí zápisu

V systému zápisy ve více oblastech zvyšuje netto dostupná ru pro operace zápisu časy, `N` ve kterých `N` je počet oblastí pro zápis. Na rozdíl od zápisu jedné oblasti je teď každá oblast zapisovatelná a měla by podporovat řešení konfliktů. Objem úlohy pro zapisovače se zvýšil. V bodě plánování nákladů na pohled, že k provádění `M` ru/s stačí po celém světě, budete muset zřídit M `RUs` na úrovni kontejneru nebo databáze. Pak můžete přidat tolik oblastí, jak byste chtěli, a použít je pro zápisy k provedení `M` ru po celém světě zápisů. 

### <a name="example"></a>Příklad

Vezměte v úvahu, že máte kontejner ve Západní USA zřízený s propustností 10 000 RU/s a ukládá 1 TB dat v tomto měsíci. Předpokládejme, že přidáte tři oblasti – Východní USA, Severní Evropa a Východní Asie, každé se stejným úložištěm a propustností a chcete mít možnost zapisovat do kontejnerů ve všech čtyřech oblastech ze své globálně distribuované aplikace. Vaše celkové měsíční faktury (za předpokladu 31 dnů) v měsíci jsou následující:

|**Položka**|**Využití (měsíčně)**|**Rychlost**|**Měsíční náklady**|
|----|----|----|----|
|Faktura za propustnost pro kontejner v Západní USA (více oblastí zápisu) |10 000 RU/s * 24 × 31 |$0,016 za 100 RU/s za hodinu |$1 190,40 |
|Faktura za propustnost pro 3 další oblasti – Východní USA, Severní Evropa a Východní Asie (více oblastí pro zápis) |(3 + 1) * 10 000 RU/s * 24 * 31 |$0,016 za 100 RU/s za hodinu |$4 761,60 |
|Faktura za úložiště pro kontejner v Západní USA |1 TB (nebo 1 024 GB) |0,25/GB |$256 |
|Faktura za úložiště pro 3 další oblasti – Východní USA, Severní Evropa a Východní Asie |3 × 1 TB (nebo 3 072 GB) |0,25/GB |$768 |
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


---
title: Optimalizace nákladů pro nasazení ve více oblastech v Azure Cosmos DB
description: Tento článek vysvětluje, jak spravovat náklady na nasazení s více oblastmi v Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: e0a24b52c12bce6a8e016a926dfa64a1e36a7cc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72753321"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Optimalizace nákladů na více oblastí ve službě Azure Cosmos DB

Oblasti můžete přidat a odebrat do svého účtu Azure Cosmos kdykoli. Propustnost, kterou nakonfigurujete pro různé databáze a kontejnery Azure Cosmos, je vyhrazena v každé oblasti přidružené k vašemu účtu. Pokud je `T` propustnost zřízená za hodinu, tedy součet RU/s nakonfigurovaných ve všech databázích a kontejnerech `N`pro váš účet Azure Cosmos a počet oblastí Azure přidružených k vašemu databázovému účtu je , pak se celková zřízená propustnost pro váš účet Cosmos pro danou hodinu rovná:

1. `T x N RU/s`pokud je váš účet Azure Cosmos nakonfigurovaný s jednou oblastí zápisu. 

1. `T x (N+1) RU/s`pokud je váš účet Azure Cosmos nakonfigurovaný se všemi oblastmi schopnými zpracovávat zápisy. 

Náklady na zřízenou propustnost s jednou oblastí zápisu jsou 0,008 USD za hodinu a 100 RU/s a náklady na zřízenou propustnost s více oblastmi zápisu jsou 0,016 USD za hodinu a 100 RU/s. Další informace najdete na [stránce Ceny](https://azure.microsoft.com/pricing/details/cosmos-db/)Azure Cosmos DB .

## <a name="costs-for-multiple-write-regions"></a>Náklady na více oblastí zápisu

V systému s více hlavními servery nesíťové jednotky pro zápis prodlužují `N` dobu, kdy `N` je počet oblastí zápisu. Na rozdíl od zápisů v jedné oblasti je nyní každá oblast zapisovatelná a měla by podporovat řešení konfliktů. Množství zatížení pro autory se zvýšil. Z hlediska plánování nákladů, chcete-li provádět `M` ru/s v hodnotě `RUs` zápisů po celém světě, budete muset zřídit M na úrovni kontejneru nebo databáze. Potom můžete přidat tolik oblastí, kolik chcete, a `M` použít je pro zápisy k provedení RU v hodnotě celosvětových zápisů. 

### <a name="example"></a>Příklad

Vezměte v úvahu, že máte kontejner v západní USA zřízené s propustností 10 kB RU/s a ukládá 1 TB dat tento měsíc. Předpokládejme, že přidáte tři oblasti – východní USA, severní Evropu a východní Asii, z nichž každá má stejné úložiště a propustnost, a chcete mít možnost zapisovat do kontejnerů ve všech čtyřech oblastech z globálně distribuované aplikace. Celkový měsíční účet (za předpokladu, že 31 dní) za měsíc je následující:

|**Položka**|**Použití (měsíčně)**|**Sazba**|**Měsíční náklady**|
|----|----|----|----|
|Účet za propustnost pro kontejner v západní USA (více oblastí zápisu) |10K RU/s * 24 * 31 |$0.016 za 100 RU/s za hodinu |1 190,40 USD |
|Účet za propustnost pro další 3 oblasti – východní USA, severní Evropa a východní Asie (více oblastí zápisu) |(3 + 1) * 10K RU/s * 24 * 31 |$0.016 za 100 RU/s za hodinu |4 761,60 USD |
|Účet za skladování kontejneru v západní USA |1 TB (nebo 1 024 GB) |$0.25/GB |256 000 Kč |
|Účet za úložiště pro další 3 oblasti – východní USA, severní Evropa a východní Asie |3 * 1 TB (nebo 3 072 GB) |$0.25/GB |768 dolarů |
|**Celkem**|||**6 976 USD** |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Zlepšete využití propustností pro-region-základ

Pokud máte neefektivní využití, například jednu nebo více oblastí s nedostatečným nebo nadměrně využívaným, můžete ke zlepšení využití propustnosti provést následující kroky:  

1. Ujistěte se, že optimalizovat zřízená propustnost (RU) v oblasti zápisu první a pak maximální využití ru v oblasti čtení pomocí kanálu změn z oblasti pro čtení atd. 

2. Více oblastí zápisu čtení a zápisy lze škálovat ve všech oblastech přidružených k účtu Azure Cosmos. 

3. Sledujte aktivitu ve vašich oblastech a můžete přidávat a odebírat oblasti na vyžádání, abyste mohli škálovat propustnost pro čtení a zápis.

## <a name="next-steps"></a>Další kroky

Další informace o optimalizaci nákladů v Azure Cosmos DB najdete v následujících článcích:

* Další informace o [optimalizaci pro vývoj a testování](optimize-dev-test.md)
* Další informace o [vysvětlení vaší faktury z DB Služby Azure Cosmos](understand-your-bill.md)
* Další informace o [optimalizaci nákladů na propustnost](optimize-cost-throughput.md)
* Další informace o [optimalizaci nákladů na úložiště](optimize-cost-storage.md)
* Další informace o [optimalizaci nákladů na čtení a zápisy](optimize-cost-reads-writes.md)
* Další informace o [optimalizaci nákladů na dotazy](optimize-cost-queries.md)


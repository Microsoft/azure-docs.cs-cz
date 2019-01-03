---
title: Optimalizujte náklady pro nasazení ve více oblastech ve službě Azure Cosmos DB
description: Tento článek vysvětluje, jak spravovat náklady na nasazení ve více oblastech ve službě Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: 0ec603df7005b9151f1cf760df80de9aaffc78b3
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53539700"
---
# <a name="optimize-the-cost-for-multi-region-deployments-in-azure-cosmos-db"></a>Optimalizujte náklady pro nasazení ve více oblastech ve službě Azure Cosmos DB

Můžete přidávat a odebírat oblastí k vašemu účtu Azure Cosmos v každém okamžiku. V každé oblasti spojené s vaším účtem je vyhrazená propustnost, které nakonfigurujete pro různé databáze Azure Cosmos a kontejnery. Pokud zřízená propustnost za hodinu, který je součtem RU/s, které jsou nakonfigurované ve všech databázích a kontejnerů pro váš účet Azure Cosmos je `T` a počtu oblastí Azure spojené s vaším účtem databáze je `N`, pak součet zřízená propustnost účtu Cosmos pro příslušné hodiny je rovna hodnotě:

1. ` T x N RU/s` Pokud váš účet Azure Cosmos je nakonfigurovaný s oblastí zápisu jednoho. 

1. `T x (N+1) RU/s` Pokud váš účet Azure Cosmos je nakonfigurována pomocí všech oblastech dokáže zpracovávat zápisy. 

Zřízená propustnost s oblastí zápisu jednoho náklady 0.008 $za hodinu za 100 RU/s a zřízená propustnost s více oblastmi zapisovatelné stojí 0,016 $/ za hodinu za 100 RU/s. Další informace najdete v tématu služby Azure Cosmos DB [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="costs-for-multiple-write-regions"></a>Náklady pro více oblastí zápisu

V systému s několika hlavními databázemi, net ru k dispozici pro zápis operace zvýšení `N` vyprší where `N` je počtem oblastí zápisu. Na rozdíl od jedné oblasti zápisu každé oblasti se teď může zapisovat a by měly podporovat řešení konfliktů. Zvýšil se objem úlohy pro zápis. Z pohledu, provádět plánování nákladů` M` RU/s za zápisy po celém světě, budete muset zřídit M `RUs` na úrovni kontejneru nebo databáze. Poté můžete přidat jakýkoli počet oblastí, jak chcete vytvořit a použít je pro zápis k provádění `M` RU za zápisy po celém světě. 

### <a name="example"></a>Příklad:

Vezměte v úvahu, že máte kontejner v oblasti západní USA zřízený s propustností 10 tisíc RU/s a 1 TB dat ukládá tento měsíc. Předpokládejme, že přidáte tři oblasti: východní USA, Severní Evropa a východní Asie, každá má stejné úložiště a propustnost a chcete mít možnost zapisovat do kontejnerů ve všech oblastech, čtyři z globálně distribuovaných aplikací. Celkový měsíční vyúčtování (za předpokladu, že nabídka 31 dní) za měsíc je následujícím způsobem:

|**Položka**|**Využití (měsíčně)**|**Frekvence**|**Měsíční náklady**|
|----|----|----|----|
|Faktura za propustnost za kontejner v oblasti západní USA (více oblastí zápisu) |10 tisíc RU/s * 24 * 31 |0,016 $ za 100 RU/s za hodinu |$1,190.40 |
|Faktura za propustnost za 3 Další oblasti: východní USA, Severní Evropa a východní Asie (více oblastí zápisu) |(3 + 1) * 10 tisíc RU/s * 24 * 31 |0,016 $ za 100 RU/s za hodinu |$4,761.60 |
|Faktura za úložiště za kontejner v oblasti Západní USA |100 GB |$ 0,25/GB |25 USD |
|Faktura za úložiště za 3 další oblasti: Východní USA, Severní Evropa a Východní Asie |3 * 1 TB |$ 0,25/GB |$75 |
|**Celkem**|||**$6,052** |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Zlepšit využití propustnosti jednotlivé oblasti

Pokud máte neefektivní využití, například jeden nebo více nevyužitých nebo over-pass-the jiným oblastí, provedete následující kroky pro zlepšení propustnosti využití:  

1. Ujistěte se, že nejprve optimalizovat zřízená propustnost (ru) v oblasti zápisu a pak proveďte maximální využití jednotky RU v oblastech čtení pomocí z další oblast čtení kanálu změn. 

2. Více oblasti čtení a zápisy je možné škálovat napříč všemi oblastmi přidružené k účtu Azure Cosmos. 

3. Monitorování aktivity ve vaší oblasti a můžete přidat a odebrat oblasti na vyžádání škálovat vaše pro čtení a zápis propustnost.

## <a name="next-steps"></a>Další postup

Potom můžete přejít k další informace o optimalizaci nákladů ve službě Azure Cosmos DB najdete v následujících článcích:

* Další informace o [optimalizace pro vývoj a testování](optimize-dev-test.md)
* Další informace o [vysvětlení vašeho vyúčtování Azure Cosmos DB](understand-your-bill.md)
* Další informace o [optimalizace propustnosti náklady](optimize-cost-throughput.md)
* Další informace o [optimalizovat náklady na úložiště](optimize-cost-storage.md)
* Další informace o [optimalizovat náklady na operace čtení a zápisy](optimize-cost-reads-writes.md)
* Další informace o [optimalizovat náklady na dotazy](optimize-cost-queries.md)


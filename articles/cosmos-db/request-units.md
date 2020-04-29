---
title: Jednotky a propustnost žádostí v Azure Cosmos DB
description: Přečtěte si, jak zadat a odhadnout požadavky na jednotky požadavků v Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: a0058bf309e0ff4fbe687731d676e907d1c3fd82
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79246588"
---
# <a name="request-units-in-azure-cosmos-db"></a>Jednotky žádosti v Azure Cosmos DB

V případě služby Azure Cosmos DB platíte po hodinách za zřízenou propustnost a spotřebované úložiště. Propustnost je potřeba zřídit, aby se zajistilo, že databáze Azure Cosmos bude mít vždy k dispozici dostatek systémových prostředků. K uspokojení nebo překročení [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)potřebujete dostatek prostředků.

Azure Cosmos DB podporuje mnoho rozhraní API, například SQL, MongoDB, Cassandra, Gremlin a Table. Každé rozhraní API má svou vlastní sadu databázových operací. Tyto operace jsou v rozsahu od jednoduchých čtení a zápisů do složitých dotazů. Každá databázová operace spotřebovává systémové prostředky na základě složitosti operace. 

Náklady na všechny databázové operace jsou normalizovány Azure Cosmos DB a jsou vyjádřeny *jednotkami žádostí* (nebo ru, pro krátké). Jednotky RU za sekundu si můžete představit jako měnu pro propustnost. Jednotky RU za sekundu představují měnu založenou na rychlosti. Abstrahuje systémové prostředky, jako jsou procesor, IOPS nebo paměť, potřebné k provedení databázových operací podporovaných službou Azure Cosmos DB. 

Náklady na čtení 1kB položky jsou 1 jednotka žádosti (neboli 1 RU). Pro uložení každého 1 GB dat se vyžaduje minimálně 10 RU/s. Podobně jsou náklady v RU přiřazené ke všem ostatním databázovým operacím. Bez ohledu na to, jaké rozhraní API používáte k interakci s kontejnerem Azure Cosmos, se náklady vždy měří v jednotkách RU. Ať už je databázovou operací zápis, čtení nebo dotazování, náklady se vždy měří v jednotkách RU.

Následující obrázek znázorňuje základní myšlenku jednotek RU:

![Databázové operace spotřebovávají jednotky žádosti](./media/request-units/request-units.png)

Aby bylo možné spravovat a plánovat kapacitu, Azure Cosmos DB zajišťuje, že je počet RU pro konkrétní databázovou operaci nad danou datovou sadou deterministický. Pokud chcete sledovat počet RU spotřebovaných jakoukoli databázovou operací, můžete prozkoumat hlavičku odpovědi. Když rozumíte [faktorům, které mají vliv na poplatky za ru](request-units.md#request-unit-considerations) a požadavky na propustnost vaší aplikace, můžete efektivně spouštět náklady na aplikace.

Počet RU pro aplikaci se zřizuje po sekundách v přírůstcích pro 100 RU za sekundu. Pokud chcete škálovat zřízenou propustnost pro vaši aplikaci, můžete počet RU kdykoli zvýšit nebo snížit. Škálovat můžete v přírůstcích po 100 RU. Změny můžete provést programově nebo pomocí webu Azure Portal. Účtování probíhá po hodinách.

Propustnost můžete zřídit ve dvou různých rozlišeních: 

* **Kontejnery**: Další informace najdete v tématu [zřízení propustnosti v kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
* **Databáze**: Další informace najdete v tématu [zřízení propustnosti databáze Azure Cosmos](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Aspekty jednotek žádosti

Při plánování počtu jednotek žádosti za sekundu, které budete zřizovat, vezměte v úvahu tyto faktory:

* **Velikost položky**: podle velikosti položky se zvyšuje počet ru spotřebovaných pro čtení nebo zápis položky.

* **Indexování položek**: ve výchozím nastavení je každá položka automaticky indexována. Pokud se rozhodnete některé z položek v kontejneru neindexovat, spotřebujete tak méně jednotek žádosti.

* **Počet vlastností položky**: za předpokladu, že výchozí indexování je u všech vlastností, počet ru spotřebovaných pro zápis položky se zvětšuje podle počtu vlastností položky.

* **Indexované vlastnosti**: zásady indexů na jednotlivých kontejnerech určují, které vlastnosti jsou ve výchozím nastavení indexované. Pokud tedy chcete snížit spotřebu jednotek žádosti pro operace zápisu, omezte počet indexovaných vlastností.

* **Konzistence dat**: silná a ohraničená úroveň konzistence odolná proti chybám spotřebovává během operací čtení ve srovnání s jinými uvolněnými úrovněmi konzistence přibližně dvojnásobek více ru.

* **Vzory dotazů**: složitost dotazu ovlivňuje, kolik ru se spotřebuje pro určitou operaci. Mezi faktory ovlivňující náklady na operace dotazování patří: 
    
    - Počet výsledků dotazu
    - Počet predikátů
    - Povaha predikátů
    - Počet funkcí definovaných uživatelem
    - Velikost zdrojových dat
    - Velikost sady výsledků
    - Projekce

  Služba Azure Cosmos DB garantuje, že při opakovaných spouštěních se náklady v podobě jednotek žádosti u stejného dotazu na stejná data nezmění.

* **Použití skriptu**: stejně jako u dotazů, uložené procedury a triggery spotřebovávají ru na základě složitosti operací, které jsou prováděny. Abyste získali lepší přehled o tom, kolik jednotek žádosti každá operace spotřebuje, zkontrolujte při vývoji aplikace [hlavičku poplatku za žádost](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query).

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o tom, jak [zřídit propustnost na Cosmosch kontejnerech a databázích Azure](set-throughput.md).
* Přečtěte si další informace o [logických oddílech](partition-data.md).
* Přečtěte si další informace o [globálním škálování zřízené propustnosti](scaling-throughput.md).
* Naučte se [zřídit propustnost v kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
* Naučte se [zřídit propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).
* Přečtěte si, jak [Najít poplatek za jednotku žádosti o operaci](find-request-unit-charge.md).
* Naučte se [optimalizovat zřízené náklady na propustnost v Azure Cosmos DB](optimize-cost-throughput.md).
* Naučte se [optimalizovat čtení a zápis nákladů v Azure Cosmos DB](optimize-cost-reads-writes.md).
* Naučte se [optimalizovat náklady na dotaz v Azure Cosmos DB](optimize-cost-queries.md).
* Naučte se [používat metriky k monitorování propustnosti](use-metrics.md).

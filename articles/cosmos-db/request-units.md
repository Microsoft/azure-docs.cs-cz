---
title: Jednotky a propustnost žádostí v Azure Cosmos DB
description: Přečtěte si, jak zadat a odhadnout požadavky na jednotky požadavků v Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/24/2020
ms.openlocfilehash: f1f203d17de9fb0fc9fe8bb0f6de80fe2b93ba8b
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87327799"
---
# <a name="request-units-in-azure-cosmos-db"></a>Jednotky žádostí ve službě Azure Cosmos DB

V případě služby Azure Cosmos DB platíte po hodinách za zřízenou propustnost a spotřebované úložiště. Propustnost je potřeba zřídit, aby se zajistilo, že databáze Azure Cosmos bude mít vždy k dispozici dostatek systémových prostředků. K uspokojení nebo překročení [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)potřebujete dostatek prostředků.

Azure Cosmos DB podporuje mnoho rozhraní API, například SQL, MongoDB, Cassandra, Gremlin a Table. Každé rozhraní API má svou vlastní sadu databázových operací. Tyto operace jsou v rozsahu od jednoduchých čtení a zápisů do složitých dotazů. Každá databázová operace spotřebovává systémové prostředky na základě složitosti operace.

Náklady na všechny databázové operace jsou normalizovány Azure Cosmos DB a jsou vyjádřeny *jednotkami žádostí* (nebo ru, pro krátké). Jednotky RU za sekundu si můžete představit jako měnu pro propustnost. Jednotky RU za sekundu představují měnu založenou na rychlosti. Abstrahuje systémové prostředky, jako jsou procesor, IOPS nebo paměť, potřebné k provedení databázových operací podporovaných službou Azure Cosmos DB. Pro uložení každého 1 GB dat se vyžaduje minimálně 10 RU/s.

Náklady na čtení bodu pro položku 1 KB jsou 1 jednotka požadavku (nebo 1 RU). Podobně jsou náklady v RU přiřazené ke všem ostatním databázovým operacím. Bez ohledu na to, jaké rozhraní API používáte k interakci s kontejnerem Azure Cosmos, se náklady vždy měří v jednotkách RU. Bez ohledu na to, jestli je databázová operace zápis, čtení z bodu nebo dotazování, se náklady vždycky měří v ru.

Následující obrázek znázorňuje základní myšlenku jednotek RU:

:::image type="content" source="./media/request-units/request-units.png" alt-text="Databázové operace spotřebovávají jednotky žádosti" border="false":::

Aby bylo možné spravovat a plánovat kapacitu, Azure Cosmos DB zajišťuje, že je počet RU pro konkrétní databázovou operaci nad danou datovou sadou deterministický. Pokud chcete sledovat počet RU spotřebovaných jakoukoli databázovou operací, můžete prozkoumat hlavičku odpovědi. Když rozumíte [faktorům, které mají vliv na poplatky za ru](request-units.md#request-unit-considerations) a požadavky na propustnost vaší aplikace, můžete efektivně spouštět náklady na aplikace.

Počet RU pro aplikaci se zřizuje po sekundách v přírůstcích pro 100 RU za sekundu. Pokud chcete škálovat zřízenou propustnost pro vaši aplikaci, můžete počet RU kdykoli zvýšit nebo snížit. Škálovat můžete v přírůstcích po 100 RU. Změny můžete naprogramovat nebo můžete použít Azure Portal. Účtování probíhá po hodinách.

Propustnost můžete zřídit ve dvou různých rozlišeních:

* **Kontejnery**: Další informace najdete v tématu [zřízení propustnosti v kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
* **Databáze**: Další informace najdete v tématu [zřízení propustnosti databáze Azure Cosmos](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Co ovlivňuje jednotky žádosti

Při plánování počtu jednotek žádosti za sekundu, které budete zřizovat, vezměte v úvahu tyto faktory:

* **Velikost položky**: Se vzrůstající velikostí položky se také zvyšuje počet jednotek žádosti spotřebovaných pro čtení a zápis.

* **Indexování položky**: Ve výchozím nastavení se automaticky indexuje každá položka. Pokud se rozhodnete některé z položek v kontejneru neindexovat, spotřebujete tak méně jednotek žádosti.

* **Počet vlastností položky**: Pokud u všech vlastností platí výchozí indexování, roste s počtem vlastností položky také počet jednotek žádosti spotřebovaných pro její zápis.

* **Indexované vlastnosti**: Vlastnosti, které se ve výchozím nastavení indexují, jsou určovány zásadami indexování každého kontejneru. Pokud tedy chcete snížit spotřebu jednotek žádosti pro operace zápisu, omezte počet indexovaných vlastností.

* **Konzistence dat**: silná a ohraničená úroveň konzistence odolná proti chybám spotřebovává během operací čtení ve srovnání s jinými uvolněnými úrovněmi konzistence přibližně dvojnásobek více ru.

* **Typ čtení**: bod čte náklady významně méně než dotazy.

* **Struktura dotazů**: Složitost dotazu má vliv na počet jednotek žádosti spotřebovaných operací. Mezi faktory ovlivňující náklady na operace dotazování patří: 
    
    - Počet výsledků dotazu
    - Počet predikátů
    - Povaha predikátů
    - Počet funkcí definovaných uživatelem
    - Velikost zdrojových dat
    - Velikost sady výsledků
    - Projekce

  Služba Azure Cosmos DB garantuje, že při opakovaných spouštěních se náklady v podobě jednotek žádosti u stejného dotazu na stejná data nezmění.

* **Použití skriptu**: stejně jako u dotazů, uložené procedury a triggery spotřebovávají ru na základě složitosti operací, které jsou prováděny. Abyste získali lepší přehled o tom, jakou kapacitu (vyjádřenou v jednotkách RU) každá operace spotřebuje, zkontrolujte při vývoji aplikace [hlavičku zátěže žádostmi](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query).

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

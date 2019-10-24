---
title: Jednotky a propustnost žádostí v Azure Cosmos DB
description: Přečtěte si, jak zadat a odhadnout požadavky na jednotky požadavků v Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 6e5d95a47261445e3031f55368f4e2cd8e2830a7
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754867"
---
# <a name="request-units-in-azure-cosmos-db"></a>Jednotky žádosti v Azure Cosmos DB

S Azure Cosmos DB platíte za propustnost, kterou zřizujete, a úložiště, které spotřebujete po hodinách. Aby bylo zajištěno, že budou pro vaši databázi Azure Cosmos neustále k dispozici dostatečné systémové prostředky, musí být zajištěna propustnost. K uspokojení nebo překročení [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)potřebujete dostatek prostředků.

Azure Cosmos DB podporuje mnoho rozhraní API, například SQL, MongoDB, Cassandra, Gremlin a Table. Každé rozhraní API má svou vlastní sadu databázových operací. Tyto operace jsou v rozsahu od jednoduchých čtení a zápisů do složitých dotazů. Každá databázová operace spotřebovává systémové prostředky na základě složitosti operace. 

Náklady na všechny databázové operace jsou normalizovány Azure Cosmos DB a jsou vyjádřeny *jednotkami žádostí* (nebo ru, pro krátké). Ru za sekundu si můžete představit jako měnu pro propustnost. Ru za sekundu je měna založená na sazbách. Vyabstrakcí systémové prostředky, jako je například CPU, IOPS a paměť, které jsou nutné k provedení databázových operací podporovaných nástrojem Azure Cosmos DB. 

Náklady na čtení položky 1 KB jsou 1 jednotka žádosti (nebo 1 RU). U všech ostatních databázových operací se podobně přiřazují náklady pomocí ru. Bez ohledu na to, jaké rozhraní API používáte k interakci s vaším kontejnerem Azure Cosmos, jsou náklady vždy měřeny pomocí ru. Bez ohledu na to, jestli je databázová operace zápis, čtení nebo dotazování, se náklady vždycky měří v ru.

Následující obrázek znázorňuje nejdůležitější představu o ru:

![Databázové operace spotřebovávají jednotky žádosti](./media/request-units/request-units.png)

Aby bylo možné spravovat a plánovat kapacitu, Azure Cosmos DB zajistí, že počet ru pro danou databázovou operaci nad danou datovou sadou je deterministický. Můžete prozkoumat hlavičku odpovědi a sledovat tak počet ru, které se spotřebují jakoukoli databázovou operací. Když rozumíte [faktorům, které mají vliv na poplatky za ru](request-units.md#request-unit-considerations) a požadavky na propustnost vaší aplikace, můžete efektivně spouštět náklady na aplikace.

Počet ru pro vaši aplikaci zřizujete za sekundu v přírůstcích po 100 ru za sekundu. Pokud chcete škálovat zřízenou propustnost vaší aplikace, můžete počet ru kdykoli zvýšit nebo snížit. Můžete škálovat v přírůstcích nebo snížení 100 ru. Změny můžete provést buď prostřednictvím kódu programu, nebo pomocí Azure Portal. Účtuje se vám každou hodinu.

Propustnost můžete zřídit ve dvou různých rozlišeních: 

* **Kontejnery**: Další informace najdete v tématu [zřízení propustnosti v kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
* **Databáze**: Další informace najdete v tématu [zřízení propustnosti databáze Azure Cosmos](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Doporučení pro jednotky žádosti

Při odhadování počtu ru za sekundu ke zřízení Vezměte v úvahu následující faktory:

* **Velikost položky**: podle velikosti položky se zvyšuje počet ru spotřebovaných pro čtení nebo zápis položky.

* **Indexování položek**: ve výchozím nastavení je každá položka automaticky indexována. Pokud se rozhodnete, že některé položky v kontejneru nechcete indexovat, ru se spotřebují méně.

* **Počet vlastností položky**: za předpokladu, že výchozí indexování je u všech vlastností, počet ru spotřebovaných pro zápis položky se zvětšuje podle počtu vlastností položky.

* **Indexované vlastnosti**: zásady indexů na jednotlivých kontejnerech určují, které vlastnosti jsou ve výchozím nastavení indexované. Chcete-li snížit spotřebu RU pro operace zápisu, omezte počet indexovaných vlastností.

* **Konzistence dat**: silná a ohraničená úroveň konzistence odolná proti chybám spotřebovává během operací čtení ve srovnání s jinými uvolněnými úrovněmi konzistence přibližně dvojnásobek více ru.

* **Vzory dotazů**: složitost dotazu ovlivňuje, kolik ru se spotřebuje pro určitou operaci. Mezi faktory, které ovlivňují náklady na operace dotazů, patří: 
    
    - Počet výsledků dotazu
    - Počet predikátů
    - Povaha predikátů
    - Počet uživatelsky definovaných funkcí
    - Velikost zdrojových dat
    - Velikost sady výsledků dotazu
    - Projekce

  Azure Cosmos DB garantuje, že stejný dotaz na stejná data vždy při opakovaném spuštění ru náklady na stejný počet.

* **Použití skriptu**: stejně jako u dotazů, uložené procedury a triggery spotřebovávají ru na základě složitosti operací, které jsou prováděny. Při vývoji aplikace si podíváte [hlavičku poplatků za žádosti](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) , abyste lépe pochopili, kolik z nich jednotlivé operace spotřebují.

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

---
title: Jednotky žádostí jako propustnost a výkonnostní měna v Azure Cosmos DB
description: Přečtěte si, jak zadat a odhadnout požadavky na jednotky požadavků v Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 6831cb3f39c25eb69d16300156f456980cf57fa0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88604833"
---
# <a name="request-units-in-azure-cosmos-db"></a>Jednotky žádostí ve službě Azure Cosmos DB

Azure Cosmos DB podporuje mnoho rozhraní API, například SQL, MongoDB, Cassandra, Gremlin a Table. Každé rozhraní API má svou vlastní sadu databázových operací. Tyto operace jsou v rozsahu od jednoduchých čtení a zápisů do složitých dotazů. Každá databázová operace spotřebovává systémové prostředky na základě složitosti operace.

Náklady na všechny databázové operace jsou normalizovány Azure Cosmos DB a jsou vyjádřeny *jednotkami žádostí* (nebo ru, pro krátké). Ru si můžete představit jako měnu výkonu pro abstrakci systémových prostředků, jako jsou například CPU, IOPS a paměť, které jsou potřeba k provádění databázových operací, které Azure Cosmos DB podporuje.

Náklady na čtení bodu (tj. načtení jedné položky podle jejího ID a hodnoty klíče oddílu) pro položku 1 KB je 1 jednotka žádosti (nebo 1 RU). Podobně jsou náklady v RU přiřazené ke všem ostatním databázovým operacím. Bez ohledu na to, jaké rozhraní API používáte k interakci s kontejnerem Azure Cosmos, se náklady vždy měří v jednotkách RU. Bez ohledu na to, jestli je databázová operace zápis, čtení z bodu nebo dotazování, se náklady vždycky měří v ru.

Následující obrázek znázorňuje základní myšlenku jednotek RU:

:::image type="content" source="./media/request-units/request-units.png" alt-text="Databázové operace spotřebovávají jednotky žádosti" border="false":::

Aby bylo možné spravovat a plánovat kapacitu, Azure Cosmos DB zajišťuje, že je počet RU pro konkrétní databázovou operaci nad danou datovou sadou deterministický. Pokud chcete sledovat počet RU spotřebovaných jakoukoli databázovou operací, můžete prozkoumat hlavičku odpovědi. Když rozumíte [faktorům, které mají vliv na poplatky za ru](request-units.md#request-unit-considerations) a požadavky na propustnost vaší aplikace, můžete efektivně spouštět náklady na aplikace.

Typ účtu Azure Cosmos, který používáte, určuje způsob, jakým se účtují spotřebované Ruy:

- V režimu [zřízené propustnosti](set-throughput.md) zřizujete počet ru pro vaši aplikaci za sekundu v přírůstcích po 100 ru za sekundu. Pro škálování zřízené propustnosti aplikace můžete zvýšit nebo snížit počet ru v jakémkoli okamžiku v přírůstcích nebo snižování 100 ru. Změny můžete naprogramovat nebo můžete použít Azure Portal. Poplatky se účtují po hodinách po dobu ru za sekundu, kterou jste zřídili. Propustnost můžete zřídit ve dvou různých rozlišeních:
  - **Kontejnery**: Další informace najdete v tématu [zřízení propustnosti v kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
  - **Databáze**: Další informace najdete v tématu [zřízení propustnosti databáze Azure Cosmos](how-to-provision-database-throughput.md).
- V režimu bez [serveru](serverless.md) nemusíte při vytváření prostředků v účtu Azure Cosmos zřizovat žádné propustnosti. Na konci fakturačního období se vám bude účtovat množství jednotek požadavků, které vaše databázové operace spotřebují.

## <a name="request-unit-considerations"></a>Co ovlivňuje jednotky žádosti

Při odhadování počtu ru spotřebovaných úlohami Vezměte v úvahu následující faktory:

* **Velikost položky**: Se vzrůstající velikostí položky se také zvyšuje počet jednotek žádosti spotřebovaných pro čtení a zápis.

* **Indexování položky**: Ve výchozím nastavení se automaticky indexuje každá položka. Pokud se rozhodnete některé z položek v kontejneru neindexovat, spotřebujete tak méně jednotek žádosti.

* **Počet vlastností položky**: Pokud u všech vlastností platí výchozí indexování, roste s počtem vlastností položky také počet jednotek žádosti spotřebovaných pro její zápis.

* **Indexované vlastnosti**: Vlastnosti, které se ve výchozím nastavení indexují, jsou určovány zásadami indexování každého kontejneru. Pokud tedy chcete snížit spotřebu jednotek žádosti pro operace zápisu, omezte počet indexovaných vlastností.

* **Konzistence dat**: silná a ohraničená úroveň konzistence odolná proti chybám spotřebovává během operací čtení ve srovnání s jinými uvolněnými úrovněmi konzistence přibližně dvojnásobek více ru.

* **Typ čtení**: Point čte náklady významně méně ru než dotazy.

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
* Přečtěte si další informace o [Azure Cosmos DB bez serveru](serverless.md).
* Přečtěte si další informace o [logických oddílech](partition-data.md).
* Přečtěte si další informace o [globálním škálování zřízené propustnosti](scaling-throughput.md).
* Naučte se [zřídit propustnost v kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
* Naučte se [zřídit propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).
* Přečtěte si, jak [Najít poplatek za jednotku žádosti o operaci](find-request-unit-charge.md).
* Naučte se [optimalizovat zřízené náklady na propustnost v Azure Cosmos DB](optimize-cost-throughput.md).
* Naučte se [optimalizovat čtení a zápis nákladů v Azure Cosmos DB](optimize-cost-reads-writes.md).
* Naučte se [optimalizovat náklady na dotaz v Azure Cosmos DB](optimize-cost-queries.md).
* Naučte se [používat metriky k monitorování propustnosti](use-metrics.md).

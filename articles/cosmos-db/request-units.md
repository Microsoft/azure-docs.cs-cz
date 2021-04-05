---
title: Jednotky žádostí jako propustnost a výkonnostní měna v Azure Cosmos DB
description: Přečtěte si, jak zadat a odhadnout požadavky na jednotky požadavků v Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2020
ms.custom: seo-nov-2020
ms.openlocfilehash: 23401885580a3883dc49eccc97c17bbedd9080ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96187319"
---
# <a name="request-units-in-azure-cosmos-db"></a>Jednotky žádostí ve službě Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB podporuje mnoho rozhraní API, například SQL, MongoDB, Cassandra, Gremlin a Table. Každé rozhraní API má svou vlastní sadu databázových operací. Tyto operace jsou v rozsahu od jednoduchých čtení a zápisů do složitých dotazů. Každá databázová operace spotřebovává systémové prostředky na základě složitosti operace.

Azure Cosmos DB normalizuje náklady na všechny databázové operace a vyjadřuje je v jednotkách žádostí (zkráceně RU). Jednotka požadavku je výkon abstrakce systémových prostředků, jako jsou například CPU, IOPS a paměť, které jsou potřeba k provádění databázových operací, které Azure Cosmos DB podporuje.

Náklady na čtení bodu (tj. načtení jedné položky podle jejího ID a hodnoty klíče oddílu) pro položku 1 KB je 1 jednotka žádosti (nebo 1 RU). Podobně jsou náklady v RU přiřazené ke všem ostatním databázovým operacím. Bez ohledu na to, jaké rozhraní API používáte k interakci s kontejnerem Azure Cosmos, se náklady vždy měří v jednotkách RU. Bez ohledu na to, jestli je databázová operace zápis, čtení z bodu nebo dotazování, se náklady vždycky měří v ru.

Následující obrázek znázorňuje základní myšlenku jednotek RU:

:::image type="content" source="./media/request-units/request-units.png" alt-text="Databázové operace spotřebovávají jednotky žádosti" border="false":::

Aby bylo možné spravovat a plánovat kapacitu, Azure Cosmos DB zajišťuje, že je počet RU pro konkrétní databázovou operaci nad danou datovou sadou deterministický. Pokud chcete sledovat počet RU spotřebovaných jakoukoli databázovou operací, můžete prozkoumat hlavičku odpovědi. Když rozumíte [faktorům, které mají vliv na poplatky za ru](request-units.md#request-unit-considerations) a požadavky na propustnost vaší aplikace, můžete efektivně spouštět náklady na aplikace.

Typ účtu Azure Cosmos, který používáte, určuje způsob, jakým se účtují poplatky za spotřebované ru. Existují tři režimy, ve kterých můžete vytvořit účet:

1. **Zřízený režim propustnosti**: v tomto režimu zřizujete počet ru pro vaši aplikaci za sekundu v přírůstcích po 100 ru za sekundu. Pro škálování zřízené propustnosti aplikace můžete zvýšit nebo snížit počet ru v jakémkoli okamžiku v přírůstcích nebo snižování 100 ru. Změny můžete naprogramovat nebo můžete použít Azure Portal. Poplatky se účtují po hodinách po dobu ru za sekundu, kterou jste zřídili. Další informace najdete v článku o [zřízené propustnosti](set-throughput.md) .

   Propustnost můžete zřídit ve dvou různých rozlišeních:

   * **Kontejnery**: Další informace najdete v tématu [zřízení propustnosti v kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
   * **Databáze**: Další informace najdete v tématu [zřízení propustnosti databáze Azure Cosmos](how-to-provision-database-throughput.md).

2. **Režim bez serveru**: v tomto režimu nemusíte při vytváření prostředků v účtu Azure Cosmos zřizovat žádné propustnosti. Na konci fakturačního období se vám bude účtovat množství jednotek požadavků, které vaše databázové operace spotřebují. Další informace najdete v článku o [propustnosti bez serveru](serverless.md) . 

3. **Režim automatického škálování**: v tomto režimu můžete automaticky a okamžitě škálovat propustnost (ru/s) databáze nebo kontejneru na základě využití IT, aniž by to ovlivnilo dostupnost, latenci, propustnost nebo výkon úlohy. Tento režim je vhodný pro klíčové úlohy, které mají proměnlivé nebo nepředvídatelné vzory přenosů dat, a vyžadují SLA s vysokým výkonem a škálováním. Další informace najdete v článku o [propustnosti automatického škálování](provision-throughput-autoscale.md) . 

## <a name="request-unit-considerations"></a>Co ovlivňuje jednotky žádosti

Při odhadování počtu ru spotřebovaných úlohami Vezměte v úvahu následující faktory:

* **Velikost položky**: Se vzrůstající velikostí položky se také zvyšuje počet jednotek žádosti spotřebovaných pro čtení a zápis.

* **Indexování položky**: Ve výchozím nastavení se automaticky indexuje každá položka. Pokud se rozhodnete některé z položek v kontejneru neindexovat, spotřebujete tak méně jednotek žádosti.

* **Počet vlastností položky**: Pokud u všech vlastností platí výchozí indexování, roste s počtem vlastností položky také počet jednotek žádosti spotřebovaných pro její zápis.

* **Indexované vlastnosti**: Vlastnosti, které se ve výchozím nastavení indexují, jsou určovány zásadami indexování každého kontejneru. Pokud tedy chcete snížit spotřebu jednotek žádosti pro operace zápisu, omezte počet indexovaných vlastností.

* **Konzistence dat**: silná a ohraničená úroveň konzistence odolná proti chybám spotřebovává během operací čtení ve srovnání s jinými uvolněnými úrovněmi konzistence přibližně dvojnásobek více ru.

* **Typ čtení**: Point čte náklady významně méně ru než dotazy.

* **Struktura dotazů**: Složitost dotazu má vliv na počet jednotek žádosti spotřebovaných operací. Mezi faktory ovlivňující náklady na operace dotazování patří: 
 
  * Počet výsledků dotazu
  * Počet predikátů
  * Povaha predikátů
  * Počet funkcí definovaných uživatelem
  * Velikost zdrojových dat
  * Velikost sady výsledků
  * Projekce

  Stejný dotaz na stejná data bude vždy při opakovaném spuštění ru náklady na stejný počet.

* **Použití skriptu**: stejně jako u dotazů, uložené procedury a triggery spotřebovávají ru na základě složitosti operací, které jsou prováděny. Abyste získali lepší přehled o tom, jakou kapacitu (vyjádřenou v jednotkách RU) každá operace spotřebuje, zkontrolujte při vývoji aplikace [hlavičku zátěže žádostmi](./optimize-cost-reads-writes.md#measuring-the-ru-charge-of-a-request).

## <a name="request-units-and-multiple-regions"></a>Jednotky žádostí a několik oblastí

Pokud zřizujete *' r '* ru na kontejneru Cosmos (nebo databázi), Cosmos DB zajistí, že je v *každé* oblasti přidružené k vašemu účtu Cosmos k dispozici ru *r* . Ru nelze selektivně přiřadit ke konkrétní oblasti. Ru zřízené na kontejneru Cosmos (nebo databázi) se zřídí ve všech oblastech přidružených k vašemu účtu Cosmos.

Za předpokladu, že je kontejner Cosmos nakonfigurovaný pomocí *R* ru a je *N* oblastí přidružených k účtu Cosmos, celkový ru k dispozici globálně na kontejneru = *R* x *N*.

Vaše volba [modelu konzistence](consistency-levels.md) má vliv také na propustnost. V porovnání s silnějšími úrovněmi konzistence můžete získat přibližně dvojnásobnou propustnost čtení pro příznivější úrovně konzistence *(například* *relace*, *konzistentní předpony* *a* konečná *konzistence* ).

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o tom, jak [zřídit propustnost na Cosmosch kontejnerech a databázích Azure](set-throughput.md).
- Přečtěte si další informace o [Azure Cosmos DB bez serveru](serverless.md).
- Přečtěte si další informace o [logických oddílech](./partitioning-overview.md).
- Naučte se [zřídit propustnost v kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
- Naučte se [zřídit propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).
- Přečtěte si, jak [Najít poplatek za jednotku žádosti o operaci](find-request-unit-charge.md).
- Naučte se [optimalizovat zřízené náklady na propustnost v Azure Cosmos DB](optimize-cost-throughput.md).
- Naučte se [optimalizovat čtení a zápis nákladů v Azure Cosmos DB](optimize-cost-reads-writes.md).
- Naučte se [optimalizovat náklady na dotaz v Azure Cosmos DB](./optimize-cost-reads-writes.md).
- Naučte se [používat metriky k monitorování propustnosti](use-metrics.md).

---
title: Jednotky požadavků a propustnost v db Služby Azure Cosmos
description: Informace o tom, jak specifikovat a odhadnout požadavky jednotky požadavku v Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: a0058bf309e0ff4fbe687731d676e907d1c3fd82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246588"
---
# <a name="request-units-in-azure-cosmos-db"></a>Jednotky požadavků v Azure Cosmos DB

V případě služby Azure Cosmos DB platíte po hodinách za zřízenou propustnost a spotřebované úložiště. Propustnost je potřeba zřídit, aby se zajistilo, že databáze Azure Cosmos bude mít vždy k dispozici dostatek systémových prostředků. Potřebujete dostatek prostředků ke splnění nebo překročení [azure cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Azure Cosmos DB podporuje mnoho rozhraní API, jako je NAPŘÍKLAD SQL, MongoDB, Cassandra, Gremlin a Table. Každé rozhraní API má vlastní sadu databázových operací. Tyto operace se pohybují od jednoduchých přečte a zapisuje bodů na složité dotazy. Každá databázová operace spotřebovává systémové prostředky na základě složitosti operace. 

Náklady na všechny databázové operace je normalizovánazure Cosmos DB a je vyjádřena *jednotky požadavku* (nebo ru, zkráceně). Jednotky RU za sekundu si můžete představit jako měnu pro propustnost. Jednotky RU za sekundu představují měnu založenou na rychlosti. Abstrahuje systémové prostředky, jako jsou procesor, IOPS nebo paměť, potřebné k provedení databázových operací podporovaných službou Azure Cosmos DB. 

Náklady na čtení 1kB položky jsou 1 jednotka žádosti (neboli 1 RU). Pro uložení každého 1 GB dat je zapotřebí minimálně 10 RU/s. Podobně jsou náklady v RU přiřazené ke všem ostatním databázovým operacím. Bez ohledu na to, jaké rozhraní API používáte k interakci s kontejnerem Azure Cosmos, se náklady vždy měří v jednotkách RU. Ať už je databázovou operací zápis, čtení nebo dotazování, náklady se vždy měří v jednotkách RU.

Následující obrázek znázorňuje základní myšlenku jednotek RU:

![Databázové operace spotřebovávají jednotky požadavků](./media/request-units/request-units.png)

Aby bylo možné spravovat a plánovat kapacitu, Azure Cosmos DB zajišťuje, že je počet RU pro konkrétní databázovou operaci nad danou datovou sadou deterministický. Pokud chcete sledovat počet RU spotřebovaných jakoukoli databázovou operací, můžete prozkoumat hlavičku odpovědi. Když rozumíte [faktorům, které ovlivňují poplatky ru](request-units.md#request-unit-considerations) a požadavky na propustnost vaší aplikace, můžete aplikaci spustit nákladově efektivně.

Počet RU pro aplikaci se zřizuje po sekundách v přírůstcích pro 100 RU za sekundu. Pokud chcete škálovat zřízenou propustnost pro vaši aplikaci, můžete počet RU kdykoli zvýšit nebo snížit. Škálovat můžete v přírůstcích po 100 RU. Změny můžete provést programově nebo pomocí webu Azure Portal. Účtování probíhá po hodinách.

Propustnost můžete zřídit na dvě odlišné rozlišovací schopnosti: 

* **Kontejnery**: Další informace naleznete [v tématu Zřízení propustnost na kontejnerU Azure Cosmos](how-to-provision-container-throughput.md).
* **Databáze**: Další informace naleznete [v tématu Provision propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Aspekty jednotek žádosti

Při plánování počtu jednotek žádosti za sekundu, které budete zřizovat, vezměte v úvahu tyto faktory:

* **Velikost položky**: S velikostí položky se zvyšuje také počet ru spotřebovaných ke čtení nebo zápisu položky.

* **Indexování položek**: Ve výchozím nastavení je každá položka automaticky indexována. Pokud se rozhodnete některé z položek v kontejneru neindexovat, spotřebujete tak méně jednotek žádosti.

* **Počet vlastností položky**: Za předpokladu, že výchozí indexování je na všechny vlastnosti, počet ru spotřebované k zápisu položky zvyšuje jako počet vlastností položky zvyšuje.

* **Indexované vlastnosti**: Zásada indexu pro každý kontejner určuje, které vlastnosti jsou ve výchozím nastavení indexovány. Pokud tedy chcete snížit spotřebu jednotek žádosti pro operace zápisu, omezte počet indexovaných vlastností.

* **Konzistence dat**: Silné a ohraničené úrovně konzistence neaktuálnosti spotřebovávají přibližně dvakrát více ru při provádění operací čtení ve srovnání s jinými úrovněmi uvolněné konzistence.

* **Vzorky dotazu**: Složitost dotazu ovlivňuje, kolik ru jsou spotřebovány pro operaci. Mezi faktory ovlivňující náklady na operace dotazování patří: 
    
    - Počet výsledků dotazu
    - Počet predikátů
    - Povaha predikátů
    - Počet funkcí definovaných uživatelem
    - Velikost zdrojových dat
    - Velikost sady výsledků
    - Projekce

  Služba Azure Cosmos DB garantuje, že při opakovaných spouštěních se náklady v podobě jednotek žádosti u stejného dotazu na stejná data nezmění.

* **Použití skriptu**: Stejně jako u dotazů, uložené procedury a aktivační události spotřebovávají ru na základě složitosti operací, které jsou prováděny. Abyste získali lepší přehled o tom, kolik jednotek žádosti každá operace spotřebuje, zkontrolujte při vývoji aplikace [hlavičku poplatku za žádost](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query).

## <a name="next-steps"></a>Další kroky

* Další informace o tom, jak [zřídit propustnost na kontejnery A databáze Azure Cosmos](set-throughput.md).
* Další informace o [logických oddílech](partition-data.md).
* Další informace o tom, jak [globálně škálovat zřízenou propustnost](scaling-throughput.md).
* Zjistěte, jak [zřídit propustnost v kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
* Zjistěte, jak [zřídit propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).
* Přečtěte si, jak [najít poplatek za jednotku požadavku na operaci](find-request-unit-charge.md).
* Zjistěte, jak optimalizovat náklady na [zřízenou propustnost v Azure Cosmos DB](optimize-cost-throughput.md).
* Zjistěte, jak optimalizovat náklady na [čtení a zápisy v Azure Cosmos DB](optimize-cost-reads-writes.md).
* Zjistěte, jak [optimalizovat náklady na dotazy v Azure Cosmos DB](optimize-cost-queries.md).
* Přečtěte si, jak [pomocí metrik sledovat propustnost](use-metrics.md).

---
title: Globálně distribuované transakční a analytické úložiště (v privátní verzi Preview) pro kontejnery Azure Cosmos
description: Přečtěte si o transakčních a analytických úložištích a možnostech jejich konfigurace pro kontejnery Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: 1c2b79f8d0641b1a1386329a2add14ded766bf5a
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623383"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Globálně distribuované transakční a analytické úložiště pro kontejnery Azure Cosmos

Služba Azure Cosmos Container je interně založená na dvou úložištích – transakční modul úložiště a aktualizovatelný modul analytického úložiště (v privátní verzi Preview). Úložné moduly jsou strukturované a optimalizované pro zápis pro rychlejší aktualizace. Každý z nich je však kódován odlišně:

* **Transakční modul úložiště** – je kódovaný v řádkovém formátu pro rychlé transakční čtení a dotazy.

* **Analytický modul úložiště** – je kódovaný ve sloupcovém formátu pro rychlé analytické dotazy a kontroly.

![Moduly úložiště a mapování Azure Cosmos DB API](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

Transakční modul úložiště je zálohovaný místními SSD, zatímco analytické úložiště je uložené v nenákladovém úložišti SSD mimo cluster. Následující tabulka zachycuje významné rozdíly mezi transakčním a analytickým úložištěm.


|Funkce  |Transakční úložiště  |Analytické úložiště |
|---------|---------|---------|
|Maximální velikost úložiště na jeden kontejner Azure Cosmos |   Neomezeno      |    Neomezeno     |
|Maximální velikost úložiště na klíč logického oddílu   |   20 GB      |   Neomezeno      |
|Kódování úložiště  |   Řádky orientované pomocí interního formátu.   |   Sloupce orientované pomocí formátu Apache Parquet. |
|Místní úložiště |   Replikované úložiště, které je založené na místním/souběžném clusteru SSD. |  Replikované úložiště zajištěné nenáročným vzdáleným/vypnutým clusterem SSD.       |
|Stálost  |    99,99999 (7-9 s)     |  99,99999 (7-9 s)       |
|Rozhraní API, která přistupují k datům  |   SQL, MongoDB, Cassandra, Gremlin, Tables a etcd.       | Apache Spark         |
|Uchování (Time-to-Live nebo TTL)   |  Řízené zásadami nakonfigurovanými na kontejneru Azure Cosmos pomocí vlastnosti `DefaultTimeToLive`.       |   Řízené zásadami nakonfigurovanými na kontejneru Azure Cosmos pomocí vlastnosti `ColumnStoreTimeToLive`.      |
|Cena za GB    |   Zobrazit [stránku s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/)     |   Zobrazit [stránku s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/)        |
|Cena za transakce úložiště    |  Zobrazit [stránku s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/)         |   Zobrazit [stránku s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/)        |

## <a name="benefits-of-transactional-and-analytical-storage"></a>Výhody transakčního a analytického úložiště

### <a name="no-etl-operations"></a>Žádné operace ETL

Tradiční analytické kanály jsou složité s více fázemi, které vyžadují operace extrakce, transformace a načítání (ETL) do a z výpočetních a úložných vrstev. Výsledkem je komplexní architektury zpracování dat. Což znamená vysoké náklady na více fází úložiště a výpočetních prostředků a vysokou latenci kvůli obrovským objemům dat přenesených mezi různými fázemi úložiště a výpočetními prostředky.  

Nehrozí žádná režie na provádění operací ETL pomocí Azure Cosmos DB. Každý kontejner Azure Cosmos je zálohovaný pomocí transakčního i analytického strojového úložiště a přenos dat mezi transakčním a analytickým modulem úložiště se provádí v rámci databázového stroje a bez směrování sítě. Výsledná latence a náklady jsou v porovnání s tradičními analytickými řešeními výrazně nižší. A získáte jeden globálně distribuovaný úložný systém pro transakční i analytické úlohy.  

### <a name="store-multiple-versions-update-and-query-the-analytical-storage"></a>Uložení více verzí, aktualizace a dotazování analytického úložiště

Analytické úložiště je plně aktualizovatelné a obsahuje kompletní historii všech transakčních aktualizací, ke kterým došlo v kontejneru Azure Cosmos.

Jakékoli aktualizace transakčního úložiště jsou zaručené k analýze úložiště do 30 sekund. 

### <a name="globally-distributed-multi-master-analytical-storage"></a>Globálně distribuované analytické úložiště s více hlavními servery

Pokud je váš účet Azure Cosmos vymezený na jednu oblast, jsou uložená data (v transakčním a analytickém úložišti) v kontejnerech také vymezena v jedné oblasti. Na druhou stranu platí, že pokud je účet Azure Cosmos globálně distribuovaný, data uložená v kontejnerech se také globálně distribuují.

U účtů Azure Cosmos konfigurovaných s více oblastmi zápisu se zápisy do kontejneru (do transakčního i analytického úložiště) vždycky provádějí v místní oblasti, a proto jsou rychlé.

Pro jeden nebo více oblastí Azure Cosmos účet bez ohledu na to, jestli je jedna oblast pro zápis (jedna předloha) nebo více oblastí zápisu (označovaná také jako vícenásobný příklad), provádějí se transakční i analytické čtení a dotazy v dané oblasti místně.

### <a name="performance-isolation-between-transactional-and-analytical-workloads"></a>Izolace výkonu mezi transakčními a analytickými úlohami

V dané oblasti transakční úlohy fungují na transakčním a řádkovém úložišti vašeho kontejneru. Na druhé straně analytické úlohy působí v rámci analytického a sloupcového úložiště vašeho kontejneru. Tyto dva úložné motory pracují nezávisle a poskytují striktní izolaci výkonu mezi úlohami.

Transakční úlohy využívají zřízenou propustnost (ru). Na rozdíl od transakčních úloh je propustnost analytických úloh založena na skutečné spotřebě. Analytické úlohy využívají prostředky na vyžádání.

## <a name="next-steps"></a>Další kroky

* [Čas do provozu v Azure Cosmos DB](time-to-live.md)

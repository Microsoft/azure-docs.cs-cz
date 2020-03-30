---
title: Globálně distribuované transakční a analytické (v privátní verzi Preview) úložiště pro kontejnery Azure Cosmos
description: Seznamte se s transakčním a analytickým úložištěm a jejich možnostmi konfigurace pro kontejnery Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: 1c2b79f8d0641b1a1386329a2add14ded766bf5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623383"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Globálně distribuované transakční a analytické úložiště pro kontejnery Azure Cosmos

Kontejner Azure Cosmos je interně zálohován dvěma moduly úložiště – transakčním úložištěm a aktualizovatelným analytickým úložištěm (v privátní verzi Preview). Oba moduly úložiště jsou strukturované protokolem a optimalizované pro zápis pro rychlejší aktualizace. Nicméně, každý z nich je kódován jinak:

* **Transakční úložiště motor –** je kódován ve formátu orientovaném na řádek pro rychlé transakční čtení a dotazy.

* **Analytický úložný modul** - Je kódován ve sloupcovém formátu pro rychlé analytické dotazy a skenování.

![Moduly úložišť a mapování rozhraní API Azure Cosmos DB](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

Modul transakčníúložiště je zálohována místní ssd disky, zatímco analytické úložiště je uloženo na levné úložiště SSD mimo cluster. Následující tabulka zachycuje významné rozdíly mezi transakční a analytické úložiště.


|Funkce  |Transakční úložiště  |Analytické skladování |
|---------|---------|---------|
|Maximální úložiště na kontejner Azure Cosmos |   Unlimited      |    Unlimited     |
|Maximální úložiště na klíč logického oddílu   |   20 GB      |   Unlimited      |
|Kódování úložiště  |   Orientované na řádky, pomocí interního formátu.   |   Orientace na sloupce, pomocí formátu Apache Parquet. |
|Lokalita úložiště |   Replikované úložiště podporované místními nebo vnitroclusterovými disky SSD. |  Replikované úložiště podporované levnými vzdálenými nebo offclusterovými ssd disky.       |
|Stálost  |    99,99999 (7-9 s)     |  99,99999 (7-9 s)       |
|API, která přistupují k datům  |   SQL, MongoDB, Cassandra, Gremlin, tabulky a etcd.       | Apache Spark         |
|Uchovávání (doba aktivní nebo TTL)   |  Zásady řízené, nakonfigurované v `DefaultTimeToLive` kontejneru Azure Cosmos pomocí vlastnosti.       |   Zásady řízené, nakonfigurované v `ColumnStoreTimeToLive` kontejneru Azure Cosmos pomocí vlastnosti.      |
|Cena za GB    |   Podívejte se na [stránku s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/)     |   Podívejte se na [stránku s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/)        |
|Cena za skladovací transakce    |  Podívejte se na [stránku s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/)         |   Podívejte se na [stránku s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/)        |

## <a name="benefits-of-transactional-and-analytical-storage"></a>Výhody transakčního a analytického skladování

### <a name="no-etl-operations"></a>Žádné operace ETL

Tradiční analytické kanály jsou složité s více fázemi, z nichž každá vyžaduje operace extrakce transformace a zatížení (ETL) do a z výpočetních a úložných vrstev. Výsledkem je komplexní architektura zpracování dat. Což znamená vysoké náklady na více fází úložiště a výpočetních prostředků a vysokou latenci díky masivním objemům dat přenášených mezi různými fázemi úložiště a výpočetních prostředků.  

Neexistuje žádná režie provádění operací ETL s Azure Cosmos DB. Každý kontejner Azure Cosmos je zálohována transakční a analytické moduly úložiště a přenos dat mezi transakční a analytický modul úložiště se provádí v rámci databázového stroje a bez jakékoli sítě směrování. Výsledná latence a náklady jsou výrazně nižší ve srovnání s tradičními analytickými řešeními. A získáte jeden globálně distribuovaný úložný systém pro transakční i analytické úlohy.  

### <a name="store-multiple-versions-update-and-query-the-analytical-storage"></a>Uložení více verzí, aktualizace a dotazování na analytické úložiště

Analytické úložiště je plně aktualizovatelné a obsahuje úplnou historii verzí všech transakčních aktualizací, ke kterým došlo v kontejneru Azure Cosmos.

Jakákoli aktualizace transakčního úložiště je zaručena viditelná pro analytické úložiště do 30 sekund. 

### <a name="globally-distributed-multi-master-analytical-storage"></a>Globálně distribuované analytické úložiště s více servery

Pokud váš účet Azure Cosmos je vymezenna do jedné oblasti, data uložená (v transakční a analytické úložiště) v kontejnerech je také vymezena na jednu oblast. Na druhou stranu pokud je účet Azure Cosmos globálně distribuován, data uložená v kontejnerech jsou také globálně distribuována.

Pro účty Azure Cosmos nakonfigurované s více oblastí zápisu, zápisy do kontejneru (do transakční a analytické úložiště) se vždy provádí v místní oblasti, a proto jsou rychlé.

Pro účty Azure Cosmos s jednou nebo více oblastmi, bez ohledu na to, zda se v dané oblasti provádí oblast jednoho zápisu (jeden hlavní) nebo více oblastí zápisu (označované také jako více hlavních), transakční i analytické čtení/dotazy místně v dané oblasti.

### <a name="performance-isolation-between-transactional-and-analytical-workloads"></a>Izolace výkonu mezi transakčními a analytickými úlohami

V dané oblasti transakční úlohy pracovat proti transakční úložiště kontejneru/řádek. Na druhou stranu analytické úlohy pracují s analytickým/sloupcovým úložištěm vašeho kontejneru. Dva moduly úložiště pracují nezávisle a poskytují přísnou izolaci výkonu mezi úlohami.

Transakční úlohy spotřebovávají zřízenou propustnost (RU). Na rozdíl od transakčních úloh je propustnost analytických úloh založena na skutečné spotřebě. Analytické úlohy spotřebovávají prostředky na vyžádání.

## <a name="next-steps"></a>Další kroky

* [Čas žít v Azure Cosmos DB](time-to-live.md)

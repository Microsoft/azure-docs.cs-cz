---
title: Azure SQL Database – obecné účely a pro důležité obchodní informace | Dokumentace Microsoftu
description: Článek popisuje obecné účely a obchodní vrstvou kritické služby v nákupu modelu virtuálních jader.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan, moslake
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: 2a9b84b20ab1480c9b0222980cb36c40e8017973
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55989302"
---
# <a name="azure-sql-database-service-tiers"></a>Úrovně služby Azure SQL Database

Azure SQL Database je založené na architektuře databázový stroj SQL serveru, která je upravená pro cloudové prostředí, aby bylo možné zajistit 99,99 % dostupnost i v případě selhání infrastruktury. Existují tři modely architektury, které se používají ve službě Azure SQL Database:

- [Obecné účely](sql-database-service-tier-general-purpose.md) navržené pro většinu obecných úloh.
- [Důležité obchodní informace](sql-database-service-tier-business-critical.md) navržené pro úlohy s nízkou latencí pomocí jednoho čitelných replik.
- [Velkokapacitní](sql-database-service-tier-hyperscale.md) je určená pro velké databáze (až 100 TB) s více čitelné repliky.

Tento článek popisuje aspekty úložiště a zálohování pro úrovně služeb pro obecné účely a pro důležité obchodní informace v nákupní model založený na virtuálních jádrech.

> [!NOTE]
> Podrobnosti na úrovni služby Hyperškálovatelného v nákupní model založený na virtuálních jádrech najdete v tématu [úroveň služby Hyperškálovatelného](sql-database-service-tier-hyperscale.md). Porovnání nákupní model s založený na DTU nákupní model založený na virtuálních jádrech najdete v tématu [nákupu modely a prostředků Azure SQL Database](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Úložiště dat a protokolů

Zvažte použití těchto zdrojů:

- Přidělené úložiště využívané datové soubory (MDF) a soubory protokolu souborů (LDF).
- Každou izolovanou databázi vypočítat velikost podporuje maximální velikost databáze, s výchozí maximální velikost 32 GB.
- Když konfigurujete velikost požadované izolovanou databázi (velikost MDF), 30 % dalšího úložiště, je automaticky přidaná kvůli podpoře LDF
- Velikost úložiště ve spravované instanci musí být určena v násobcích po 32 GB.
- Můžete vybrat libovolné velikosti izolované databáze mezi 10 GB a podporované maximum
  - Pro úložiště úrovně standard nebo obecné účely zvětšit nebo zmenšit velikost v přírůstcích po 10 GB
  - Pro úložiště úrovně premium nebo buxiness kritických služeb zvětšit nebo zmenšit velikost v přírůstcích po 250 GB
- V úrovni General Purpose služby `tempdb` používá připojené SSD a náklady na toto úložiště je zahrnutá v ceně vCore.
- V rámci úrovně služeb pro důležité obchodní informace `tempdb` sdílené složky připojené SSD se soubory MDF a LDF a náklady na úložiště databáze tempDB je zahrnutá v ceně vCore.

> [!IMPORTANT]
> Bude vám účtována celkový úložiště přidělené MDF a LDF.

Chcete-li monitorovat aktuální celková velikost MDF a LDF, použijte [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Chcete-li monitorovat aktuální velikost jednotlivých souborů MDF a LDF, použijte [katalogu sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Za určitých okolností budete muset zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [spravovat místo souborů ve službě Azure SQL Database](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Zálohování a úložiště

Přidělení úložiště pro zálohování databáze podporují bod v webu času obnovení (PITR) a [dlouhá období uchovávání dat (LTR)](sql-database-long-term-retention.md) schopnosti služby SQL Database. Toto úložiště je přidělena odděleně pro každou databázi a účtovat jako dvě samostatné na databázi poplatky.

- **PITR**: Zálohování jednotlivých databází se zkopírují do [úložiště RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md) se automaticky. Velikost úložiště zvyšuje dynamicky vytvářené nových záloh.  Úložiště využívané týdenními úplnými zálohami, denními rozdílovými zálohami a zálohami protokolů transakcí se kopíruje každých 5 minut. Spotřeba úložiště závisí na četnosti změn databáze a dobu uchování. Můžete nakonfigurovat samostatný uchovávají pro každou databázi mezi 7 až 35 dnů. Minimální objem úložiště rovna 1 x velikosti dat je k dispozici bez dalších poplatků. Pro většinu databází je tato částka dostatečná k uložení zálohy za 7 dní.
- **LTR**: SQL Database nabízí možnost konfigurace dlouhodobého uchovávání úplné zálohy po dobu až 10 let. Pokud je povolené zásady LTR, tyto zálohy se ukládají do úložiště RA-GRS automaticky, ale můžete řídit, jak často se zkopírují zálohy. Aby splnila požadavek na dodržení jiný, můžete vybrat různých období uchovávání záloh týdenní, měsíční nebo roční. Tato konfigurace bude definovat, jak velké úložiště se použije k zálohování LTR. Zleva doprava cenové kalkulačky můžete odhadnout náklady na úložiště zleva doprava. Další informace najdete v tématu [Dlouhodobé uchovávání](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Další postup

- Podrobnosti o konkrétní výpočetní prostředky, velikosti a možnosti velikosti úložiště dostupné pro izolované databáze na úrovni obecné účely a důležité firemní službu, najdete v části [limity prostředků založený na virtuálních jádrech SQL Database pro izolované databáze](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)
- Podrobnosti o konkrétní výpočetní prostředky, velikosti a možnosti velikosti úložiště dostupné pro elastické fondy pro obecné účely a služba pro důležité obchodní vrstvy, najdete v části [limity pro elastické fondy SQL Database založené na virtuálních jádrech prostředků](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).

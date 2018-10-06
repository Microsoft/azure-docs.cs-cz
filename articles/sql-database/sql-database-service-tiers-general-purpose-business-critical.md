---
title: Azure SQL Database – obecné účely a pro důležité obchodní informace | Dokumentace Microsoftu
description: Článek popisuje obecné účely a obchodní vrstvou kritické služby v nákupu modelu virtuálních jader.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan, moslake
manager: craigg
ms.date: 10/04/2018
ms.openlocfilehash: 053bcd46f5b0f7e06997bb0bcd57f448617b9911
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2018
ms.locfileid: "48832171"
---
# <a name="general-purpose-and-business-critical-service-tiers"></a>Obecné účely a pro důležité obchodní informace úrovně služeb

Tento článek popisuje aspekty úložiště a zálohování pro úrovně služeb pro obecné účely a pro důležité obchodní informace v nákupní model založený na virtuálních jádrech. 

> [!NOTE]
> Podrobnosti na úrovni služby Hyperškálovatelného v nákupní model založený na virtuálních jádrech najdete v tématu [úroveň služby Hyperškálovatelného](sql-database-service-tier-hyperscale.md). Porovnání nákupní model s založený na DTU nákupní model založený na virtuálních jádrech najdete v tématu [nákupu modely a prostředků Azure SQL Database](sql-database-service-tiers.md).

## <a name="data-and-log-storage"></a>Úložiště dat a protokolů

Zvažte použití těchto zdrojů:

- Přidělené úložiště využívané datové soubory (MDF) a soubory protokolu souborů (LDF).
- Každou izolovanou databázi vypočítat velikost podporuje maximální velikost databáze, s výchozí maximální velikost 32 GB.
- Když konfigurujete velikost požadované izolovanou databázi (velikost MDF), 30 % dalšího úložiště, je automaticky přidaná kvůli podpoře LDF
- Velikost úložiště ve spravované instanci musí být určena v násobcích po 32 GB.
- Můžete vybrat libovolné velikosti izolované databáze mezi 10 GB a podporované maximum
  - Pro úložiště úrovně Standard zvětšit nebo zmenšit velikost v přírůstcích po 10 GB
  - Pro Premium storage zvětšit nebo zmenšit velikost v přírůstcích po 250 GB
- V úrovni General Purpose služby `tempdb` používá připojené SSD a náklady na toto úložiště je zahrnutá v ceně vCore.
- V rámci úrovně služeb pro důležité obchodní informace `tempdb` sdílené složky připojené SSD se soubory MDF a LDF a náklady na úložiště databáze tempDB je zahrnutá v ceně vCore.

> [!IMPORTANT]
> Bude vám účtována celkový úložiště přidělené MDF a LDF.

Chcete-li monitorovat aktuální celková velikost MDF a LDF, použijte [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Chcete-li monitorovat aktuální velikost jednotlivých souborů MDF a LDF, použijte [katalogu sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Za určitých okolností budete muset zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [spravovat místo souborů ve službě Azure SQL Database](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Zálohování a úložiště

Přidělení úložiště pro zálohování databáze podporují bod v webu času obnovení (PITR) a [dlouhá období uchovávání dat (LTR)](sql-database-long-term-retention.md) schopnosti služby SQL Database. Toto úložiště je přidělena odděleně pro každou databázi a účtovat jako dvě samostatné na databázi poplatky.

- **PITR**: zálohování jednotlivých databází se zkopírují do [úložiště RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md) se automaticky. Velikost úložiště zvyšuje dynamicky vytvářené nových záloh.  Úložiště využívané týdenními úplnými zálohami, denními rozdílovými zálohami a zálohami protokolů transakcí se kopíruje každých 5 minut. Spotřeba úložiště závisí na četnosti změn databáze a dobu uchování. Můžete nakonfigurovat samostatný uchovávají pro každou databázi mezi 7 až 35 dnů. Minimální objem úložiště rovna 1 x velikosti dat je k dispozici bez dalších poplatků. Pro většinu databází je tato částka dostatečná k uložení zálohy za 7 dní.
- **LTR**: SQL Database nabízí možnost konfigurace dlouhodobého uchovávání úplné zálohy po dobu až 10 let. Pokud je povolené zásady LTR, tyto zálohy se ukládají do úložiště RA-GRS automaticky, ale můžete řídit, jak často se zkopírují zálohy. Aby splnila požadavek na dodržení jiný, můžete vybrat různých období uchovávání záloh týdenní, měsíční nebo roční. Tato konfigurace bude definovat, jak velké úložiště se použije k zálohování LTR. Zleva doprava cenové kalkulačky můžete odhadnout náklady na úložiště zleva doprava. Další informace najdete v tématu [Dlouhodobé uchovávání](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Další postup

- Podrobnosti o konkrétní výpočetní prostředky, velikosti a možnosti velikosti úložiště dostupné pro izolované databáze na úrovni obecné účely a důležité firemní službu, najdete v části [limity prostředků založený na virtuálních jádrech SQL Database pro izolované databáze](sql-database-vcore-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)
- Podrobnosti o konkrétní výpočetní prostředky, velikosti a možnosti velikosti úložiště dostupné pro elastické fondy pro obecné účely a služba pro důležité obchodní vrstvy, najdete v části [limity pro elastické fondy SQL Database založené na virtuálních jádrech prostředků](sql-database-vcore-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).

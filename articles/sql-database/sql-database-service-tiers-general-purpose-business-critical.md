---
title: Azure SQL Database – obecné účely a pro důležité obchodní informace | Dokumentace Microsoftu
description: Článek popisuje obecné účely a obchodními úrovněmi kritické služby v nákupní model založený na virtuálních jádrech.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
manager: craigg
ms.date: 02/23/2019
ms.openlocfilehash: e5426bb7c8eba9d58dbf0472360c6ce0b19c9bc4
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431343"
---
# <a name="azure-sql-database-service-tiers"></a>Úrovně služby Azure SQL Database

Azure SQL Database je založená na architektuře modul databáze SQL serveru, který je upraven pro cloudové prostředí a zajistit tak dostupnost 99,99 % dostupnost i v případě, že dojde chybě infrastruktury. Tři úrovně služby slouží ve službě Azure SQL Database, každý s modelem jiné architektury. Tyto úrovně služby jsou:

- [Obecné účely](sql-database-service-tier-general-purpose.md), která je navržená pro největší obecné úlohy.
- [Pro důležité obchodní informace](sql-database-service-tier-business-critical.md), která je navržená pro úlohy s nízkou latencí pomocí jednoho čitelných replik.
- [Velkokapacitní](sql-database-service-tier-hyperscale.md), která je navržená pro velmi velké databáze (až 100 TB) s více čitelné repliky.

Tento článek popisuje aspekty úložiště a zálohování pro obecné účely a obchodními úrovněmi kritické služby v nákupní model založený na virtuálních jádrech.

> [!NOTE]
> Informace o úrovni služeb hyperškálovatelného v nákupní model založený na virtuálních jádrech najdete v tématu [úroveň služby hyperškálovatelného](sql-database-service-tier-hyperscale.md). Porovnání nákupní model s založený na DTU nákupní model založený na virtuálních jádrech najdete v tématu [nákupu modely a prostředků Azure SQL Database](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Úložiště dat a protokolů

Těchto faktorů vliv na velikost úložiště využitá pro data a soubory protokolu:

- Přidělené úložiště využívané datové soubory (MDF) a soubory protokolu (LDF).
- Každou izolovanou databázi vypočítat velikost podporuje maximální velikost databáze, s výchozí maximální velikost 32 GB.
- Když konfigurujete velikost požadované izolovanou databázi (velikosti souboru MDF), 30 procent více dodatečné úložiště se automaticky přidá do soubory LDF podpory.
- Velikost úložiště pro spravovanou instanci SQL Database musí být určena v násobcích po 32 GB.
- Můžete vybrat libovolné velikosti izolované databáze mezi 10 GB a maximální podporované.
  - Pro úložiště úrovně standard nebo obecné účely zvětšit nebo zmenšit velikost v přírůstcích po 10 GB.
  - Pro úložiště úrovně premium nebo obchodními úrovněmi důležité služby, zvýšení nebo snížení velikosti v přírůstcích po 250 GB.
- V úrovni general purpose služby `tempdb` používá připojené SSD a náklady na toto úložiště je zahrnutá v ceně vCore.
- V obchodní vrstvě důležité služby `tempdb` sdílí soubory MDF a LDF připojené SSD a `tempdb` náklady na úložiště je zahrnutá v ceně vCore.

> [!IMPORTANT]
> Bude vám účtována celkový úložiště přidělené pro soubory MDF a LDF.

Chcete-li monitorovat aktuální celková velikost souborů MDF a LDF, použijte [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Chcete-li monitorovat aktuální velikost jednotlivých souborů MDF a LDF, použijte [katalogu sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Za určitých okolností budete muset zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [spravovat místo souborů ve službě Azure SQL Database](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Zálohování a úložiště

Přidělení úložiště pro zálohování databáze podporující obnovení bodu v čase (PITR) a [dlouhodobé uchovávání dat (LTR)](sql-database-long-term-retention.md) schopnosti služby SQL Database. Toto úložiště je přidělena odděleně pro každou databázi a účtovat jako dvě samostatné na databázi poplatky.

- **PITR**: Zálohování jednotlivých databází se zkopírují do [geograficky redundantní úložiště (RA-GRS) jen pro čtení](../storage/common/storage-designing-ha-apps-with-ragrs.md) automaticky. Velikost úložiště zvyšuje dynamicky vytvářené nových záloh. Úložiště využívané týdenními úplnými zálohami, denními rozdílovými zálohami a zálohování protokolů transakcí, které se kopírují každých 5 minut. Spotřeba úložiště závisí na četnosti změn databáze a doby uchování pro zálohy. Můžete nakonfigurovat samostatný uchovávají pro každou databázi mezi 7 až 35 dnů. Minimální objem úložiště velikosti 100 % (1 x) velikosti databáze, je k dispozici bez dalších poplatků. Pro většinu databází je tato částka dostatečná k uložení zálohy za 7 dní.
- **LTR**: SQL Database nabízí možnost konfigurace dlouhodobého uchovávání úplné zálohy po dobu až 10 let. Pokud jste nastavili zásadu zleva doprava, tyto zálohy jsou automaticky ukládají do úložiště RA-GRS, ale můžete řídit, jak často se zkopírují zálohy. Pro splnění požadavků na jiné dodržování předpisů, můžete vybrat různých období uchovávání záloh týdenní, měsíční nebo roční. Zvolené konfigurace určuje, jak velké úložiště se použije pro zálohy zleva doprava. Pokud chcete odhadnout náklady na úložiště zleva doprava, můžete použít cenovou kalkulačku zleva doprava. Další informace najdete v tématu [dlouhodobé uchovávání SQL Database](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Další postup

- Podrobnosti o konkrétní výpočetní prostředky, velikosti a velikosti úložiště dostupné pro izolované databáze na úrovních služby business a obecné účely, najdete v části [limity prostředků založený na virtuálních jádrech SQL Database pro izolované databáze](sql-database-vcore-resource-limits-single-databases.md).
- Podrobnosti o konkrétní výpočetní prostředky, velikosti a velikosti úložiště dostupné pro elastické fondy v obecné účely a obchodními úrovněmi důležité služby, najdete v části [limity pro elastické fondy SQL Database založené na virtuálních jádrech prostředků](sql-database-vcore-resource-limits-elastic-pools.md).

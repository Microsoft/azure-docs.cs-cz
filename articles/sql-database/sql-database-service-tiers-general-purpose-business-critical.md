---
title: Azure SQL Database – obecný účel a důležité pro podnikání | Microsoft Docs
description: Článek popisuje úrovně služeb pro obecné účely a důležité pro firmy v rámci nákupního modelu založeného na vCore.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 02/23/2019
ms.openlocfilehash: decb4428321d5083d6ba7af134e223eb2fa5a912
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566711"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL Database úrovní služeb

Azure SQL Database vychází z SQL Server architektury databázového stroje, která je upravena pro cloudové prostředí, aby se zajistila 99,99% dostupnost, i když dojde k selhání infrastruktury. V Azure SQL Database se používají tři úrovně služeb, z nichž každý má jiný model architektury. Tyto úrovně služeb jsou:

- [Obecné účely](sql-database-service-tier-general-purpose.md), který je určený pro většinu obecných úloh.
- [Důležité pro podnikání](sql-database-service-tier-business-critical.md), který je určený pro úlohy s nízkou latencí s jednou čitelnou replikou.
- [Škálovatelné](sql-database-service-tier-hyperscale.md), navržené pro velmi velké databáze (až 100 TB) s několika replikami, které lze číst.

Tento článek popisuje požadavky na úložiště a zálohování pro úrovně služeb pro obecné účely a důležité obchodní informace v rámci nákupního modelu založeného na vCore.

> [!NOTE]
> Informace o úrovni služby vCore v nákupním modelu založeném na najdete v tématu [úroveň služby pro škálování](sql-database-service-tier-hyperscale.md)na více úrovních. Porovnání nákupního modelu založeného na DTU v vCore s nákupním modelem založeným na DTU najdete v tématu [Azure SQL Database nákupu modelů a prostředků](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Data a úložiště protokolů

Velikost úložiště využitého pro data a soubory protokolu ovlivňují následující faktory:

- Přidělené úložiště používají datové soubory (MDF) a soubory protokolu (LDF).
- Velikost výpočetní velikosti každé databáze podporuje maximální velikost databáze s výchozí maximální velikostí 32 GB.
- Když nakonfigurujete požadovanou velikost izolované databáze (velikost souboru MDF), k podpoře souborů LDF se automaticky přidá ještě 30% dalšího úložiště.
- Velikost úložiště pro SQL Database spravovanou instanci musí být zadaná v násobcích 32 GB.
- Můžete vybrat libovolnou velikost databáze mezi 10 GB a podporovaným maximem.
  - Pro úložiště v úrovních služby Standard nebo pro obecné účely zvyšte nebo zmenšete velikost v přírůstcích po 10 GB.
  - V případě úložiště v úrovních služeb Premium nebo pro důležité obchodní hodnoty zvyšte nebo zmenšete velikost v 250 až GB.
- Na úrovni `tempdb` služby pro obecné účely se používá připojená jednotka SSD a náklady na úložiště jsou zahrnuté do vCoreové ceny.
- Na úrovni `tempdb` služby důležité pro podnikání sdílí připojenou jednotku SSD se soubory MDF a LDF `tempdb` a náklady na úložiště jsou zahrnuté v ceně Vcore.

> [!IMPORTANT]
> Účtuje se vám celkové přidělené úložiště pro soubory MDF a LDF.

Chcete-li monitorovat aktuální celkovou velikost souborů MDF a LDF, použijte [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Chcete-li monitorovat aktuální velikost jednotlivých souborů MDF a LDF, použijte [Sys. database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Za určitých okolností budete muset zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [spravovat místo souborů ve službě Azure SQL Database](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Zálohy a úložiště

Úložiště pro zálohy databáze je přiděleno pro podporu obnovení PITR (Point-in-time) a [dlouhodobého uchování (LTR)](sql-database-long-term-retention.md) SQL Database. Toto úložiště se přiděluje samostatně pro každou databázi a účtuje se jako dvě samostatné poplatky za databázi.

- **PITR**: Jednotlivé zálohy databáze se zkopírují do [úložiště s přístupem pro čtení geograficky redundantního úložiště (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md) . Velikost úložiště se dynamicky zvětšuje při vytváření nových záloh. Úložiště používá týdenní úplné zálohování, denní rozdílové zálohy a zálohy transakčních protokolů, které se zkopírují každých 5 minut. Spotřeba úložiště závisí na četnosti změn databáze a na době uchovávání záloh. Pro každou databázi můžete nastavit samostatné období uchování mezi 7 a 35 dny. Minimální velikost úložiště rovnající se 100% (1x) velikosti databáze se poskytuje bez dalších poplatků. U většiny databází je tato velikost dostačující pro ukládání 7 dnů zálohování.
- **LTR**: SQL Database nabízí možnost konfigurace dlouhodobého uchovávání úplných záloh po dobu až 10 let. Pokud nastavíte zásadu LTR, budou se tyto zálohy ukládat do úložiště RA-GRS automaticky, ale můžete určit, jak často se mají zálohy kopírovat. Pro splnění různých požadavků na dodržování předpisů můžete pro týdenní, měsíční nebo roční zálohy vybrat jiné doby uchování. Konfigurace, kterou zvolíte, určuje, kolik úložiště se bude používat pro zálohy LTR. K odhadu nákladů na úložiště LTR můžete použít cenovou kalkulačku LTR. Další informace najdete v tématu [SQL Database dlouhodobé uchovávání](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Další postup

- Podrobnosti o specifických velikostech a velikostech úložiště, které jsou dostupné pro izolovanou databázi, najdete v tématu [SQL Database omezení prostředků na základě Vcore pro jednotlivé databáze](sql-database-vcore-resource-limits-single-databases.md).
- Podrobnosti o specifických velikostech a velikostech úložiště, které jsou dostupné pro elastické fondy, najdete v tématu [SQL Database omezení prostředků na základě Vcore pro elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md).

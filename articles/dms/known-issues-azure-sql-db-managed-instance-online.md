---
title: Známé problémy a omezení s online migrací do spravované instance Azure SQL Database
description: Přečtěte si o známých problémech nebo omezeních migrace spojených s online migrací do spravované instance Azure SQL Database.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 88e2b5894686ee93caecf33e04940803eb75f394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648661"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database-managed-instance"></a>Známé problémy/omezení migrace s online migrací do spravované instance Azure SQL Database

Známé problémy a omezení, které jsou spojeny s migracemi online z SQL Serveru do azure SQL database spravované instance jsou popsány níže.

> [!IMPORTANT]
> Při online migraci SQL Serveru do Azure SQL Database není migrace SQL_variant datových typů podporována.

## <a name="backup-requirements"></a>Požadavky na zálohování

- **Zálohy s kontrolním součtem**

    Služba migrace databáze Azure používá metodu zálohování a obnovení k migraci místních databází do spravované instance SQL Database. Služba Migrace databáze Azure podporuje jenom zálohy vytvořené pomocí kontrolního součtu.

    [Povolení nebo zakázání kontrolních součtů zálohování během zálohování nebo obnovení (SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017)

    > [!NOTE]
    > Pokud budete mít zálohy databáze s kompresí, kontrolní součet je výchozí chování, pokud není explicitně zakázáno.

    Pokud zvolíte možnost Azure Database Migration Service, pokud zvolíte **službu Migrace databáze Azure,** bude služba Migrace databáze Azure převzít zálohu databáze s povolenou možností kontrolního součtu.

- **Záložní médium**

    Ujistěte se, že jste vzali každou zálohu na samostatném záložním médiu (záložnísoubory). Služba Migrace databáze Azure nepodporuje zálohy, které jsou připojeny k jednomu záložnímu souboru. Vezměte úplnou zálohu a protokolujte zálohy do samostatných záložních souborů.

## <a name="data-and-log-file-layout"></a>Rozložení dat a souborů protokolu

- **Počet souborů protokolu**

    Služba Migrace databáze Azure nepodporuje databáze s více soubory protokolu. Pokud máte více souborů protokolu, zmenšit a reorganizovat do jednoho souboru protokolu transakcí. Vzhledem k tomu, že nelze vzdáleně protokolovat soubory, které nejsou prázdné, je třeba nejprve zálohovat soubor protokolu.

## <a name="sql-server-features"></a>Funkce serveru SQL Server

- **FileStream/FileTables**

    Instance spravované databází SQL Database aktuálně nepodporuje FileStream a FileTables. Pro úlohy závislé na těchto funkcích doporučujeme zvolit sql servery spuštěné na virtuálních počítačích Azure jako cíl Azure.

- **Tabulky v paměti**

    Oltp v paměti je k dispozici v vrstvách Premium a Business Critical pro spravovanou instanci SQL Database; úroveň Obecné účely nepodporuje OLTP v paměti.

## <a name="migration-resets"></a>Migrace se resetuje

- **Nasazení**

    Instance spravované službou SQL Database je služba PaaS s automatickými opravami a aktualizacemi verzí. Během migrace instance spravované službou SQL Database jsou nedůležité aktualizace pomoci až 36 hodin. Poté (a pro důležité aktualizace), pokud je migrace přerušena, proces se obnoví do stavu úplnéobnovení.

    Přechod migrace cutover lze volat pouze po obnovení úplné zálohy a dožení všechny zálohy protokolu. Pokud jsou ovlivněny vaše přechody migrace výroby jsou ovlivněny, obraťte se na [alias Azure DMS Feedback](mailto:dmsfeedback@microsoft.com).

---
title: Známé problémy a omezení pro online migrace Azure SQL Database spravované instance
description: Přečtěte si o známých problémech nebo omezeních migrace souvisejících s online migracemi pro Azure SQL Database spravované instance.
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
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77648661"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database-managed-instance"></a>Známé problémy/omezení migrace pro online migrace Azure SQL Database spravované instance

Známé problémy a omezení související s online migracemi z SQL Server do Azure SQL Database spravované instance jsou popsány níže.

> [!IMPORTANT]
> S online migracemi SQL Server Azure SQL Database není podporována migrace datových typů SQL_variant.

## <a name="backup-requirements"></a>Požadavky na zálohování

- **Zálohování pomocí kontrolního součtu**

    Azure Database Migration Service používá metodu zálohování a obnovení k migraci místních databází do SQL Database spravované instance. Azure Database Migration Service podporuje jenom zálohy vytvořené pomocí kontrolního součtu.

    [Povolit nebo zakázat kontrolní součet zálohování během zálohování nebo obnovení (SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017)

    > [!NOTE]
    > Pokud převezmete zálohy databáze s kompresí, je kontrolní součet výchozím chováním, pokud není explicitně zakázán.

    Pokud se při offline migraci rozhodnete **Azure Database Migration Service...** , pak Azure Database Migration Service převezme zálohu databáze s povolenou možností kontrolního součtu.

- **Zálohovací médium**

    Nezapomeňte provést každou zálohu na samostatném záložním médiu (záložní soubory). Azure Database Migration Service nepodporuje zálohy, které se připojují k jedinému záložnímu souboru. Proveďte úplné zálohování a zálohování protokolů, které oddělují záložní soubory.

## <a name="data-and-log-file-layout"></a>Rozložení souborů dat a protokolů

- **Počet souborů protokolu**

    Azure Database Migration Service nepodporuje databáze s více soubory protokolu. Pokud máte více souborů protokolu, zmenšete a uspořádejte je do jednoho souboru protokolu transakcí. Vzhledem k tomu, že nemůžete vzdáleně zaprotokolovat soubory, které nejsou prázdné, je nutné nejprve zálohovat soubor protokolu.

## <a name="sql-server-features"></a>SQL Server funkce

- **FileStream/tabulky**

    SQL Database spravovaná instance v současné době nepodporuje FileStream a tabulky typu. Pro úlohy, které jsou na těchto funkcích závislé, doporučujeme, abyste pro SQL servery běžící na virtuálních počítačích Azure jako cíl Azure zavedli výslovný souhlas.

- **Tabulky v paměti**

    OLTP v paměti je k dispozici v úrovních Premium a Pro důležité obchodní informace pro SQL Database Managed instance; Pro obecné účely vrstva nepodporuje OLTP v paměti.

## <a name="migration-resets"></a>Obnovení migrace

- **Nasazení**

    SQL Database Managed instance je služba PaaS s automatickými opravami a aktualizacemi verzí. Během migrace spravované instance SQL Database se Nekritická aktualizace může zvýšit až na 36 hodin. Pokud dojde k přerušení migrace a následně (a pro důležité aktualizace), proces se obnoví do stavu úplného obnovení.

    Přímou migraci migrace lze volat pouze po obnovení úplného zálohování a zachycení se všemi zálohami protokolu. Pokud se to týká cutoversí produkční migrace, obraťte se na [alias zpětné vazby Azure DMS](mailto:dmsfeedback@microsoft.com).

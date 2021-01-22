---
title: Známé problémy a omezení pro online migrace do spravované instance Azure SQL
description: Přečtěte si o známých problémech nebo omezeních migrace spojených s online migracemi do spravované instance Azure SQL.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 46c5f5995c7a1d4eb074f6c1b25ecaad7e2da37e
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98695525"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-managed-instance"></a>Známé problémy/omezení migrace pro online migrace do spravované instance Azure SQL

Známé problémy a omezení související s online migracemi z SQL Server do spravované instance Azure SQL jsou popsány níže.

> [!IMPORTANT]
> S online migracemi SQL Server Azure SQL Database není podporována migrace datových typů SQL_variant.

## <a name="backup-requirements"></a>Požadavky na zálohování

- **Zálohování pomocí kontrolního součtu**

    Azure Database Migration Service používá metodu zálohování a obnovení k migraci místních databází do spravované instance SQL. Azure Database Migration Service podporuje jenom zálohy vytvořené pomocí kontrolního součtu.

    [Povolí nebo zakáže kontrolní součet zálohování během zálohování nebo obnovení (SQL Server)](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server).

    > [!NOTE]
    > Pokud převezmete zálohy databáze s kompresí, je kontrolní součet výchozím chováním, pokud není explicitně zakázán.

    Pokud se při offline migraci rozhodnete **Azure Database Migration Service...**, pak Azure Database Migration Service převezme zálohu databáze s povolenou možností kontrolního součtu.

- **Zálohovací médium**

    Nezapomeňte provést každou zálohu na samostatném záložním médiu (záložní soubory). Azure Database Migration Service nepodporuje zálohy, které se připojují k jedinému záložnímu souboru. Proveďte úplné zálohování a zálohování protokolů, které oddělují záložní soubory.

## <a name="data-and-log-file-layout"></a>Rozložení souborů dat a protokolů

- **Počet souborů protokolu**

    Azure Database Migration Service nepodporuje databáze s více soubory protokolu. Pokud máte více souborů protokolu, zmenšete a uspořádejte je do jednoho souboru protokolu transakcí. Vzhledem k tomu, že nemůžete vzdáleně zaprotokolovat soubory, které nejsou prázdné, je nutné nejprve zálohovat soubor protokolu.

## <a name="sql-server-features"></a>SQL Server funkce

- **FileStream/tabulky**

    Spravovaná instance SQL aktuálně nepodporuje FileStream a tabulky typu. Pro úlohy, které jsou na těchto funkcích závislé, doporučujeme, abyste pro SQL servery běžící na virtuálních počítačích Azure jako cíl Azure zavedli výslovný souhlas.

- **Tabulky v paměti**

    OLTP v paměti je k dispozici v úrovních Premium a Pro důležité obchodní informace pro spravovanou instanci SQL. Pro obecné účely vrstva nepodporuje OLTP v paměti.

## <a name="migration-resets"></a>Obnovení migrace

- **Nasazení**

    SQL Managed instance je služba PaaS s automatickými opravami a aktualizacemi verzí. Během migrace spravované instance SQL se uchovávají nedůležité aktualizace po dobu až 36 hodin. Pokud dojde k přerušení migrace a následně (a pro důležité aktualizace), proces se obnoví do stavu úplného obnovení.

    Přímou migraci migrace lze volat pouze po obnovení úplného zálohování a zachycení se všemi zálohami protokolu. Pokud se to týká cutoversí produkční migrace, obraťte se na [alias zpětné vazby Azure DMS](mailto:dmsfeedback@microsoft.com).

## <a name="smb-file-share-connectivity"></a>Připojení sdílení souborů SMB

Problémy s připojením ke sdílené složce SMB jsou pravděpodobně způsobeny problémem s oprávněním. 

Pokud chcete otestovat připojení sdílené složky SMB, postupujte takto: 

1. Uložte zálohu do sdílené složky SMB. 
1. Ověřte připojení k síti mezi podsítí Azure Database Migration Service a zdrojovým SQL Server. Nejjednodušší způsob, jak to provést, je nasadit virtuální počítač s SQL Server do podsítě DMS a připojit se ke zdrojovému SQL Server pomocí SQL Server Management Studio. 
1. Obnovte hlavičku ve zdrojovém SQL Server ze zálohy ve sdílené složce: 

   ```sql
   RESTORE HEADERONLY   
   FROM DISK = N'\\<SMB file share path>\full.bak'
   ```

Pokud se nemůžete připojit ke sdílené složce, nakonfigurujte oprávnění pomocí těchto kroků: 

1. Přejděte do sdílené složky pomocí Průzkumníka souborů. 
1. Klikněte pravým tlačítkem na sdílenou složku a vyberte vlastnosti. 
1. Zvolte kartu **sdílení** a vyberte **Rozšířené sdílení**. 
1. Přidejte účet systému Windows, který se používá pro migraci, a přiřaďte mu přístup pro úplné řízení. 
1. Přidejte účet služby SQL Server a přiřaďte mu přístup pro úplné řízení. Pokud si nejste jistí, který účet se používá, zaškrtněte **Configuration Manager SQL Server** pro účet služby SQL Server. 

   :::image type="content" source="media/known-issues-azure-sql-db-managed-instance-online/assign-fileshare-permissions.png" alt-text="Poskytněte úplný přístup k účtům systému Windows, které se používají k migraci, a pro účet služby SQL Server. ":::


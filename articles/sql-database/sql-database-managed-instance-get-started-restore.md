---
title: Obnovit zálohu do spravované instance
description: Zjistěte, jak obnovit zálohu databáze do Azure SQL Database Managed Instance pomocí SSMS.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab, bonova
ms.date: 12/14/2018
ms.openlocfilehash: 7b7d635a45929ead1ef67e10fef75e502c5005f7
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83770087"
---
# <a name="quickstart-restore-a-database-to-a-managed-instance"></a>Rychlý Start: obnovení databáze do spravované instance

V tomto rychlém startu použijete SQL Server Management Studio (SSMS) k obnovení databáze (World World Importers – Standard Backup) z úložiště objektů BLOB v Azure do [spravované Instance](sql-database-managed-instance.md)Azure SQL Database.

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

> [!NOTE]
> Další informace o migraci pomocí Azure Database Migration Service (DMS) najdete v tématu [migrace spravované instance pomocí DMS](../dms/tutorial-sql-server-to-managed-instance.md).
> Další informace o různých metodách migrace najdete v tématu [migrace instance SQL Server do Azure SQL Database spravované instance](sql-database-managed-instance-migrate.md).

## <a name="prerequisites"></a>Požadavky

Tento rychlý start:

- Používá prostředky z rychlého startu [Vytvoření spravované instance](sql-database-managed-instance-get-started.md) .
- Vyžaduje, aby byl v počítači nainstalován nejnovější [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) .
- Vyžaduje použití SSMS k připojení ke spravované instanci. V těchto rychlých startech se můžete podívat, jak se připojit:
  - [Povolit veřejný koncový bod](sql-database-managed-instance-public-endpoint-configure.md) na spravované instanci – jedná se o doporučený postup pro tento kurz.
  - [Připojení ke spravované instanci Azure SQL Database z virtuálního počítače Azure](sql-database-managed-instance-configure-vm.md)
  - [Nakonfigurujte připojení typu Point-to-site k Azure SQL Database spravované instanci z místního prostředí](sql-database-managed-instance-configure-p2s.md).

> [!NOTE]
> Další informace o zálohování a obnovení databáze SQL Server pomocí služby Azure Blob Storage a [klíče sdíleného přístupového podpisu (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)najdete v tématu [SQL Server zálohování na adresu URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017).

## <a name="restore-the-database-from-a-backup-file"></a>Obnovení databáze ze záložního souboru

V SSMS postupujte podle těchto kroků a obnovte databázi World Importers do spravované instance. Záložní soubor databáze je uložený v předem nakonfigurovaném účtu služby Azure Blob Storage.

1. Otevřete SSMS a připojte se ke svojí spravované instanci.
2. V nabídce na levé straně klikněte pravým tlačítkem na spravovanou instanci a vyberte **Nový dotaz** . otevře se nové okno dotazu.
3. Spusťte následující skript SQL, který pomocí předem nakonfigurovaného účtu úložiště a klíče SAS [vytvoří přihlašovací údaje](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) ve spravované instanci.

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```

    ![vytvoření přihlašovacích údajů](./media/sql-database-managed-instance-get-started-restore/credential.png)

4. Pokud chcete ověřit přihlašovací údaje, spusťte následující skript, který pomocí adresy URL [kontejneru](https://azure.microsoft.com/services/container-instances/) načte seznam záložních souborů.

   ```sql
   RESTORE FILELISTONLY FROM URL =
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![seznam souborů](./media/sql-database-managed-instance-get-started-restore/file-list.png)

5. Spusťte následující skript pro obnovení databáze World Importers.

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![Obnovení](./media/sql-database-managed-instance-get-started-restore/restore.png)

6. Spuštěním následujícího skriptu Sledujte stav obnovení.

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time
   FROM sys.dm_exec_requests r
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')
   ```

7. Až se obnovení dokončí, zobrazte databázi v Průzkumník objektů. Pomocí zobrazení [Sys. dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) můžete ověřit, že se obnovení databáze dokončilo.

> [!NOTE]
> Operace obnovení databáze je asynchronní a vyvolaly. Pokud dojde k přerušení připojení nebo k vypršení časového limitu, může dojít SQL Server Management Studio k chybě. Azure SQL Database se nadále snaží obnovit databázi na pozadí a průběh obnovy můžete sledovat pomocí zobrazení [Sys. dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) a [Sys. dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) .
> V některých fázích procesu obnovení se v systémových zobrazeních zobrazí jedinečný identifikátor místo skutečného názvu databáze. Další informace o `RESTORE` rozdílech v chování příkazů [najdete tady](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#restore-statement).

## <a name="next-steps"></a>Další kroky

- Řešení potíží se zálohováním na adresu URL najdete v tématu [SQL Server služby Backup na adresu URL osvědčené postupy a řešení potíží](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting).
- Přehled možností připojení aplikace najdete v tématu [připojení aplikací ke spravované instanci](sql-database-managed-instance-connect-app.md).
- Dotazování pomocí oblíbených nástrojů nebo jazyků najdete v tématu [rychlý Start: Azure SQL Database připojení a dotazování](sql-database-connect-query.md).

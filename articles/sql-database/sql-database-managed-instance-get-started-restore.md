---
title: Obnovení zálohy na spravovanou instanci
description: Zjistěte, jak obnovit zálohu databáze do Azure SQL Database Managed Instance pomocí SSMS.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab, bonova
ms.date: 12/14/2018
ms.openlocfilehash: 3b12aa860bee189f706bd98df63fc194a3a0cc71
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874700"
---
# <a name="quickstart-restore-a-database-to-a-managed-instance"></a>Úvodní příručka: Obnovení databáze do spravované instance

V tomto rychlém startu použijete SQL Server Management Studio (SSMS) k obnovení databáze (wide world importéři – standardní záložní soubor) z úložiště objektů Blob Azure do [spravované instance](sql-database-managed-instance.md)Azure SQL Database .

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

> [!NOTE]
> Další informace o migraci pomocí služby Azure Database Migration Service (DMS) najdete v [tématu Migrace spravované instance pomocí Služby DMS](../dms/tutorial-sql-server-to-managed-instance.md).
> Další informace o různých metodách migrace najdete v [tématu migrace instance SQL Serveru do spravované instance Azure SQL Database .](sql-database-managed-instance-migrate.md)

## <a name="prerequisites"></a>Požadavky

Tento rychlý start:

- Používá prostředky z rychlého startu [Vytvořit spravovanou instanci.](sql-database-managed-instance-get-started.md)
- Vyžaduje, aby váš počítač měl nainstalovanou nejnovější aplikaci [SQL Server Management Studio.](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)
- Vyžaduje použití SSMS pro připojení ke spravované instanci. Podívejte se na tyto rychlé starty o připojení:
  - [Povolit veřejný koncový bod](sql-database-managed-instance-public-endpoint-configure.md) na spravované instanci – to je doporučený přístup pro tento kurz.
  - [Připojení ke spravované instanci Azure SQL Database z virtuálního počítače Azure](sql-database-managed-instance-configure-vm.md)
  - [Nakonfigurujte připojení z místního](sql-database-managed-instance-configure-p2s.md)prostředí ke službě Azure SQL Database Managed Instance .

> [!NOTE]
> Další informace o zálohování a obnovení databáze serveru SQL Server pomocí úložiště objektů Blob Azure a [klíče s sdíleným přístupovým podpisem (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)naleznete v tématu [Zálohování serveru SQL Server na adresu URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017).

## <a name="restore-the-database-from-a-backup-file"></a>Obnovení databáze ze záložního souboru

V SSMS postupujte takto obnovit databázi Wide World Importers do spravované instance. Záložní soubor databáze je uložen v předem nakonfigurovaném účtu úložiště objektů Blob Azure.

1. Otevřete SSMS a připojte se ke spravované instanci.
2. V levé nabídce klikněte pravým tlačítkem myši na spravovanou instanci a výběrem **možnosti Nový dotaz** otevřete nové okno dotazu.
3. Spusťte následující skript SQL, který používá předem nakonfigurovaný účet úložiště a klíč SAS k [vytvoření pověření](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) ve spravované instanci.

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```

    ![vytvoření přihlašovacích údajů](./media/sql-database-managed-instance-get-started-restore/credential.png)

4. Chcete-li zkontrolovat pověření, spusťte následující skript, který používá adresu URL [kontejneru](https://azure.microsoft.com/services/container-instances/) k získání seznamu záložních souborů.

   ```sql
   RESTORE FILELISTONLY FROM URL =
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![seznam souborů](./media/sql-database-managed-instance-get-started-restore/file-list.png)

5. Spusťte následující skript a obnovte databázi Wide World Importers.

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![Obnovení](./media/sql-database-managed-instance-get-started-restore/restore.png)

6. Spusťte následující skript pro sledování stavu obnovení.

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time
   FROM sys.dm_exec_requests r
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')
   ```

7. Po dokončení obnovení zobrazte databázi v Průzkumníku objektů. Obnovení databáze můžete ověřit pomocí zobrazení [sys.dm_operation_status.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)

> [!NOTE]
> Operace obnovení databáze je asynchronní a retriable. Může se stát, že dojde k chybě, pokud dojde k přerušení připojení nebo vypršení časového limitu, dojde k chybě sql server management studio. Azure SQL Database se bude dál snažit obnovit databázi na pozadí a můžete sledovat průběh obnovení pomocí zobrazení [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) a [sys.dm_operation_status.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)
> V některých fázích procesu obnovení se v systémových zobrazeních zobrazí jedinečný identifikátor namísto skutečného názvu databáze. Další `RESTORE` informace o rozdílech chování prohlášení [zde](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#restore-statement).

## <a name="next-steps"></a>Další kroky

- Informace o řešení potíží se zálohováním na adresu URL naleznete v tématu [SQL Server Backup to URL Best Practices and Troubleshooting](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting).
- Přehled možností připojení k aplikacím najdete v tématu [Připojení aplikací ke spravované instanci](sql-database-managed-instance-connect-app.md).
- Informace o dotazování pomocí oblíbených nástrojů nebo jazyků najdete [v tématu Rychlé starty: Azure SQL Database Connect a Query](sql-database-connect-query.md).

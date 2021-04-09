---
title: 'Rychlý Start: obnovení zálohy (SSMS)'
titleSuffix: Azure SQL Managed Instance
description: V tomto rychlém startu se dozvíte, jak obnovit zálohu databáze do spravované instance Azure SQL pomocí SQL Server Management Studio (SSMS).
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 12/14/2018
ms.openlocfilehash: 15aead5c9f93205baac28e8b93a88015c9689e3a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625309"
---
# <a name="quickstart-restore-a-database-to-azure-sql-managed-instance-with-ssms"></a>Rychlý Start: obnovení databáze do spravované instance Azure SQL pomocí SSMS
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

V tomto rychlém startu použijete SQL Server Management Studio (SSMS) k obnovení databáze (World World Importers – Standard Backup) z úložiště objektů BLOB v Azure do [spravované instance Azure SQL](sql-managed-instance-paas-overview.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

> [!NOTE]
> Další informace o migraci pomocí Azure Database Migration Service najdete v tématu [migrace spravované instance SQL pomocí Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).
> Další informace o různých metodách migrace najdete v tématu [SQL Server migrace do spravované instance Azure SQL](migrate-to-instance-from-sql-server.md).

## <a name="prerequisites"></a>Požadavky

Tento rychlý start:

- Používá prostředky z rychlého startu [Vytvoření spravované instance](instance-create-quickstart.md) .
- Vyžaduje, aby byla nainstalovaná nejnovější verze [SSMS](/sql/ssms/sql-server-management-studio-ssms) .
- Vyžaduje použití SSMS k připojení ke spravované instanci SQL. V těchto rychlých startech se můžete podívat, jak se připojit:
  - [Povolení veřejného koncového bodu](public-endpoint-configure.md) na spravované instanci SQL – jedná se o doporučený postup pro tento kurz.
  - [Připojte se ke spravované instanci SQL z virtuálního počítače Azure](connect-vm-instance-configure.md).
  - [Nakonfigurujte připojení typu Point-to-site k spravované instanci SQL z místního](point-to-site-p2s-configure.md)prostředí.

> [!NOTE]
> Další informace o zálohování a obnovení databáze SQL Server pomocí služby Azure Blob Storage a [klíče sdíleného přístupového podpisu (SAS)](../../storage/common/storage-sas-overview.md)najdete v tématu [SQL Server zálohování na adresu URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url).

## <a name="restore-from-a-backup-file"></a>Obnovení ze záložního souboru

V SQL Server Management Studio postupujte podle těchto kroků a obnovte databázi World Importers do spravované instance SQL. Záložní soubor databáze je uložený v předem nakonfigurovaném účtu služby Azure Blob Storage.

1. Otevřete SSMS a připojte se ke svojí spravované instanci.
2. V **Průzkumník objektů** klikněte pravým tlačítkem na spravovanou instanci a vyberte **Nový dotaz** . otevře se nové okno dotazu.
3. Spusťte následující skript SQL, který pomocí předem nakonfigurovaného účtu úložiště a klíče SAS [vytvoří přihlašovací údaje](/sql/t-sql/statements/create-credential-transact-sql) ve spravované instanci.
 
   > [!IMPORTANT]
   > `CREDENTIAL` musí odpovídat cestě kontejneru, začínat znakem `https` a nesmí obsahovat koncové lomítko. `IDENTITY` musí být `SHARED ACCESS SIGNATURE` . `SECRET` musí se jednat o token sdíleného přístupového podpisu a nemůže obsahovat přední `?` .

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```

    ![vytvoření přihlašovacích údajů](./media/restore-sample-database-quickstart/credential.png)

4. Pokud chcete ověřit přihlašovací údaje, spusťte následující skript, který pomocí adresy URL [kontejneru](https://azure.microsoft.com/services/container-instances/) načte seznam záložních souborů.

   ```sql
   RESTORE FILELISTONLY FROM URL =
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![seznam souborů](./media/restore-sample-database-quickstart/file-list.png)

5. Spusťte následující skript pro obnovení databáze World Importers.

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![Snímek obrazovky zobrazuje skript běžící v Průzkumník objektů se zprávou o úspěchu.](./media/restore-sample-database-quickstart/restore.png)

6. Spuštěním následujícího skriptu Sledujte stav obnovení.

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time
   FROM sys.dm_exec_requests r
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')
   ```

7. Až se obnovení dokončí, zobrazte databázi v Průzkumník objektů. Dokončení obnovení databáze můžete ověřit pomocí zobrazení [Sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) .

> [!NOTE]
> Operace obnovení databáze je asynchronní a je opakovaná. Při přerušení připojení nebo vypršení časového limitu může dojít k chybě v SQL Server Management Studio. Azure SQL Database se nadále snaží obnovit databázi na pozadí a průběh obnovy můžete sledovat pomocí zobrazení [Sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) a [Sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) .
> V některých fázích procesu obnovení se místo skutečného názvu databáze v systémových zobrazeních zobrazí jedinečný identifikátor. Další informace o `RESTORE` rozdílech v chování příkazů [najdete tady](./transact-sql-tsql-differences-sql-server.md#restore-statement).

## <a name="next-steps"></a>Další kroky

- Pokud v kroku 5 dojde k ukončení obnovení databáze s ID zprávy 22003, vytvořte nový záložní soubor obsahující kontrolní součet zálohy a znovu proveďte obnovení. Viz [Povolení nebo zakázání kontrolního součtu zálohování během zálohování nebo obnovení](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server).
- Řešení potíží se zálohováním na adresu URL najdete v tématu [SQL Server služby Backup na adresu URL osvědčené postupy a řešení potíží](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting).
- Přehled možností připojení aplikace najdete v tématu [připojení aplikací k spravované instanci SQL](connect-application-instance.md).
- Dotazování pomocí oblíbených nástrojů nebo jazyků najdete v tématu [rychlý Start: Azure SQL Database připojení a dotazování](../database/connect-query-content-reference-guide.md).

---
title: Obnovení zálohy do Azure SQL Database Managed Instance | Microsoft Docs
description: Zjistěte, jak obnovit zálohu databáze do Azure SQL Database Managed Instance pomocí SSMS.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab, bonova
manager: craigg
ms.date: 12/14/2018
ms.openlocfilehash: 9459aa9df74efa500109d7c281daad28355bd0d0
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55895722"
---
# <a name="quickstart-restore-a-database-to-a-managed-instance"></a>Rychlý start: Obnovit databázi do Managed Instance 

V tomto rychlém startu použijete SQL Server Management Studio (SSMS) k obnovení databáze (Wide World Importers – Standard záložní soubor) z úložiště objektů Blob v Azure do Azure SQL Database [Managed Instance](sql-database-managed-instance.md). 

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

> [!NOTE]
> - Další informace o migraci pomocí Azure Database Migration Service (DMS) najdete v tématu [migraci Managed Instance pomocí DMS](../dms/tutorial-sql-server-to-managed-instance.md). 
> - Další informace o různých metodách migrace najdete v tématu [migrace instance SQL serveru do Azure SQL Database Managed Instance](sql-database-managed-instance-migrate.md).

## <a name="prerequisites"></a>Požadavky

Tento rychlý start:

- Používá prostředky z [vytvoříte Managed Instance](sql-database-managed-instance-get-started.md) rychlý start.
- Počítač vyžaduje nejnovější [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) nainstalované.
- Vyžaduje použití SSMS k připojení k Managed Instance. O tom, jak připojit najdete v těchto rychlých startů:
  - [Připojení ke spravované instanci Azure SQL Database z virtuálního počítače Azure](sql-database-managed-instance-configure-vm.md)
  - [Konfigurace připojení typu point-to-site k Azure SQL Database Managed Instance z místní](sql-database-managed-instance-configure-p2s.md).

> [!NOTE]
> Další informace o zálohování a obnovení serveru SQL database s využitím úložiště objektů Blob v Azure a [klíče sdíleného přístupového podpisu (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1), naleznete v tématu [zálohování SQL serveru na adresu URL](sql-database-managed-instance-get-started-restore.md).

## <a name="restore-the-database-from-a-backup-file"></a>Obnovte databázi ze zálohy

V aplikaci SSMS postupujte podle těchto kroků k obnovení databáze Wide World Importers k Managed Instance. Záložní soubor databáze je uložen v předem nakonfigurovaný účet úložiště objektů Blob v Azure.

1. Otevřete SMSS a připojte se k Managed Instance.
2. Z nabídky na levé straně, klikněte pravým tlačítkem na Managed Instance a vyberte **nový dotaz** otevřete nové okno dotazu.
3. Spusťte následující skript SQL, která používá předem nakonfigurovaný účet úložiště a klíč SAS pro [vytvoření přihlašovacích údajů](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql?view=sql-server-2017) v Managed Instance.

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases] 
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D' 
   ```

    ![vytvoření přihlašovacích údajů](./media/sql-database-managed-instance-get-started-restore/credential.png)

4. Pokud chcete zkontrolovat svoje přihlašovací údaje, spusťte následující skript, který používá [kontejneru](https://azure.microsoft.com/services/container-instances/) adresu URL pro načtení seznamu záložního souboru.

   ```sql
   RESTORE FILELISTONLY FROM URL = 
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![seznam souborů](./media/sql-database-managed-instance-get-started-restore/file-list.png)

5. Spusťte následující skript k obnovení databáze Wide World Importers.

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![Obnovení](./media/sql-database-managed-instance-get-started-restore/restore.png)

6. Spusťte následující skript, který sledovat stav vašich obnovení.

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
   FROM sys.dm_exec_requests r 
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')
   ```

7. Po dokončení obnovení zobrazte databázi v Průzkumníku objektů. 

## <a name="next-steps"></a>Další postup

- Řešení potíží s zálohování na adresu URL, najdete v části [SQL Server zálohování na adresu URL osvědčené postupy a řešení potíží](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting).
- Přehled možností připojení aplikace, najdete v části [připojení aplikací k Managed Instance](sql-database-managed-instance-connect-app.md).
- Provádět dotazy pomocí oblíbených nástrojů nebo jazyků, naleznete v tématu [rychlých startů: Azure SQL Database připojení a dotazování](sql-database-connect-query.md).

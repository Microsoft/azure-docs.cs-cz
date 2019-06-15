---
title: Konfigurace replikace databáze spravované instance Azure SQL Database | Dokumentace Microsoftu
description: Další informace o konfiguraci transakční replikace databáze spravované instance Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: c72c4d21f948d6d6c4d1d4598efa0e13de9705a6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64926199"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Konfigurace replikace databáze spravované instance Azure SQL Database

Transakční replikace umožňuje replikovat data do databáze spravované instance Azure SQL Database z databáze systému SQL Server nebo jiné instanci databáze. 

Transakční replikace můžete použít také pro nasdílení změn provedených v databázi instance ve spravované instanci Azure SQL Database do:

- Databáze SQL serveru.
- Izolované databáze ve službě Azure SQL Database.
- Ve fondu databáze v elastickém fondu Azure SQL Database.
 
Transakční replikace je ve verzi public preview na [Azure SQL Database managed instance](sql-database-managed-instance.md). Spravovaná instance může hostovat databáze vydavatele, distributor a odběratele. Zobrazit [transakční replikace konfigurace](sql-database-managed-instance-transactional-replication.md#common-configurations) pro dostupné konfigurace.

  > [!NOTE]
  > Tento článek je určený pro uživatele v konfiguraci replikace službou Azure Database managed instance od začátku do konce, od vytváření skupiny prostředků. Pokud již máte nasazené instance spravované, přeskočte k části [kroku 4](#4---create-a-publisher-database) vytvořit databázi vydavatele nebo [kroku 6](#6---configure-distribution) Pokud už máte databázi vydavatele a odběratele a můžete začít Konfigurace replikace.  

## <a name="requirements"></a>Požadavky

Konfigurace managed instance fungovat jako vydavatel a/nebo distributora vyžaduje:

- Spravované instance se teď neúčastní relace geografickou replikaci.
- Že vydavatel spravovaná instance je ve stejné virtuální síti jako distributor a odběrateli, nebo [partnerský vztah virtuální sítě](../virtual-network/tutorial-connect-virtual-networks-powershell.md) stal mezi virtuálními sítěmi všechny tři entity. 
- Připojení pomocí ověřování SQL mezi účastníky replikace.
- Sdílenou účtu úložiště Azure pro replikaci pracovní adresář.
- Port 445 (odchozí TCP) je otevřen v zabezpečení pravidla NSG pro spravované instance pro přístup ke sdílené složce Azure. 


 > [!NOTE]
 > Předplatitelé mohou být pouze izolované databáze a databáze ve fondu ve službě Azure SQL Database. 


## <a name="features"></a>Funkce

Podporované platformy:

- Transakční replikace a replikace snímků kombinaci místní SQL Server a spravované instance Azure SQL Database.
- Předplatitelé můžou být v místních databází SQL serveru, jediné databáze/spravované instance v Azure SQL Database nebo databáze ve fondu v elastických fondů Azure SQL Database.
- Jednosměrné nebo obousměrné replikace.

Ve službě Azure SQL Database managed instance nepodporuje následující funkce:

- [Aktualizovatelné odběry](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication).
- [Aktivní geografická replikace](sql-database-active-geo-replication.md) a [-automatické převzetí služeb při selhání skupiny](sql-database-auto-failover-group.md) neměl by se používat, pokud je nakonfigurovaný transakční replikace.
 
## <a name="1---create-a-resource-group"></a>1 – Vytvoření skupiny prostředků

Použití [webu Azure portal](https://portal.azure.com) a vytvořte skupinu prostředků s názvem `SQLMI-Repl`.  

## <a name="2---create-managed-instances"></a>2\. vytvoření spravované instance

Použití [webu Azure portal](https://portal.azure.com) vytvořit dvě [spravované instance](sql-database-managed-instance-create-tutorial-portal.md) na stejnou virtuální síť a podsíť. By měl být pojmenován dva spravované instance:

- `sql-mi-pub`
- `sql-mi-sub`

Budete také muset [konfigurace Virtuálním počítači Azure připojit](sql-database-managed-instance-configure-vm.md) k Azure SQL Database managed instance. 

## <a name="3---create-azure-storage-account"></a>3 – vytvoření účtu služby Azure Storage

[Vytvoření účtu služby Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) pro pracovní adresář a pak vytvořte [sdílené](../storage/files/storage-how-to-create-file-share.md) v rámci účtu úložiště. 

Zkopírujte cestu sdílené složky souboru ve formátu: `\\storage-account-name.file.core.windows.net\file-share-name`

Kopírování přístupových klíčů úložiště ve formátu: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

 Další informace najdete v článku [Zobrazení a kopírování přístupového klíče k úložišti](../storage/common/storage-account-manage.md#access-keys). 

## <a name="4---create-a-publisher-database"></a>4 – vytvoření databáze vydavatele

Připojte se k vaší `sql-mi-pub` managed instance pomocí SQL Server Management Studio a spusťte následující kód Transact-SQL (T-SQL) k vytvoření databáze vydavatele:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_PUB]
GO

USE [ReplTran_PUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO


USE [ReplTran_PUB]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="5---create-a-subscriber-database"></a>5 – vytvoření databáze odběratele

Připojte se k vaší `sql-mi-sub` managed instance pomocí SQL Server Management Studio a spusťte následující kód T-SQL k vytvoření databáze prázdný předplatitele:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_SUB]
GO

USE [ReplTran_SUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO
```

## <a name="6---configure-distribution"></a>6 – Konfigurace distribuce

Připojte se k vaší `sql-mi-pub` managed instance pomocí SQL Server Management Studio a spusťte následující kód T-SQL nakonfigurovat distribuční databázi. 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 – Nakonfigurujte použití distributora vydavatele 

U vašeho vydavatele spravovaná instance `sql-mi-pub`, změnit tak provedení dotazu [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) režimu a spusťte následující kód do nové distributora zaregistrovat u vašeho vydavatele. 

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"


USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)';
```

Tento skript nakonfiguruje místní vydavatele na spravované instanci přidá odkazovaný server a vytvoří sadu úloh pro SQL Server Agent. 

## <a name="8---create-publication-and-subscriber"></a>8 – vytvoření publikace a odběratele

Pomocí [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) režimu, spusťte následující skript T-SQL k povolení replikace pro vaši databázi a konfiguraci replikace mezi vydavatele, distributor a odběratele. 

```sql
-- Set variables
:setvar username sourceLogin
:setvar password sourcePassword
:setvar source_db ReplTran_PUB
:setvar publication_name PublishData
:setvar object ReplTest
:setvar schema dbo
:setvar target_server "sql-mi-sub.wdec33262scj9dr27.database.windows.net"
:setvar target_username targetLogin
:setvar target_password targetPassword
:setvar target_db ReplTran_SUB

-- Enable replication for your source database
USE [$(source_db)]
EXEC sp_replicationdboption
  @dbname = N'$(source_db)',
  @optname = N'publish',
  @value = N'true';

-- Create your publication
EXEC sp_addpublication
  @publication = N'$(publication_name)',
  @status = N'active';


-- Configure your log reaer agent
EXEC sp_changelogreader_agent
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the publication snapshot
EXEC sp_addpublication_snapshot
  @publication = N'$(publication_name)',
  @frequency_type = 1,
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the ReplTest table to the publication
EXEC sp_addarticle 
  @publication = N'$(publication_name)',
  @type = N'logbased',
  @article = N'$(object)',
  @source_object = N'$(object)',
  @source_owner = N'$(schema)';

-- Add the subscriber
EXEC sp_addsubscription
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @destination_db = N'$(target_db)',
  @subscription_type = N'Push';

-- Create the push subscription agent
EXEC sp_addpushsubscription_agent
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @subscriber_db = N'$(target_db)',
  @subscriber_security_mode = 0,
  @subscriber_login = N'$(target_username)',
  @subscriber_password = N'$(target_password)',
  @job_login = N'$(target_username)',
  @job_password = N'$(target_password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9 – upravit parametry agenta

Spravované instance Azure SQL Database aktuálně dochází k některým problémům back-end s připojením s agenty replikace. Když je tento problém se řeší řešit, alternativní řešení pro zvýšení hodnoty časového limitu přihlášení pro agenty replikace. 

Spusťte následující příkaz jazyka T-SQL ve vydavateli zvýšit časový limit přihlášení: 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Spusťte následující příkaz T-SQL znovu a nastavte časový limit přihlášení zpět na výchozí hodnotu, budete chtít udělat:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Restartujte všechny tři agenty k použití těchto změn. 

## <a name="10---test-replication"></a>10 – testování replikace

Jakmile replikace není nakonfigurovaná, takže ji můžete otestovat vkládání nových položek ve vydavateli a sledování změn rozšíří do odběratele. 

Spusťte následující fragment kódu jazyka T-SQL k zobrazení řádků na předplatitele:

```sql
select * from dbo.ReplTest
```

Spusťte následující fragment kódu jazyka T-SQL vložit dodatečné řádky ve vydavateli a potom zkontrolujte řádky znovu na odběratele. 

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li vyřadit publikaci, spusťte následující příkaz jazyka T-SQL:

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

Chcete-li odebrat možnost replikace z databáze, spusťte následující příkaz jazyka T-SQL:

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

Publikování a distribuci zakážete, spusťte následující příkaz jazyka T-SQL:

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

Můžete vyčistit prostředky Azure podle [odstranění spravované instance prostředků ze skupiny prostředků](../azure-resource-manager/manage-resources-portal.md#delete-resources) a její následné odstranění skupiny prostředků `SQLMI-Repl`. 

   
## <a name="see-also"></a>Viz také

- [Transakční replikace](sql-database-managed-instance-transactional-replication.md)
- [Co je Managed Instance?](sql-database-managed-instance.md)

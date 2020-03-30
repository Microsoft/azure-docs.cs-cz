---
title: Konfigurace replikace v databázi spravovaných instancí
description: Naučte se konfigurovat transakční replikaci mezi vydavatelem/distributorem spravované instance azure SQL Database a předplatitelem spravované instance.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 02/07/2019
ms.openlocfilehash: 9af7b471210ca3cc69428e68aef4aafaee159344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299069"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Konfigurace replikace v databázi spravovaných instancí Azure SQL Database

Transakční replikace umožňuje replikovat data do databáze spravovaných instancí Azure SQL Database z databáze serveru SQL Server nebo jiné databáze instancí. 

Tento článek ukazuje, jak nakonfigurovat replikaci mezi vydavatelem/distributorem spravované instance a předplatitelem spravované instance. 

![Replikovat mezi dvěma spravovanými instancemi](media/replication-with-sql-database-managed-instance/sqlmi-sqlmi-repl.png)

Transakční replikaci můžete také použít k nabízení změn provedených v databázi instancí v instanci spravované službou Azure SQL Database:

- Databáze serveru SQL Server.
- Jedna databáze v Azure SQL Database.
- Sdružená databáze v elastickém fondu Azure SQL Database.
 
Transakční replikace je ve verzi Public Preview na [spravované instanci Azure SQL Database](sql-database-managed-instance.md). Spravovaná instance může hostovat databáze vydavatele, distributora a odběratele. Viz [konfigurace transakční replikace](sql-database-managed-instance-transactional-replication.md#common-configurations) pro dostupné konfigurace.

  > [!NOTE]
  > - Tento článek je určen k vedení uživatele při konfiguraci replikace s instancí spravované azure databáze od začátku do konce, počínaje vytvořením skupiny prostředků. Pokud jste již nasadili spravované instance, přejděte na [krok 4](#4---create-a-publisher-database) a vytvořte databázi vydavatelů, nebo [krok 6,](#6---configure-distribution) pokud již máte databázi vydavatele a odběratele a jste připraveni začít konfigurovat replikaci.  
  > - Tento článek konfiguruje vydavatele a distributora na stejné spravované instanci. Chcete-li umístit distributorna samostatné instance manged, naleznete v kurzu [Konfigurace replikace mezi vydavatelem MI a distributorem MI](sql-database-managed-instance-configure-replication-tutorial.md). 

## <a name="requirements"></a>Požadavky

Konfigurace spravované instance tak, aby fungovala jako vydavatel nebo distributor, vyžaduje:

- Že vydavatel spravované instance je ve stejné virtuální síti jako distributor a odběratel nebo [partnerský vztah virtuální sítě](../virtual-network/tutorial-connect-virtual-networks-powershell.md) byla vytvořena mezi virtuální sítě všech tří entit. 
- Při připojování mezi účastníky replikace se používá ověřování SQL.
- Sdílená sada účtu úložiště Azure pro pracovní adresář replikace.
- Port 445 (TCP odchozí) je otevřena v pravidlech zabezpečení skupiny zabezpečení pro spravované instance pro přístup ke sdílené složce Azure.  Pokud narazíte na chybu "nepodařilo \<se připojit k názvu účtu úložiště Azure> s chybou operačního systému 53", budete muset přidat odchozí pravidlo do skupiny zabezpečení sítě příslušné podsítě spravované instance SQL.


 > [!NOTE]
 > Jednotlivé databáze a sdružené databáze v Azure SQL Database můžou být jenom předplatitelé. 


## <a name="features"></a>Funkce

Podporuje:

- Transakční a snímek replikace mix SQL Server místní a spravované instance v Azure SQL Database.
- Předplatitelé mohou být v místních databázích SQL Serveru, v jednotlivých databázích/spravovaných instancích v Azure SQL Database nebo ve fondech elastických fondů Azure SQL Database.
- Jednosměrná nebo obousměrná replikace.

Následující funkce nejsou podporovány ve spravované instanci v Azure SQL Database:

- [Aktualizovatelné odběry](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication).
- [Aktivní geografická replikace](sql-database-active-geo-replication.md) s transakční replikací. Místo aktivní geografické replikace použijte [skupiny automatického převzetí služeb při selhání](sql-database-auto-failover-group.md), ale všimněte si, že publikace musí být ručně [odstraněna](sql-database-managed-instance-transact-sql-information.md#replication) z primární spravované instance a znovu vytvořena v sekundární spravované instanci po převzetí služeb při selhání.  
 
## <a name="1---create-a-resource-group"></a>1 - Vytvoření skupiny prostředků

Pomocí [portálu Azure](https://portal.azure.com) vytvořte skupinu `SQLMI-Repl`prostředků s názvem .  

## <a name="2---create-managed-instances"></a>2 – Vytvoření spravovaných instancí

Portál [Azure](https://portal.azure.com) slouží k vytvoření dvou [spravovaných instancí](sql-database-managed-instance-create-tutorial-portal.md) ve stejné virtuální síti a podsíti. Pojmenujte například dvě spravované instance:

- `sql-mi-pub`(spolu s některými znaky pro randomizaci)
- `sql-mi-sub`(spolu s některými znaky pro randomizaci)

Budete také muset [nakonfigurovat virtuální počítač Azure pro připojení k](sql-database-managed-instance-configure-vm.md) vašim instancím spravované hodu Azure SQL Database. 

## <a name="3---create-azure-storage-account"></a>3 – Vytvoření účtu úložiště Azure

[Vytvořte účet úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) pro pracovní adresář a pak [vytvořte sdílenou složku](../storage/files/storage-how-to-create-file-share.md) v rámci účtu úložiště. 

Zkopírujte cestu ke sdílení souborů ve formátu:`\\storage-account-name.file.core.windows.net\file-share-name`

Příklad: `\\replstorage.file.core.windows.net\replshare`

Zkopírujte přístupové klíče úložiště ve formátu:`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Příklad: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Další informace naleznete v [tématu Správa přístupových klíčů účtu úložiště](../storage/common/storage-account-keys-manage.md). 

## <a name="4---create-a-publisher-database"></a>4 - Vytvoření databáze vydavatele

Připojte se `sql-mi-pub` ke spravované instanci pomocí sql server management studio a spusťte následující Kód Transact-SQL (T-SQL) k vytvoření databáze vydavatele:

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

## <a name="5---create-a-subscriber-database"></a>5 - Vytvoření databáze odběratelů

Připojte se `sql-mi-sub` ke spravované instanci pomocí sql server management studio a spusťte následující T-SQL kód k vytvoření prázdné databáze odběratele:

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

## <a name="6---configure-distribution"></a>6 - Konfigurace distribuce

Připojte se `sql-mi-pub` ke spravované instanci pomocí sql server management studio a spusťte následující T-SQL kód pro konfiguraci distribuční databáze. 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 - Konfigurace vydavatele pro použití distributora 

Ve spravované instanci `sql-mi-pub`vydavatele změňte spuštění dotazu na režim [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) a spusťte následující kód a zaregistrujte nového distributora u vydavatele. 

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
-- example: file_storage "\\replstorage.file.core.windows.net\replshare"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"
-- example: file_storage_key "DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net"

USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)'; -- Remove this parameter for on-premises publishers
```

   > [!NOTE]
   > Ujistěte se, že pro`\`parametr file_storage používáte pouze zpětná lomítka ( ). Použití lomítka (`/`) může způsobit chybu při připojování ke sdílené složce. 

Tento skript konfiguruje místního vydavatele na spravované instanci, přidá propojený server a vytvoří sadu úloh pro agenta serveru SQL Server. 

## <a name="8---create-publication-and-subscriber"></a>8 - Vytvoření publikace a odběratele

Pomocí režimu [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) spusťte následující skript T-SQL, který povolí replikaci databáze, a nakonfigurujte replikaci mezi vydavatelem, distributorem a odběratelem. 

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
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9 - Úprava parametrů agenta

Instance spravované službou Azure SQL Database aktuálně dochází k některým problémům s back-endem s připojením k agentům replikace. Zatímco tento problém je vyřešen, řešení pro zvýšení hodnoty časového limitu přihlášení pro agenty replikace. 

Spusťte následující příkaz T-SQL v vydavateli a prodlužte časový limit přihlášení: 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Spusťte znovu následující příkaz T-SQL a nastavte časový limit přihlášení zpět na výchozí hodnotu, pokud to budete muset udělat:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Restartujte všechny tři agenty, abyste tyto změny použili. 

## <a name="10---test-replication"></a>10 - Testovací replikace

Jakmile replikace byla nakonfigurována, můžete otestovat vložením nové položky na vydavatele a sledování změny šířit odběratele. 

Spusťte následující fragment T-SQL, chcete-li zobrazit řádky na odběrateli:

```sql
select * from dbo.ReplTest
```

Spusťte následující fragment T-SQL, chcete-li vložit další řádky do vydavatele, a potom řádky znovu zkontrolujte u odběratele. 

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li publikaci přetáhnout, spusťte následující příkaz T-SQL:

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

Chcete-li odebrat možnost replikace z databáze, spusťte následující příkaz T-SQL:

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

Chcete-li zakázat publikování a distribuci, spusťte následující příkaz T-SQL:

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

Prostředky Azure můžete vyčistit [odstraněním prostředků spravované instance ze skupiny prostředků](../azure-resource-manager/management/manage-resources-portal.md#delete-resources) a následným odstraněním skupiny `SQLMI-Repl`prostředků . 

   
## <a name="see-also"></a>Viz také

- [Transakční replikace](sql-database-managed-instance-transactional-replication.md)
- [Kurz: Konfigurace transakční replikace mezi vydavatelem MI a odběratelem serveru SQL Server](sql-database-managed-instance-configure-replication-tutorial.md)
- [Co je spravovaná instance?](sql-database-managed-instance.md)

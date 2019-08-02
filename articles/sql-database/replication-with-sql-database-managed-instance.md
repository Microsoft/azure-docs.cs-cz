---
title: Konfigurace replikace v databázi spravované instance Azure SQL Database | Microsoft Docs
description: Přečtěte si o konfiguraci transakční replikace v databázi spravované instance Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
ms.date: 02/07/2019
ms.openlocfilehash: 96571b55158733f8189ddf87402fb634330ca80d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570460"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Konfigurace replikace v databázi spravované instance Azure SQL Database

Transakční replikace umožňuje replikovat data do databáze spravované instance Azure SQL Database z databáze SQL Server nebo jiné instance databáze. 

Transakční replikaci můžete použít taky k nahrání změn provedených v databázi instance v Azure SQL Database Managed instance:

- Databáze SQL Server.
- Jedna databáze v Azure SQL Database.
- Databáze ve fondu Azure SQL Database elastického fondu.
 
Transakční replikace je ve verzi Public Preview na [Azure SQL Database Managed instance](sql-database-managed-instance.md). Spravovaná instance může hostovat databáze vydavatelů, distributorů a předplatitelů. V tématu [Konfigurace transakční replikace](sql-database-managed-instance-transactional-replication.md#common-configurations) pro dostupné konfigurace.

  > [!NOTE]
  > Tento článek slouží jako vodítko pro uživatele při konfiguraci replikace s Azure Database Managed instance z koncového na konec, počínaje vytvořením skupiny prostředků. Pokud jste již nasadili spravované instance, přejděte k [kroku 4](#4---create-a-publisher-database) a vytvořte databázi vydavatele, nebo [Krok 6](#6---configure-distribution) , pokud již máte vydavatele a databázi odběratele a jste připraveni zahájit konfiguraci replikace.  

## <a name="requirements"></a>Požadavky

Konfigurace spravované instance tak, aby fungovala jako Vydavatel nebo distributor, vyžaduje:

- Tato spravovaná instance se momentálně neúčastní vztahu geografické replikace.
- Zda je spravovaná instance vydavatele ve stejné virtuální síti jako distributor a předplatitel, nebo je mezi virtuálními sítěmi všech tří entit vytvořeno [partnerský vztah](../virtual-network/tutorial-connect-virtual-networks-powershell.md) virtuální sítě. 
- Při připojování mezi účastníky replikace se používá ověřování SQL.
- Sdílená složka účtu Azure Storage pro pracovní adresář replikace.
- Port 445 (odchozí TCP) je otevřen v pravidlech zabezpečení NSG pro spravované instance pro přístup ke sdílené složce Azure. 


 > [!NOTE]
 > Izolované databáze a databáze ve fondu v Azure SQL Database můžou být jenom předplatitelé. 


## <a name="features"></a>Funkce

Podporuje:

- Transakční a snímková replikace je kombinací SQL Server místních a spravovaných instancí v Azure SQL Database.
- Předplatitelé můžou být v místních SQL Server databázích, izolovaných databázích nebo spravovaných instancích v Azure SQL Database nebo ve fondu databází v Azure SQL Database elastických fondech.
- Jednosměrná nebo obousměrná replikace.

Ve spravované instanci nejsou v Azure SQL Database podporovány následující funkce:

- Odběry, které je možné [aktualizovat](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication)
- Pokud je nakonfigurovaná transakční replikace, neměli byste používat [aktivní geografickou replikaci](sql-database-active-geo-replication.md) a [skupiny s automatickým převzetím služeb při selhání](sql-database-auto-failover-group.md) .
 
## <a name="1---create-a-resource-group"></a>1\. Vytvoření skupiny prostředků

Pomocí [Azure Portal](https://portal.azure.com) vytvořte skupinu prostředků s názvem `SQLMI-Repl`.  

## <a name="2---create-managed-instances"></a>2\. vytvoření spravovaných instancí

Pomocí [Azure Portal](https://portal.azure.com) vytvořte dvě [spravované instance](sql-database-managed-instance-create-tutorial-portal.md) ve stejné virtuální síti a podsíti. Tyto dvě spravované instance by měly mít název:

- `sql-mi-pub`
- `sql-mi-sub`

Také budete muset [nakonfigurovat virtuální počítač Azure pro připojení](sql-database-managed-instance-configure-vm.md) k vašemu Azure SQL Database spravované instance. 

## <a name="3---create-azure-storage-account"></a>3\. vytvoření účtu Azure Storage

[Vytvořte účet Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) pro pracovní adresář a pak vytvořte [sdílenou složku](../storage/files/storage-how-to-create-file-share.md) v rámci účtu úložiště. 

Zkopírujte cestu ke sdílené složce ve formátu:`\\storage-account-name.file.core.windows.net\file-share-name`

Zkopírujte přístupové klíče úložiště ve formátu:`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

 Další informace najdete v článku [Zobrazení a kopírování přístupového klíče k úložišti](../storage/common/storage-account-manage.md#access-keys). 

## <a name="4---create-a-publisher-database"></a>4\. vytvoření databáze vydavatele

Připojte se ke `sql-mi-pub` svojí spravované instanci pomocí SQL Server Management Studio a spusťte následující kód Transact-SQL (T-SQL) k vytvoření databáze vydavatele:

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

## <a name="5---create-a-subscriber-database"></a>5\. vytvoření databáze odběratele

Připojte se ke `sql-mi-sub` svojí spravované instanci pomocí SQL Server Management Studio a spusťte následující kód T-SQL k vytvoření prázdné databáze odběratele:

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

## <a name="6---configure-distribution"></a>6\. konfigurace distribuce

Připojte se ke `sql-mi-pub` svojí spravované instanci pomocí SQL Server Management Studio a spusťte následující kód T-SQL ke konfiguraci distribuční databáze. 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7\. konfigurace vydavatele pro použití distributora 

Ve spravované instanci `sql-mi-pub`vydavatele změňte provedení dotazu na režim [Sqlcmd](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) a spuštěním následujícího kódu Zaregistrujte nového distributora s vaším vydavatelem. 

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
  @storage_connection_string = N'$(file_storage_key)'; -- Remove this parameter for on-premises publishers
```

Tento skript nakonfiguruje místního vydavatele na spravované instanci, přidá propojený server a vytvoří sadu úloh pro agenta SQL Server. 

## <a name="8---create-publication-and-subscriber"></a>8\. vytvoření publikace a odběratele

Pomocí režimu [Sqlcmd](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) spusťte následující skript T-SQL, který povolí replikaci pro vaši databázi, a nakonfigurujte replikaci mezi vaším vydavatelem, distributorem a předplatitelem. 

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

## <a name="9---modify-agent-parameters"></a>9\. úprava parametrů agenta

U Azure SQL Database Managed instance v tuto chvíli dochází k problémům s back-endu s replikačními agenty. I když se tento problém řeší, alternativní řešení pro zvýšení hodnoty časového limitu přihlášení pro agenty replikace. 

Pokud chcete prodloužit časový limit přihlášení, spusťte následující příkaz T-SQL na vydavateli: 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Znovu spusťte následující příkaz T-SQL, abyste nastavili časový limit přihlášení zpátky na výchozí hodnotu, třeba v tomto případě:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Aby se tyto změny projevily, restartujte všechny tři agenty. 

## <a name="10---test-replication"></a>10. testování replikace

Po dokončení konfigurace replikace ji můžete otestovat vložením nových položek na vydavatele a sledováním změn, které se šíří do odběratele. 

Spusťte následující fragment T-SQL pro zobrazení řádků v předplatiteli:

```sql
select * from dbo.ReplTest
```

Spusťte následující fragment T-SQL pro vložení dalších řádků na vydavateli a potom znovu zkontrolujte řádky předplatitele. 

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

K vyřazení publikace spusťte následující příkaz T-SQL:

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

Pokud chcete z databáze odebrat možnost replikace, spusťte následující příkaz T-SQL:

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

Pokud chcete zakázat publikování a distribuci, spusťte následující příkaz T-SQL:

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

Prostředky Azure můžete vyčistit [odstraněním prostředků spravované instance ze skupiny prostředků](../azure-resource-manager/manage-resources-portal.md#delete-resources) a následným odstraněním skupiny `SQLMI-Repl`prostředků. 

   
## <a name="see-also"></a>Viz také

- [Transakční replikace](sql-database-managed-instance-transactional-replication.md)
- [Co je spravovaná instance?](sql-database-managed-instance.md)

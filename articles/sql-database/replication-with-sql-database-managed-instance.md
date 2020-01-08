---
title: Konfigurace replikace v databázi spravované instance
description: Naučte se konfigurovat transakční replikaci mezi Azure SQL Database vydavatele/distributora spravované instance a odběratelem spravované instance.
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
ms.openlocfilehash: fd881142e0260d313e197d5e40ae25a2621646df
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75372464"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Konfigurace replikace v databázi spravované instance Azure SQL Database

Transakční replikace umožňuje replikovat data do databáze spravované instance Azure SQL Database z databáze SQL Server nebo jiné instance databáze. 

V tomto článku se dozvíte, jak nakonfigurovat replikaci mezi vydavatelem nebo distributorem spravované instance a předplatitelem spravované instance. 

![Replikace mezi dvěma spravovanými instancemi](media/replication-with-sql-database-managed-instance/sqlmi-sqlmi-repl.png)

Transakční replikaci můžete použít taky k nahrání změn provedených v databázi instance v Azure SQL Database Managed instance:

- Databáze SQL Server.
- Jedna databáze v Azure SQL Database.
- Databáze ve fondu Azure SQL Database elastického fondu.
 
Transakční replikace je ve verzi Public Preview na [Azure SQL Database Managed instance](sql-database-managed-instance.md). Spravovaná instance může hostovat databáze vydavatelů, distributorů a předplatitelů. V tématu [Konfigurace transakční replikace](sql-database-managed-instance-transactional-replication.md#common-configurations) pro dostupné konfigurace.

  > [!NOTE]
  > - Tento článek slouží jako vodítko pro uživatele při konfiguraci replikace s Azure Database Managed instance z koncového na konec, počínaje vytvořením skupiny prostředků. Pokud jste již nasadili spravované instance, přejděte k [kroku 4](#4---create-a-publisher-database) a vytvořte databázi vydavatele, nebo [Krok 6](#6---configure-distribution) , pokud již máte vydavatele a databázi odběratele a jste připraveni zahájit konfiguraci replikace.  
  > - V tomto článku se nakonfiguruje váš Vydavatel a distributor na stejné spravované instanci. Pokud chcete distributora umístit na samostatnou instanci spravovaných, přečtěte si kurz [Konfigurace replikace mezi vydavatelem v mi a distributorem mi](sql-database-managed-instance-configure-replication-tutorial.md). 

## <a name="requirements"></a>Požadavky

Konfigurace spravované instance tak, aby fungovala jako Vydavatel nebo distributor, vyžaduje:

- Tato spravovaná instance se momentálně neúčastní vztahu geografické replikace.
- Zda je spravovaná instance vydavatele ve stejné virtuální síti jako distributor a předplatitel, nebo je mezi virtuálními sítěmi všech tří entit vytvořeno [partnerský vztah](../virtual-network/tutorial-connect-virtual-networks-powershell.md) virtuální sítě. 
- Při připojování mezi účastníky replikace se používá ověřování SQL.
- Sdílená složka účtu Azure Storage pro pracovní adresář replikace.
- Port 445 (odchozí TCP) je otevřen v pravidlech zabezpečení NSG pro spravované instance pro přístup ke sdílené složce Azure.  Pokud narazíte na chybu "připojení k Azure Storage \<název účtu úložiště > s chybou operačního systému 53", budete muset přidat odchozí pravidlo do NSG příslušné podsítě spravované instance SQL.


 > [!NOTE]
 > Izolované databáze a databáze ve fondu v Azure SQL Database můžou být jenom předplatitelé. 


## <a name="features"></a>Funkce

Podporovaných

- Transakční a snímková replikace je kombinací SQL Server místních a spravovaných instancí v Azure SQL Database.
- Předplatitelé můžou být v místních SQL Server databázích, izolovaných databázích nebo spravovaných instancích v Azure SQL Database nebo ve fondu databází v Azure SQL Database elastických fondech.
- Jednosměrná nebo obousměrná replikace.

Ve spravované instanci nejsou v Azure SQL Database podporovány následující funkce:

- [Odběry](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication), které je možné aktualizovat
- [Aktivní geografická replikace](sql-database-active-geo-replication.md) s transakční replikací Místo aktivní geografické replikace používejte [skupiny s automatickým převzetím služeb při selhání](sql-database-auto-failover-group.md), ale Všimněte si, že po převzetí služeb při selhání se publikace musí [ručně odstranit](sql-database-managed-instance-transact-sql-information.md#replication) z primární spravované instance a znovu vytvořit v sekundární spravované instanci.  
 
## <a name="1---create-a-resource-group"></a>1\. Vytvoření skupiny prostředků

Pomocí [Azure Portal](https://portal.azure.com) vytvořte skupinu prostředků s názvem `SQLMI-Repl`.  

## <a name="2---create-managed-instances"></a>2\. vytvoření spravovaných instancí

Pomocí [Azure Portal](https://portal.azure.com) vytvořte dvě [spravované instance](sql-database-managed-instance-create-tutorial-portal.md) ve stejné virtuální síti a podsíti. Například pojmenujte dvě spravované instance:

- `sql-mi-pub` (spolu s některými znaky pro náhodnost)
- `sql-mi-sub` (spolu s některými znaky pro náhodnost)

Také budete muset [nakonfigurovat virtuální počítač Azure pro připojení](sql-database-managed-instance-configure-vm.md) k vašemu Azure SQL Database spravované instance. 

## <a name="3---create-azure-storage-account"></a>3\. vytvoření účtu Azure Storage

[Vytvořte účet Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) pro pracovní adresář a pak vytvořte [sdílenou složku](../storage/files/storage-how-to-create-file-share.md) v rámci účtu úložiště. 

Zkopírujte cestu ke sdílené složce ve formátu: `\\storage-account-name.file.core.windows.net\file-share-name`

Příklad: `\\replstorage.file.core.windows.net\replshare`

Zkopírujte přístupové klíče úložiště ve formátu: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Příklad: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Další informace najdete v tématu [Správa přístupových klíčů účtu úložiště](../storage/common/storage-account-keys-manage.md). 

## <a name="4---create-a-publisher-database"></a>4\. vytvoření databáze vydavatele

Připojte se k vaší `sql-mi-pub` spravované instanci pomocí SQL Server Management Studio a spusťte následující kód jazyka Transact-SQL (T-SQL) pro vytvoření databáze vydavatele:

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

Připojte se k vaší `sql-mi-sub` spravované instanci pomocí SQL Server Management Studio a spusťte následující kód T-SQL k vytvoření prázdné databáze odběratele:

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

Připojte se k vaší `sql-mi-pub` spravované instanci pomocí SQL Server Management Studio a spusťte následující kód T-SQL ke konfiguraci distribuční databáze. 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7\. konfigurace vydavatele pro použití distributora 

Na spravované instanci vydavatele `sql-mi-pub`změňte provádění dotazů na režim [Sqlcmd](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) a spusťte následující kód, který zaregistruje nového distributora s vaším vydavatelem. 

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
   > Nezapomeňte pro parametr file_storage používat jenom zpětná lomítka (`\`). Použití lomítka (`/`) může při připojování ke sdílené složce způsobit chybu. 

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

Prostředky Azure můžete vyčistit [odstraněním prostředků spravované instance ze skupiny prostředků](../azure-resource-manager/management/manage-resources-portal.md#delete-resources) a následným odstraněním `SQLMI-Repl`skupiny prostředků. 

   
## <a name="see-also"></a>Viz také

- [Transakční replikace](sql-database-managed-instance-transactional-replication.md)
- [Kurz: Konfigurace transakční replikace mezi vydavatelem v MI a předplatitelem SQL Server](sql-database-managed-instance-configure-replication-tutorial.md)
- [Co je spravovaná instance?](sql-database-managed-instance.md)

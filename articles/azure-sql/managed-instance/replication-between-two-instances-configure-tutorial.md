---
title: Konfigurace replikace mezi spravovanými instancemi
titleSuffix: Azure SQL Managed Instance
description: V tomto kurzu se naučíte konfigurovat transakční replikaci mezi vydavatelem/distributorem spravované instance Azure SQL a předplatitelem spravované instance SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 67902073b1484835d23566c91cbfae6f1d80d249
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900458"
---
# <a name="tutorial-configure-replication-between-two-managed-instances"></a>Kurz: Konfigurace replikace mezi dvěma spravovanými instancemi

[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Transakční replikace umožňuje replikovat data z jedné databáze do jiné, jejímž hostitelem je SQL Server nebo [Azure SQL Managed instance](sql-managed-instance-paas-overview.md). Spravovaná instance SQL může být Vydavatel, distributor nebo předplatitel v topologii replikace. V tématu [Konfigurace transakční replikace](replication-transactional-overview.md#common-configurations) pro dostupné konfigurace. 

Transakční replikace je aktuálně ve verzi Public Preview pro spravovanou instanci SQL. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> - Konfigurace spravované instance jako vydavatele a distributora replikace
> - Nakonfigurujte spravovanou instanci jako předplatitele replikace.

![Replikace mezi dvěma spravovanými instancemi](./media/replication-between-two-instances-configure-tutorial/sqlmi-sqlmi-repl.png)

Tento kurz je určený pro zkušené publikum a předpokládá, že uživatel je obeznámen s nasazením a připojením ke spravovaným instancím a SQL Server virtuálním počítačům v rámci Azure. 


> [!NOTE]
> - Tento článek popisuje použití [transakční replikace](/sql/relational-databases/replication/transactional/transactional-replication) ve spravované instanci Azure SQL. Nesouvisí se [skupinami převzetí služeb při selhání](../database/auto-failover-group-overview.md), což je funkce spravované instance Azure SQL, která umožňuje vytvářet kompletní čitelné repliky jednotlivých instancí. Při konfiguraci [replikace transakcí se skupinami převzetí služeb při selhání](replication-transactional-overview.md#with-failover-groups)jsou k dispozici další předpoklady.



## <a name="requirements"></a>Požadavky

Konfigurace spravované instance SQL tak, aby fungovala jako Vydavatel nebo distributor, vyžaduje:

- Tato spravovaná instance vydavatele se nachází ve stejné virtuální síti jako distributor a předplatitel, nebo [VPN Gateway](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) byly nakonfigurovány mezi virtuálními sítěmi všech tří entit. 
- Při připojování mezi účastníky replikace se používá ověřování SQL.
- Sdílená složka účtu úložiště Azure pro pracovní adresář replikace.
- Port 445 (odchozí TCP) je otevřen v pravidlech zabezpečení NSG pro spravované instance pro přístup ke sdílené složce Azure.  Pokud dojde k chybě `failed to connect to azure storage \<storage account name> with os error 53` , bude nutné přidat odchozí pravidlo do NSG příslušné podsítě spravované instance SQL.

## <a name="1---create-a-resource-group"></a>1. Vytvoření skupiny prostředků

Pomocí [Azure Portal](https://portal.azure.com) vytvořte skupinu prostředků s názvem `SQLMI-Repl` .  

## <a name="2---create-managed-instances"></a>2. vytvoření spravovaných instancí

Pomocí [Azure Portal](https://portal.azure.com) vytvořte dvě [spravované instance SQL](instance-create-quickstart.md) ve stejné virtuální síti a podsíti. Například pojmenujte dvě spravované instance:

- `sql-mi-pub` (spolu s některými znaky pro náhodnost)
- `sql-mi-sub` (spolu s některými znaky pro náhodnost)

Pro připojení ke spravovaným instancím budete taky muset [nakonfigurovat virtuální počítač Azure](connect-vm-instance-configure.md) . 

## <a name="3---create-an-azure-storage-account"></a>3. vytvoření účtu služby Azure Storage

[Vytvořte účet úložiště Azure](../../storage/common/storage-account-create.md#create-a-storage-account) pro pracovní adresář a pak vytvořte [sdílenou složku](../../storage/files/storage-how-to-create-file-share.md) v rámci účtu úložiště. 

Zkopírujte cestu ke sdílené složce ve formátu: `\\storage-account-name.file.core.windows.net\file-share-name`

Příklad: `\\replstorage.file.core.windows.net\replshare`

Zkopírujte přístupové klíče úložiště ve formátu: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Příklad: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Další informace najdete v tématu [Správa přístupových klíčů účtu úložiště](../../storage/common/storage-account-keys-manage.md). 

## <a name="4---create-a-publisher-database"></a>4. vytvoření databáze vydavatele

Připojte se ke svojí `sql-mi-pub` spravované instanci pomocí SQL Server Management Studio a spusťte následující kód Transact-SQL (T-SQL) k vytvoření databáze vydavatele:

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

## <a name="5---create-a-subscriber-database"></a>5. vytvoření databáze odběratele

Připojte se ke svojí `sql-mi-sub` spravované instanci pomocí SQL Server Management Studio a spusťte následující kód T-SQL k vytvoření prázdné databáze odběratele:

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

## <a name="6---configure-distribution"></a>6. konfigurace distribuce

Připojte se ke svojí `sql-mi-pub` spravované instanci pomocí SQL Server Management Studio a spusťte následující kód T-SQL ke konfiguraci distribuční databáze.

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7. konfigurace vydavatele pro použití distributora

Na spravované instanci aplikace Publisher SQL `sql-mi-pub` změňte provedení dotazu na režim [Sqlcmd](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) a spuštěním následujícího kódu Zaregistrujte nového distributora s vaším vydavatelem.

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
   > Nezapomeňte pro parametr file_storage použít jenom zpětná lomítka ( `\` ). Použití lomítka ( `/` ) může při připojování ke sdílené složce způsobit chybu.

Tento skript nakonfiguruje místního vydavatele na spravované instanci, přidá propojený server a vytvoří sadu úloh pro agenta SQL Server.

## <a name="8---create-publication-and-subscriber"></a>8. vytvoření publikace a odběratele

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


-- Configure your log reader agent
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

## <a name="9---modify-agent-parameters"></a>9. úprava parametrů agenta

V této službě Azure SQL Managed instance v tuto chvíli dochází k problémům s back-endu s agenty replikace. I když se tento problém řeší, je alternativním řešením zvýšit hodnotu časového limitu přihlášení pro agenty replikace.

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

Prostředky Azure můžete vyčistit [odstraněním prostředků spravované instance SQL ze skupiny prostředků](../../azure-resource-manager/management/manage-resources-portal.md#delete-resources) a následným odstraněním skupiny prostředků `SQLMI-Repl` . 

## <a name="next-steps"></a>Další kroky

Můžete si taky přečíst další informace o [transakční replikaci pomocí spravované instance Azure SQL](replication-transactional-overview.md) nebo se seznámit s konfigurací replikace mezi [vydavatelem nebo distributorem spravované instance SQL a SQL na předplatiteli virtuálních počítačů Azure](replication-two-instances-and-sql-server-configure-tutorial.md).